---
layout: default
group: release-notes
subgroup: Release Notes
title: Magento EE 2.1 Release Candidate-1 Release Notes 
menu_title: Magento EE 2.1 Release Candidate-1 Release Notes 
menu_order: 14
github_link: release-notes/ReleaseNotes2.1_RC1EE.md
---

<h2>Magento Enterprise Edition 2.1 Release Candidate-1</h2>
We are pleased to present Magento 2.1 Release Candidate-1. This release candidate build is not intended for production purposes. Instead, it provides a preview of the new features and fixes that Magento 2.1 GA will contain.

This candidate release also offers the development community an opportunity to contribute to the Magento 2.1 code base by identifying unresolved issues. We welcome your participation in this process on GitHub! For more information on how to provide feedback and contribute on GitHub, see <a href="{{ site.gdeurl }}contributor-guide/contributing.html" target="_blank">Code contributions</a>. 

This Release Candidate is available only on GitHub and repo.magento.com. 

<h3>Highlights</h3>

Magento Enterprise Edition 2.1 includes several new  features:

* **Content Staging and Preview** improves productivity by enabling business teams to easily create, preview, and schedule a wide range of content updates without involving IT. Merchants can make updates to products, categories, CMS content, promotions, and pricing and can preview these changes based on specific dates and times or store views. User-friendly dashboards provide greater visibility into all planned site changes and updates can be automatically deployed at scheduled times.
 
* **Elasticsearch is a next-generation search technology** that is replacing Solr in Magento Enterprise Edition 2.1. It is simpler to set up, able to handle large catalogs, and can easily scale as search volume grows. It supports 33 languages out-of-the-box and merchants can configure stop words and synonyms to ensure high quality search results.

* **PayPal in-context checkout helps to increase conversion rates**.  PayPal in-context checkout helps to increase conversion rates by allowing shoppers to pay with PayPal without leaving the merchant’s site. PayPal saved credit card capabilities allow merchants to securely store credit cards with PayPal so shoppers can make future purchases without re-entering their credit card information.
 
* **Braintree enhancements enable merchants to qualify for the simplest set of PCI compliance** requirements by using Braintree Hosted Fields to collect all sensitive cardholder information in checkout. Merchants retain complete control over their checkout style and layout because Braintree uses small, transparent iframes to replace individual payment fields. Merchants can now also access Braintree settlement reports from within the Magento Admin.
 
* **Improved management interfaces** make it faster and easier to search for information in the Admin, set up global search synonyms, and create new product, category, and CMS content.

 
<h3>Fixed issues</h3>

This release includes fixes for the following GitHub issues:

<!--- 52414 --> * Integration test syntax error has been fixed. <a href="https://github.com/magento/magento2/issues/4343" target="_blank">(GITHUB-4343)</a> 

<!--- 50611--> * Web APIs no longer allow anonymous access (GITHUB-3786)<a href="https://github.com/magento/magento2/issues/3786" target="_blank">(GITHUB-3786)</a>.

<!--- 51292 --> * The OAuth Token exchange expiration period is now calculated correctly. <a href="https://github.com/magento/magento2/issues/3449" target="_blank">(GITHUB-3449)</a> 

<!--- 46720 --> * Shipping Address is now exposed for the Orders API. <a href="https://github.com/magento/magento2/issues/2628" target="_blank">(GITHUB-2628)</a>


<!--- 52613 --> * A Credit Memo REST API issue with updating attributes has been fixed. Previously, certain attributes (such as Order Status) were not updated when the user took action through the API. However, Magento updates these attributes when the same action is completed in the Admin interface. <a href="https://github.com/magento/magento2/issues/4329" target="_blank">(GITHUB-4329)</a>  

<!--- 52607 --> *  Varnish caching performance has been enhanced. <a href="https://github.com/magento/magento2/issues/3926" target="_blank">(GITHUB-3926)</a> 

<!--- 52316 --> *  Product update operations by either customers or store administrators no longer result in locking queries on catalog category product index.<a href="https://github.com/magento/magento2/issues/4342" target="_blank">(GITHUB-4342)</a> 

<!--- 52079 --> * The Order Repository GetList method no longer returns the same shipping address for all orders. <a href="https://github.com/magento/magento2/issues/4019" target="_blank">(GITHUB-4019)</a> 

<!--- 51181 --> * A configurable product's last attribute with price of zero (0) no longer results in an error. The user can configure the product, and the correct price results. <a href="https://github.com/magento/magento2/issues/3912" target="_blank">(GITHUB-3912)</a> 

<!--- 48175 --> * An error message that users typically received during upgrade has been improved. The message now clearly states when a user must login first to `magento.com` before continuing the upgrade process. <a href="https://github.com/magento/magento2/issues/3059" target="_blank">(GITHUB-3059)</a> 

<!--- 47440 --> *  Magento now displays the correct product prices on the Configurable product page when catalog prices include tax. <a href="https://github.com/magento/magento2/issues/2471" target="_blank">(GITHUB-2471)</a> 

<!--- 47439 --> * The `i18n:collect-phrases -m` command now works correctly. Previously, this command would not find all important Magento phrases. <a href="https://github.com/magento/magento2/issues/2630" target="_blank">(GITHUB-26301)</a>

