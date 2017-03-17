---
layout: default
group: UI_Components_guide
subgroup: components
title: DateColumn component
menu_title: DateColumn component
version: 2.2
github_link: ui_comp_guide/components/listing/ui-datecolumn.md
---

## Overview

The DateColumn component is a table's column that displays dates in a format defined by the `dateFormat` option.

DateColumn сonstructor: [app/code/Magento/Ui/view/base/web/js/grid/columns/date.js]({{site.mage2200url}}app/code/Magento/Ui/view/base/web/js/grid/columns/date.js)

## DateColumn configuration

Extends all [Column]({{page.baseurl}}ui_comp_guide/components/listing/ui-column.html) configuration.

DateColumn specific configuration:

<table>
  <tr>
    <th>Option</th>
    <th>Description</th>
    <th>Type</th>
    <th>Default</th>
  </tr>
  <tr>
    <td><code>dateFormat</code></td>
    <td>Date format for the displayed column's values.</td>
    <td>String</td>
    <td><code>MMM d, YYYY h:mm:ss A</code></td>
  </tr>
</table>
