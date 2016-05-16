---
layout: default
group: config-guide
subgroup: 20_cqrs
title: Manually configure master databases
menu_title: Manually configure master databases
menu_order: 3
menu_node: 
github_link: config-guide/multi-master/multi-master_manual.md
---

<img src="{{ site.baseurl }}common/images/ee-only_large.png">

#### Contents
*	[Overview of manual split database configuration](#config-ee-multidb-manual-over)
*   [Back up the Magento system](#config-ee-multidb-backup)
*	[Set up additional master databases](#config-ee-multidb-master-masters)
*	[Run quote database SQL scripts](#config-ee-multidb-oms)
*	[Configure the sales database](#config-ee-multidb-checkout)
*   [Verify your configuration](#config-ee-multidb-config)

## Overview of manual split database configuration {#config-ee-multidb-manual-over}
If the Magento application is already in production or if you've already installed custom code or components, you might need to configure split databases manually. 

Before continuing, contact Magento Support to see if this is necessary in your case.

Manually splitting databases involves:

*	Create the checkout and order management system (OMS) databases
*	Run a series of SQL scripts that:

	*	Drop foreign keys
	*	Move tables from your main Magento database to the sales and quote databases

<div class="bs-callout bs-callout-warning">
    <p>If any custom code uses JOINs, you <em>cannot</em> use split databases. If in doubt, contact the authors of any custom code or extensions to make sure their code does not use JOINs.</p>
</div>

This topic uses the following naming conventions:

*	The main Magento database name is `magento` and its user name and password are both `magento`
*	The sales database name is `magento_checkout` and its user name and password are both `magento_checkout`

    The sales database is also referred to as the *checkout* database.
*	The quote database name is `magento_oms` and its user name and password are both `magento_oms`

    The quote database is also referred to as the order management system (*OMS*) database.

<div class="bs-callout bs-callout-info" id="info">
<span class="glyphicon-class">
  <p>This guide assumes all three databases are on the same host as the Magento application. However, the choice of where to locate the databases and what they're named is up to you. We hope our examples make the instructions easier to follow.</p></span>
</div>

## Back up the Magento system {#config-ee-multidb-backup}
We strongly recommend you back up your current database and file system so you can restore it later in the event of issues during the process.

To back up your system:

1.	Log in to your Magento server as, or switch to, the [Magento file system owner]({{ site.gdeurl }}install-gde/prereq/apache-user.html).
2.	Enter the following commands:

		magento setup:backup --code --media --db
3.	Continue with the next section.

## Set up additional master databases {#config-ee-multidb-master-masters}
Create checkout and OMS master databases as follows:

1.  Log in to your database server as any user.
2.  Enter the following command to get to a MySQL command prompt:

        mysql -u root -p

3.  Enter the MySQL `root` user's password when prompted.
4.  Enter the following commands in the order shown to create database instances named `magento_checkout` and `magento_oms` with the same user names and passwords:

        create database magento_checkout;
        GRANT ALL ON magento_checkout.* TO magento_checkout@localhost IDENTIFIED BY 'magento_checkout';

        create database magento_oms;
        GRANT ALL ON magento_oms.* TO magento_oms@localhost IDENTIFIED BY 'magento_oms';

5.  Enter `exit` to quit the command prompt.

6.  Verify the databases, one at a time:

    Checkout database:

        mysql -u magento_checkout -p
        exit

    Order management database:

        mysql -u magento_oms -p
        exit

    If the MySQL monitor displays, you created the database properly. If an error displays, repeat the preceding commands.
7.  Continue with the next section.

## Run quote database SQL scripts {#config-ee-multidb-oms}
This section discusses how to create and run SQL scripts that alter quote database tables and back up data from those tables.

For more information, see:

*	[Create quote database SQL scripts](#config-ee-multidb-sql-oms)
*	[Run SQL scripts](#config-ee-multidb-sql-run)
*   [Back up sales data](#sales-backup)

### Create quote database SQL scripts {#config-ee-multidb-sql-oms}
Create the following SQL scripts in a location that is accessible by the user as whom you log in to your Magento server. For example, if you log in or run commands as `root`, you can create the scripts in the `/root/sql-scripts` directory.

#### Remove foreign keys (first script)
This script is the first of two that remove foreign keys that refer to non-sales tables from sales tables. 

Create the following script and give it a name like `1_foreign1.sql`. Replace `<your main magento DB name>` with the name of your Magento database. In this topic, the sample database name is `magento`.

{% highlight sql %}
select concat(
    'ALTER TABLE ',
    replace(for_name, '<your main magento DB name>/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, '<your main magento DB name>/', ''),
    ';'
    )
from information_schema.INNODB_SYS_FOREIGN
where for_name like  '<your main magento DB name>/|sales_%' escape '|'
    and ref_name not like  '<your main magento DB name>/|sales_%' escape '|'
union all
select concat(
    'ALTER TABLE ',
    replace(for_name, '<your main magento DB name>/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, '<your main magento DB name>/', ''),
    ';'
    )
from information_schema.INNODB_SYS_FOREIGN
where for_name like  '<your main magento DB name>/|magento_sales|_%' escape '|'
    and ref_name not like  '<your main magento DB name>/|sales|_%' escape '|'
;
{% endhighlight %}

#### Remove foreign keys (second script)
This script is the second of two that remove foreign keys that refer to non-sales tables from sales tables. Replace `<your main magento DB name>` with the name of your Magento database.

Create the following script and give it a name like `2_foreign-key2.sql`:

{% highlight sql %}
select concat(
    'ALTER TABLE ',
    replace(for_name, '<your main magento DB name>/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, '<your main magento DB name>/', ''),
    ';'
    )
from information_schema.INNODB_SYS_FOREIGN
where for_name like '<your main magento DB name>/%'
    and ref_name like '<your main magento DB name>/sales\_%'
union all
select concat(
    'ALTER TABLE ',
    replace(for_name, '<your main magento DB name>/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, '<your main magento DB name>/', ''),
    ';'
    )
from information_schema.INNODB_SYS_FOREIGN
where for_name like '<your main magento DB name>/%'
    and ref_name like '<your main magento DB name>/magento_sales\_%'
union all
select concat(
    'ALTER TABLE ',
    replace(for_name, '<your main magento DB name>/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, '<your main magento DB name>/', ''),
    ';'
    )
from information_schema.INNODB_SYS_FOREIGN
where for_name like '<your main magento DB name>/%'
    and ref_name like '<your main magento DB name>/magento_customercustomattributes\_%'
;
{% endhighlight %}

#### Remove quote tables
This script removes quote tables from the main Magento database. 

Create the following script and give it a name like `3_remove-tables.sql`. Replace `<your main magento DB name>` with the name of your Magento database. In this topic, the sample database name is `magento`.

{% highlight sql %}
select ' SET foreign_key_checks = 0;' as querytext
union all
select
    concat('DROP TABLE IF EXISTS ' , table_name, ';')
from information_schema.tables
where table_schema = '<your main magento db name>'
and table_name like 'sales/_%' escape '/'
union all
select
    concat('DROP TABLE IF EXISTS ' , table_name, ';')
from information_schema.tables
where table_schema = '<your main magento db name>'
and table_name like 'magento_sales/_%' escape '/'
union all
select
    concat('DROP TABLE IF EXISTS ' , table_name, ';')
from information_schema.tables
where table_schema = '<your main magento db name>'
and table_name like 'magento_customercustomattributes_sales_flat_order%' escape '/'
union all
select
    concat('DROP TABLE IF EXISTS ' , table_name, ';')
from information_schema.tables
where table_schema = '<your main magento db name>'
and table_name like 'sequence/_%' escape '/'
union all
select 'SET foreign_key_checks = 1;';
{% endhighlight %}

### Run quote database SQL scripts {#config-ee-multidb-sql-oms-run}
Run each script in the order in which you created it as follows:

1.  Log in to your MySQL database as the `root` or administrative user:

        mysql -u root -p
2.  At the `mysql>` prompt, run each script as follows:

        source <path>/<script>.sql

    For example,

        source /root/sql-scripts/1_foreign1.sql
        source /root/sql-scripts/2_foreign-key2.sql
        source /root/sql-scripts/3_remove-tables.sql

#### Sample script output
Sample output from each script follows in the order we suggest you run them.

**Drop foreign keys (script 1)**
{% highlight sql %}
+---------------------------------------------------------------------------------------------------------------------------------------------+
| concat(
    'ALTER TABLE ',
    replace(for_name, 'magento/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, 'magento/', ''),
    ';'
    ) |
+---------------------------------------------------------------------------------------------------------------------------------------------+
| ALTER TABLE salesrule_coupon_aggregated_order DROP FOREIGN KEY SALESRULE_COUPON_AGGREGATED_ORDER_STORE_ID_STORE_STORE_ID;                   |
| ALTER TABLE salesrule_coupon_aggregated DROP FOREIGN KEY SALESRULE_COUPON_AGGREGATED_STORE_ID_STORE_STORE_ID;                               |
| ALTER TABLE salesrule_coupon_aggregated_updated DROP FOREIGN KEY SALESRULE_COUPON_AGGREGATED_UPDATED_STORE_ID_STORE_STORE_ID;               |
| ALTER TABLE salesrule_coupon_usage DROP FOREIGN KEY SALESRULE_COUPON_USAGE_CUSTOMER_ID_CUSTOMER_ENTITY_ENTITY_ID;                           |
| ALTER TABLE salesrule_customer_group DROP FOREIGN KEY SALESRULE_CSTR_GROUP_CSTR_GROUP_ID_CSTR_GROUP_CSTR_GROUP_ID;                          |
| ALTER TABLE salesrule_customer DROP FOREIGN KEY SALESRULE_CUSTOMER_CUSTOMER_ID_CUSTOMER_ENTITY_ENTITY_ID;                                   |
| ALTER TABLE salesrule_label DROP FOREIGN KEY SALESRULE_LABEL_STORE_ID_STORE_STORE_ID;                                                       |
| ALTER TABLE salesrule_product_attribute DROP FOREIGN KEY SALESRULE_PRD_ATTR_ATTR_ID_EAV_ATTR_ATTR_ID;                                       |
| ALTER TABLE salesrule_product_attribute DROP FOREIGN KEY SALESRULE_PRD_ATTR_CSTR_GROUP_ID_CSTR_GROUP_CSTR_GROUP_ID;                         |
| ALTER TABLE salesrule_product_attribute DROP FOREIGN KEY SALESRULE_PRODUCT_ATTRIBUTE_WEBSITE_ID_STORE_WEBSITE_WEBSITE_ID;                   |
| ALTER TABLE salesrule_website DROP FOREIGN KEY SALESRULE_WEBSITE_WEBSITE_ID_STORE_WEBSITE_WEBSITE_ID;                                       |
+---------------------------------------------------------------------------------------------------------------------------------------------+
{% endhighlight %}

**Remove tables**
{% highlight sql %}
| querytext                                                                       |
+---------------------------------------------------------------------------------+
|  SET foreign_key_checks = 0;                                                    |
| DROP TABLE IF EXISTS sales_bestsellers_aggregated_daily;                        |
| DROP TABLE IF EXISTS sales_bestsellers_aggregated_monthly;                      |
| DROP TABLE IF EXISTS sales_bestsellers_aggregated_yearly;                       |
| DROP TABLE IF EXISTS sales_creditmemo;                                          |
| DROP TABLE IF EXISTS sales_creditmemo_comment;                                  |
| DROP TABLE IF EXISTS sales_creditmemo_grid;                                     |
| DROP TABLE IF EXISTS sales_creditmemo_item;                                     |
| DROP TABLE IF EXISTS sales_invoice;                                             |
| DROP TABLE IF EXISTS sales_invoice_comment;                                     |
| DROP TABLE IF EXISTS sales_invoice_grid;                                        |
| DROP TABLE IF EXISTS sales_invoice_item;                                        |
| DROP TABLE IF EXISTS sales_invoiced_aggregated;                                 |
| DROP TABLE IF EXISTS sales_invoiced_aggregated_order;                           |
| DROP TABLE IF EXISTS sales_order;                                               |
| DROP TABLE IF EXISTS sales_order_address;                                       |
| DROP TABLE IF EXISTS sales_order_aggregated_created;                            |
| DROP TABLE IF EXISTS sales_order_aggregated_updated;                            |
| DROP TABLE IF EXISTS sales_order_grid;                                          |
| DROP TABLE IF EXISTS sales_order_item;                                          |
| DROP TABLE IF EXISTS sales_order_payment;                                       |
| DROP TABLE IF EXISTS sales_order_status;                                        |
| DROP TABLE IF EXISTS sales_order_status_history;                                |
| DROP TABLE IF EXISTS sales_order_status_label;                                  |
| DROP TABLE IF EXISTS sales_order_status_state;                                  |
| DROP TABLE IF EXISTS sales_order_tax;                                           |
| DROP TABLE IF EXISTS sales_order_tax_item;                                      |
| DROP TABLE IF EXISTS sales_payment_transaction;                                 |
| DROP TABLE IF EXISTS sales_refunded_aggregated;                                 |
| DROP TABLE IF EXISTS sales_refunded_aggregated_order;                           |
| DROP TABLE IF EXISTS sales_sequence_meta;                                       |
| DROP TABLE IF EXISTS sales_sequence_profile;                                    |
| DROP TABLE IF EXISTS sales_shipment;                                            |
| DROP TABLE IF EXISTS sales_shipment_comment;                                    |
| DROP TABLE IF EXISTS sales_shipment_grid;                                       |
| DROP TABLE IF EXISTS sales_shipment_item;                                       |
| DROP TABLE IF EXISTS sales_shipment_track;                                      |
| DROP TABLE IF EXISTS sales_shipping_aggregated;                                 |
| DROP TABLE IF EXISTS sales_shipping_aggregated_order;                           |
| DROP TABLE IF EXISTS magento_sales_creditmemo_grid_archive;                     |
| DROP TABLE IF EXISTS magento_sales_invoice_grid_archive;                        |
| DROP TABLE IF EXISTS magento_sales_order_grid_archive;                          |
| DROP TABLE IF EXISTS magento_sales_shipment_grid_archive;                       |
| DROP TABLE IF EXISTS magento_customercustomattributes_sales_flat_order;         |
| DROP TABLE IF EXISTS magento_customercustomattributes_sales_flat_order_address; |
| DROP TABLE IF EXISTS sequence_creditmemo_0;                                     |
| DROP TABLE IF EXISTS sequence_creditmemo_1;                                     |
| DROP TABLE IF EXISTS sequence_invoice_0;                                        |
| DROP TABLE IF EXISTS sequence_invoice_1;                                        |
| DROP TABLE IF EXISTS sequence_order_0;                                          |
| DROP TABLE IF EXISTS sequence_order_1;                                          |
| DROP TABLE IF EXISTS sequence_rma_item_0;                                       |
| DROP TABLE IF EXISTS sequence_rma_item_1;                                       |
| DROP TABLE IF EXISTS sequence_shipment_0;                                       |
| DROP TABLE IF EXISTS sequence_shipment_1;                                       |
| SET foreign_key_checks = 1;                                                     |
+---------------------------------------------------------------------------------+
{% endhighlight %}

**Drop foreign keys (second script)**
{% highlight sql %}
+--------------------------------+
| concat(
    'ALTER TABLE ',
    replace(for_name, 'magento/', ''),
    ' DROP FOREIGN KEY ',
    replace(id, 'magento/', ''),
    ';'
    ) |
+-------------------------------+
| ALTER TABLE downloadable_link_purchased_item DROP FOREIGN KEY DL_LNK_PURCHASED_ITEM_ORDER_ITEM_ID_SALES_ORDER_ITEM_ITEM_ID;                 |
| ALTER TABLE downloadable_link_purchased DROP FOREIGN KEY DOWNLOADABLE_LINK_PURCHASED_ORDER_ID_SALES_ORDER_ENTITY_ID;                        |
| ALTER TABLE magento_customercustomattributes_sales_flat_order_address DROP FOREIGN KEY FK_16AD7FB31D74E6482A0A134C553F0177;                 |
| ALTER TABLE sales_payment_transaction DROP FOREIGN KEY FK_B99FF1A06402D725EBDB0F3A7ECD47A2;                                                 |
| ALTER TABLE magento_customercustomattributes_sales_flat_order DROP FOREIGN KEY FK_E3B93E92D5321FB267FA7CF9818B862A;                         |
| ALTER TABLE paypal_billing_agreement_order DROP FOREIGN KEY PAYPAL_BILLING_AGREEMENT_ORDER_ORDER_ID_SALES_ORDER_ENTITY_ID;                  |
| ALTER TABLE sales_creditmemo_comment DROP FOREIGN KEY SALES_CREDITMEMO_COMMENT_PARENT_ID_SALES_CREDITMEMO_ENTITY_ID;                        |
| ALTER TABLE sales_creditmemo_item DROP FOREIGN KEY SALES_CREDITMEMO_ITEM_PARENT_ID_SALES_CREDITMEMO_ENTITY_ID;                              |
| ALTER TABLE sales_creditmemo DROP FOREIGN KEY SALES_CREDITMEMO_ORDER_ID_SALES_ORDER_ENTITY_ID;                                              |
| ALTER TABLE sales_invoice_comment DROP FOREIGN KEY SALES_INVOICE_COMMENT_PARENT_ID_SALES_INVOICE_ENTITY_ID;                                 |
| ALTER TABLE sales_invoice_item DROP FOREIGN KEY SALES_INVOICE_ITEM_PARENT_ID_SALES_INVOICE_ENTITY_ID;                                       |
| ALTER TABLE sales_invoice DROP FOREIGN KEY SALES_INVOICE_ORDER_ID_SALES_ORDER_ENTITY_ID;                                                    |
| ALTER TABLE sales_order_address DROP FOREIGN KEY SALES_ORDER_ADDRESS_PARENT_ID_SALES_ORDER_ENTITY_ID;                                       |
| ALTER TABLE sales_order_item DROP FOREIGN KEY SALES_ORDER_ITEM_ORDER_ID_SALES_ORDER_ENTITY_ID;                                              |
| ALTER TABLE sales_order_payment DROP FOREIGN KEY SALES_ORDER_PAYMENT_PARENT_ID_SALES_ORDER_ENTITY_ID;                                       |
| ALTER TABLE sales_order_status_history DROP FOREIGN KEY SALES_ORDER_STATUS_HISTORY_PARENT_ID_SALES_ORDER_ENTITY_ID;                         |
| ALTER TABLE sales_order_status_label DROP FOREIGN KEY SALES_ORDER_STATUS_LABEL_STATUS_SALES_ORDER_STATUS_STATUS;                            |
| ALTER TABLE sales_order_status_state DROP FOREIGN KEY SALES_ORDER_STATUS_STATE_STATUS_SALES_ORDER_STATUS_STATUS;                            |
| ALTER TABLE sales_order_tax_item DROP FOREIGN KEY SALES_ORDER_TAX_ITEM_ASSOCIATED_ITEM_ID_SALES_ORDER_ITEM_ITEM_ID;                         |
| ALTER TABLE sales_order_tax_item DROP FOREIGN KEY SALES_ORDER_TAX_ITEM_ITEM_ID_SALES_ORDER_ITEM_ITEM_ID;                                    |
| ALTER TABLE sales_order_tax_item DROP FOREIGN KEY SALES_ORDER_TAX_ITEM_TAX_ID_SALES_ORDER_TAX_TAX_ID;                                       |
| ALTER TABLE sales_payment_transaction DROP FOREIGN KEY SALES_PAYMENT_TRANSACTION_ORDER_ID_SALES_ORDER_ENTITY_ID;                            |
| ALTER TABLE sales_payment_transaction DROP FOREIGN KEY SALES_PAYMENT_TRANSACTION_PAYMENT_ID_SALES_ORDER_PAYMENT_ENTT_ID;                    |
| ALTER TABLE sales_sequence_profile DROP FOREIGN KEY SALES_SEQUENCE_PROFILE_META_ID_SALES_SEQUENCE_META_META_ID;                             |
| ALTER TABLE sales_shipment_comment DROP FOREIGN KEY SALES_SHIPMENT_COMMENT_PARENT_ID_SALES_SHIPMENT_ENTITY_ID;                              |
| ALTER TABLE sales_shipment_item DROP FOREIGN KEY SALES_SHIPMENT_ITEM_PARENT_ID_SALES_SHIPMENT_ENTITY_ID;                                    |
| ALTER TABLE sales_shipment DROP FOREIGN KEY SALES_SHIPMENT_ORDER_ID_SALES_ORDER_ENTITY_ID;                                                  |
| ALTER TABLE sales_shipment_track DROP FOREIGN KEY SALES_SHIPMENT_TRACK_PARENT_ID_SALES_SHIPMENT_ENTITY_ID;                                  |
+------------------------------------+

{% endhighlight %}

### Back up sales data {#sales-backup}
If you're currently at the `mysql>` prompt, enter `exit` to return to the command shell.

Run the following `mysqldump` commands, one at a time, from the command shell. In each, substitute the following:

*   `<your database root user name>` with the name of your database root user
*   `<your database root user password>` with the user's password
*   `<your main magento DB name>` with the name of your Magento database
*   `<path>` with a writable file system path

**Script 1**
{% highlight sql %}
mysqldump -u <your database root user name> -p <your main magento DB name> sales_bestsellers_aggregated_daily sales_bestsellers_aggregated_monthly sales_bestsellers_aggregated_yearly sales_creditmemo sales_creditmemo_comment sales_creditmemo_grid sales_creditmemo_item sales_invoice sales_invoice_comment sales_invoice_grid sales_invoice_item sales_invoiced_aggregated sales_invoiced_aggregated_order sales_order sales_order_address sales_order_aggregated_created sales_order_aggregated_updated sales_order_grid sales_order_item sales_order_payment sales_order_status sales_order_status_history sales_order_status_label sales_order_status_state sales_order_tax sales_order_tax_item sales_payment_transaction sales_refunded_aggregated sales_refunded_aggregated_order sales_sequence_meta sales_sequence_profile sales_shipment sales_shipment_comment sales_shipment_grid sales_shipment_item sales_shipment_track sales_shipping_aggregated sales_shipping_aggregated_order > /<path>/sales.sql
{% endhighlight %}

**Script 2**
{% highlight sql %}
mysqldump -u <your database root user name> -p <your main magento DB name> magento_sales_creditmemo_grid_archive magento_sales_invoice_grid_archive magento_sales_order_grid_archive magento_sales_shipment_grid_archive > /<path>/salesarchive.sql
{% endhighlight %}

**Script 3**
{% highlight sql %}
mysqldump -u <your database root user name> -p <your main magento DB name> magento_customercustomattributes_sales_flat_order magento_customercustomattributes_sales_flat_order_address > /<path>/customercustomattributes.sql
{% endhighlight %}

**Script 4**
{% highlight sql %}
mysqldump -u <your database root user name> -p <your main magento DB name> sequence_creditmemo_0 sequence_creditmemo_1 sequence_invoice_0 sequence_invoice_1 sequence_order_0 sequence_order_1 sequence_rma_item_0 sequence_rma_item_1 sequence_shipment_0 sequence_shipment_1 > /<path>/sequence.sql
{% endhighlight %}


## Configure the sales database {#config-ee-multidb-checkout}
This section discusses tasks required to drop foreign keys from sales database tables and move tables to the sales database.

### Drop foreign keys from checkout tables
Run the following commands from the `mysql>` prompt:

{% highlight SQL %}
use <your Magento main DB name>;
ALTER TABLE quote DROP FOREIGN KEY QUOTE_STORE_ID_STORE_STORE_ID;
ALTER TABLE quote_item DROP FOREIGN KEY QUOTE_ITEM_PRODUCT_ID_CATALOG_PRODUCT_ENTITY_ENTITY_ID;
ALTER TABLE quote_item DROP FOREIGN KEY QUOTE_ITEM_STORE_ID_STORE_STORE_ID;
exit
{% endhighlight %}

### Back up quote tables
Run the following command from a command prompt:

    mysqldump -u <your database root user name> -p <your main Magento DB name> magento_customercustomattributes_sales_flat_quote magento_customercustomattributes_sales_flat_quote_address quote quote_address quote_address_item quote_item quote_item_option quote_payment quote_shipping_rate quote_id_mask > /<path>/quote.sql;

### Import tables to the quote database

    mysqldump -u root -p magento_oms < /<path>/quote.sql

A sample result follows:

{% highlight SQL %}
-- MySQL dump 10.13  Distrib 5.6.28, for Linux (x86_64)
--
-- Host: localhost    Database: magento_oms
-- ------------------------------------------------------
-- Server version       5.6.28

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2016-04-23 13:17:07
{% endhighlight %}

### Drop quote tables from the Magento database
Either run the following commands from the `mysql>` prompt or create a script and run it:

{% highlight sql %}
use <your Magento main DB name>;
SET foreign_key_checks = 0;
DROP TABLE magento_customercustomattributes_sales_flat_quote; 
DROP TABLE magento_customercustomattributes_sales_flat_quote_address;
DROP TABLE quote;
DROP TABLE quote_address;
DROP TABLE quote_address_item;
DROP TABLE quote_item;
DROP TABLE quote_item_option;
DROP TABLE quote_payment;
DROP TABLE quote_shipping_rate;
DROP TABLE quote_id_mask;
SET foreign_key_checks = 1;
{% endhighlight %}

### Restore sales data {#sql-sales-restore}
This script restores sales data in your quote database.

If you are using an NDB database cluster:

1.  Convert tables from InnoDb to NDB type in dump files:

        sed -ei 's/InnoDb/NDB/' <dumpfile>.sql

2.  Remove rows with FULLTEXT KEY from dumps because NDB tables don't support FULLTEXT:

        TBD

    salesarchive.sql

Run the following commands:

{% highlight sql %}
mysqldump -u <root user name> -p <your quote DB name> < /<path>/sales.sql
mysqldump -u <root user name> -p <your quote DB name> < /<path>/sequence.sql
mysqldump -u <root user name> -p <your quote DB name> < /<path>/salesarchive.sql
mysqldump -u <root user name> -p <your quote DB name> < /<path>/customercustomattributes.sql
{% endhighlight %}

where

*   `<your quote DB name>` with the name of your quote database. 

    In this topic, the sample database name is `magento_oms`.
*   `<root user name>` with your MySQL root user name
*   `<root user password>` with the user's password
*   Verify the location of the backup files you created earlier (for example, `/var/sales.sql`)

Sample output from the last command:

{% highlight sql %}
-- Host: localhost    Database: magento_oms
-- ------------------------------------------------------
-- Server version       5.6.28

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2016-04-23 10:42:51
{% endhighlight %}

## Verify your configuration {#config-ee-multidb-config}
This section discusses how to make sure the Magento configuration in `<your Magento install dir>/app/etc/env.php` is correct.

### Verify env.php
The final step in manually splitting databases is to add connection and resource information to Magento's deployment configuration, `env.php`. 


Open `<your Magento install dir>/app/etc/env.php` in a text editor and update it using the guidelines discussed in the following sections.

**Database connections**

Locate the block starting with `'default'` (under `'connection'`) and add `'checkout'` and `'sales'` sections. Replace sample values with values appropriate for your site.

{% highlight php startinline=true %}
 'default' =>
      array (
        'host' => 'localhost',
        'dbname' => 'magento',
        'username' => 'magento',
        'password' => 'magento',
        'model' => 'mysql4',
        'engine' => 'innodb',
        'initStatements' => 'SET NAMES utf8;',
        'active' => '1',
      ),
      'checkout' =>
      array (
        'host' => 'localhost',
        'dbname' => 'magento_checkout',
        'username' => 'magento_checkout',
        'password' => 'magento_checkout',
        'model' => 'mysql4',
        'engine' => 'innodb',
        'initStatements' => 'SET NAMES utf8;',
        'active' => '1',
      ),
      'sales' =>
      array (
        'host' => 'localhost',
        'dbname' => 'magento_oms',
        'username' => 'magento_oms',
        'password' => 'magento_oms',
        'model' => 'mysql4',
        'engine' => 'innodb',
        'initStatements' => 'SET NAMES utf8;',
        'active' => '1',
      ),
{% endhighlight %}

**Resources**

Locate the block starting with `'resource'` and add `'checkout'` and `'sales'` sections to it as follows:

{% highlight php startinline=true %}

'resource' =>
  array (
    'default_setup' =>
    array (
      'connection' => 'default',
    ),
    'checkout' =>
    array (
      'connection' => 'checkout',
    ),
    'sales' =>
    array (
      'connection' => 'sales',
    ),
  ),

{% endhighlight %}


#### Next step
<a href="{{ site.gdeurl }}config-guide/multi-master/multi-master_verify.html">Verify split databases</a>