<!--- 47009 --> *  Plugins/interceptors now work with early stage single instance objects in Developer mode. <a href="https://github.com/magento/magento2/issues/2674" target="_blank">(GITHUB-2674)</a> 

<!--- 46808 --> * Admin order creation no longer fails when the "Include Tax In Order Total" option is set to yes. <a href="https://github.com/magento/magento2/issues/2675" target="_blank">(GITHUB-2675)</a> 

<!--- 47639 --> * The `setup:di:compile` script now compiles all files as expected. <a href="https://github.com/magento/magento2/issues/2888" target="_blank">(GITHUB-2888)</a>

<!--- 46044 --> * Synonyms now work as expected with Magento 2.x.  <a href="https://github.com/magento/magento2/issues/2519" target="_blank">(GITHUB-2519)</a> 

<!--- 40320 --> * Attribute 'setup_version' is missing for module error when defined as optional. <a href="https://github.com/magento/magento2/issues/1493" target="_blank">(GITHUB-1493)</a>

<!--- 52605 --> * Magento no longer flushes the cache during checkout. <a href="https://github.com/magento/magento2/issues/4222" target="_blank">(GITHUB-4222)</a>







<h3>Technology stack</h3>

Our technology stack is built on PHP and MySQL. Magento 2.1 Release Candidate-1 supports PHP 5.6 and 7.0.2, and MySQL 5.6.

We do not support PHP 5.5. 


<h3>Installation instructions</h3>


<h4>New installations</h4>
New users can now complete a full installation of Magento Enterprise Edition 2.1 Release Candidate-1 from an archive file.

#####<b>Download a new installation</b>#####
1. Go to the <a href="https://www.magento.com/" target="_blank">Magento</a> website, and click **My Account**. Then, log in to your account. 
2. In the panel on the left, choose **Downloads**. Choose **Magento Enterprise Edition 2.x**, and do the following:

	a.	Click **Magento Enterprise Edition 2.x Release**.

	b.	In the list, choose **Version 2.1**.

	c.	Click **Download**.

3.	Follow the instructions to upgrade and verify your installation. If you need help, go to the **Support** tab of your Magento account, and **Open a Ticket**.


<h4>Upgrade existing installations</h4>
If you installed Magento Enterprise Edition 2.0.0 from an archive, you must perform some additional tasks before you can upgrade your installation. Current users of Magento 2.0.0/2.0.1/2.0.2/2.0.3/2.0.4/2.0.5/2.0.6 must first update the installer from the command line. Then, update the installation from the <a href="http://docs.magento.com/m2/ce/user_guide/system/web-setup-wizard.html" target="_blank">Web Setup Wizard</a> or command line. For detailed instructions, see the <a href="{{ site.gdeurl }}release-notes/tech_bull_201-upgrade.html" target="_blank">technical bulletin</a>.

Readiness check can fail under some circumstances because of incorrect permissions on the `var/session` directory. To resolve this issue, enter the following command:

<code>chmod -R 770 <your Magento install dir>/var/session</code>

For example, enter <code>chmod -R 770 /var/www/magento2/var/session</code>


#####<b>Upgrade an existing installation from the Setup Wizard</b>#####

1. Log in to the Admin panel with Administrator privileges.

2.	On the Admin sidebar, click **System**. Under **Tools**,  choose **Web Setup Wizard**.

3.	Click  **System Upgrade**. Follow the onscreen instructions to complete the upgrade.

For more information, see <a href="{{ site.gdeurl }}comp-mgr/bk-compman-upgrade-guide.html" target="_blank">Upgrade the Magento installation and components</a>.

#####<b>Magento Partners</b>#####
Magento partners can download the release and the release notes in PDF format from the Partner Portal.

1.	Log in to the <a href="https://magento.com/partners/become-a-partner" target="_blank">Partner Portal</a>.
2.	Under Magento Enterprise Edition, choose **Magento Enterprise Edition 2.x**.
3.	Find the **Magento Enterprise Edition 2.x Release**, and choose **Version 2.1**.

<h3>Migration toolkits</h3>
The <a href="{{ site.gdeurl }}migration/migration-migrate.html" target="_blank">Data Migration Tool</a> helps transfer existing Magento 1.x store data to Magento 2.x. This command-line interface includes verification, progress tracking, logging, and testing functions. For installation instructions, see  <a href="{{ site.gdeurl }}migration/migration-tool-install.html" target="_blank">Install the Data Migration Tool</a>. Consider exploring or contributing to the <a href="https://github.com/magento/data-migration-tool" target="_blank"> Magento Data Migration repository</a>.

The <a href="https://github.com/magento/code-migration" target="_blank">Code Migration Toolkit</a> helps transfer existing Magento 1.x store extensions and customizations to Magento 2.0.x. The command-line interface includes scripts for converting Magento 1.x modules and layouts.

<h3>Migration toolkits</h3>
The <a href="{{ site.gdeurl }}migration/migration-migrate.html" target="_blank">Data Migration Tool</a> helps transfer existing Magento 1.x store data to Magento 2.x. This command-line interface includes verification, progress tracking, logging, and testing functions. For installation instructions, see  <a href="{{ site.gdeurl }}migration/migration-tool-install.html" target="_blank">Install the Data Migration Tool</a>. Consider exploring or contributing to the <a href="https://github.com/magento/data-migration-tool" target="_blank"> Magento Data Migration repository</a>.










