---
layout: default
group: ext-best-practices
subgroup: 01_Admin
title: Admin
menu_title: Admin
menu_order: 1
menu_node: parent
github_link: ext-best-practices/admin/admin-best-practices.md
---

##{{page.menu_title}}

This section of the Best Practices Guide dicsusses the Admin. The Admin is the GUI panel that is used by Magento administrators. It is where Magento system and storefront parameters can be modified and configured. Third-party extensions installed by the system admin or merchant are also found in this area, along with any of their configuration settings.

This is an important section to read, because what extension developers choose to do (or not do) when integrating their extensions has a big impact on the look and feel of the Admin UI.

<div class="bs-callout bs-callout-info" id="info">
  <p>Remember that most merchants have multiple extensions installed, and most of these extesniosn have to exposed through the Admin. In order to prevent a cluttered Admin, please follow the guielines, and communicate with Magento and the community when you have questions.</p>
</div>

For additional information on design topics for the Admin, read the [Admin Pattern Library]({{site.gdeurl}}pattern-library/bk-pattern.html) and the [Admin Style Guide]({{site.gdeurl}}design-styleguide/bk-styleguide.html).

###Articles

{% assign subgroup = site.articles | where:"group","ext-best-practices" | where: "subgroup","01_Admin" | where: "menu_node",null | sort: "menu_order" %}

{% for node in subgroup %}
*  [{{ node.menu_title }}]({{site.gdeurl}}{{ node.github_link | replace: ".md",".html" }})
{% endfor %}
