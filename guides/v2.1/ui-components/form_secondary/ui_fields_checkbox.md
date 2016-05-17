---
layout: default
group:  UI Library
subgroup: D_UI Library Form Component
title: Checkbox type field component
menu_title: Checkbox type field component
menu_node:
github_link: ui-components/form_secondary/ui_fields_checkbox.md
---

<h2>What's in this topic</h2>

This topic describes the checkbox UI component, that is one of the types of the field UI component.

**Contents**

- TOC
{:toc}

## Overview

The checkbox UI component can be configured to implement the following field types (from the UI point of view): radio button, toggle button or checkbox. The component inherits the abstract behavior of the field UI component.


The following images illustrate how the different implementation of the field can look like:

- when configured as radio button:

<img src="{{site.baseurl}}common/images/ui_checkbox_radio.png">

- when configured as toggle:

<img src="{{site.baseurl}}common/images/ui_checkbox_toggle.png">

- when configured as checkbox:

<img src="{{site.baseurl}}common/images/ui_checkbox_checkbox.png">

The checkbox UI component is designed to be used in the Admin panel. 

## Structure
The checkbox UI component comprises the following files:

- JavaScript Ui component: `<Magento_UI_module_dir>/view/base/web/js/form/element/single-checkbox.js`
- Templates:
	- `<Magento_UI_module_dir>/view/base/web/templates/form/components/single/checkbox.html`
	- `<Magento_UI_module_dir>/view/base/web/templates/form/components/single/radio.html`
	- `<Magento_UI_module_dir>/view/base/web/templates/form/components/single/switcher.html`

## Configuration settings

In general case, the checkbox configuration file looks like following:

<p class="q">Is there some conventional naming/location?</p>

{%highlight xml%}
<field name="%Component_Name%">
    <argument name="data" xsi:type="array">
        <item name="config" xsi:type="array">
            <!-- Mandatory options, inherited from the field UI component -->
            <item name="formElement" xsi:type="string">checkbox</item>
            <item name="dataType" xsi:type="string">text</item>
            <item name="dataScope" xsi:type="string">%Component_Name%</item>
            <!-- Optional checkbox-specific options -->
            <item name="%option1%" xsi:type="%option1_type%">%option1_value%</item>
            <item name="%option2%" xsi:type="%option2_type%">%option2_value%</item>
            ...
        </item>
    </argument>
</field>
{%endhighlight%}

Example of the checkbox component configuration in the scope of the form configuration:

{%highlight xml%}
<form xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Ui:etc/ui_configuration.xsd">
    <!-- Checkbox component configuration. START --> 
    <field name="my_checkbox">
        <argument name="data" xsi:type="array">
            <item name="config" xsi:type="array">
                <item name="formElement" xsi:type="string">checkbox</item>
                <item name="dataType" xsi:type="string">text</item>
                <item name="dataScope" xsi:type="string">my_checkbox</item>
            </item>
        </argument>
    </field>
    <!-- Checkbox component configuration. END --> 
    <argument name="data" xsi:type="array">
        <item name="js_config" xsi:type="array">
            <item name="provider" xsi:type="string">catalog_rule_form.catalog_rule_form_data_source</item>
            <item name="deps" xsi:type="string">catalog_rule_form.catalog_rule_form_data_source</item>
        </item>
        <item name="config" xsi:type="array">
            <item name="dataScope" xsi:type="string">data</item>
            <item name="namespace" xsi:type="string">catalog_rule_form</item>
        </item>
        <item name="template" xsi:type="string">templates/form/collapsible</item>
    </argument>
    <dataSource name="catalog_rule_form_data_source">я нап
        <argument name="dataProvider" xsi:type="configurableObject">
            <argument name="class" xsi:type="string">Magento\CatalogRule\Model\Rule\DataProvider</argument>
            <argument name="name" xsi:type="string">catalog_rule_form_data_source</argument>
            <argument name="primaryFieldName" xsi:type="string">rule_id</argument>
            <argument name="requestFieldName" xsi:type="string">id</argument>
            <argument name="data" xsi:type="array">
                <item name="config" xsi:type="array">
                    <item name="submit_url" xsi:type="url" path="catalog_rule/promo_catalog/save"/>
                </item>
            </argument>
        </argument>
        <argument name="data" xsi:type="array">
            <item name="js_config" xsi:type="array">
                <item name="component" xsi:type="string">Magento_Ui/js/form/provider</item>
            </item>
        </argument>
    </dataSource>
</form>
{%endhighlight%}

### Chekbox-specific options

The following table contains the options you can configure for the checkbox component.

<table>
  <tr>
    <th>
      Option
    </th>
    <th>
      Description
    </th>
    <th>
      Type
    </th>
    <th>
      Required?
    </th>
    <th>
      Default
    </th>
  </tr>
  <tr>
    <td>
      <code>checked</code>
    </td>
    <td>
      Defines the default state of the component.
      Has lower priority than the <code>value</code> option if both are specified.
    </td>
    <td>
      Boolean
    </td>
    <td>
      optional
    </td>
    <td>
      <code>false</code>
    </td>
  </tr>
  <tr>
    <td>
      <code>description</code>
    </td>
    <td>
      The text displayed next to the field. See the <a href="#description">illustration</code> following this table.
    </td>
    <td>
      String
    </td>
    <td>
      <p>
        optional
      </p>
    </td>
    <td>
      ''
    </td>
  </tr>
  <tr>
    <td>
      <code>disabled</code>
    </td>
    <td>
      Disables/enables the component's UI for the user. When disabled, the field appears dimmed.
<p class="q">Is it true? Only UI, it still can be set programically?</p>
    </td>
    <td>
      Boolean
    </td>
    <td>
      optional
    </td>
    <td>
      false
    </td>
  </tr>
  <tr>
    <td>
      <code>elementTmpl</code>
    </td>
    <td>
      Path to the template in terms of RequireJS.
    </td>
    <td>
      String
    </td>
    <td>
      optional
    </td>
    <td>
      Depends on how the <code>prefer</code> option is configured
<p class="q">so can we specify any values? </p>
    </td>
  </tr>
  <tr>
    <td>
      <code>initialValue</code>
    </td>
    <td>
      The <code>value</code> of the component at the moment before initialization a
      component. Cannot be set in configuration. It is a calculated value.
<p class="q">Before initialization? </p>
    </td>
    <td>
      *
    </td>
    <td>
      -
    </td>
    <td>
      -
    </td>
  </tr>
  <tr>
    <td>
      <code>isMultiple</code>
    </td>
    <td>
      Enables the component to behave as checkbox set. See the [section with examples of usage](#checkbox_set) for illustration. 
    </td>
    <td>
      Boolean
    </td>
    <td>
      optional
    </td>
    <td>
      <code>false</code>
    </td>
  </tr>
  <tr>
    <td>
      <code>prefer</code>
    </td>
    <td>
      Defines the UI representation: checkbox, toggle or radio
      button. Does not influence the behavior.
    </td>
    <td>
      <code>'checkbox'</code>|<code>'toggle'</code>|<code>'radio'</code>
    </td>
    <td>
      optional
    </td>
    <td>
      <code><code>'checkbox'</code></code>
    </td>
  </tr>
  <tr>
    <td>
      <code>value</code>
    </td>
    <td>
      The value the component can return. If one value is set, then it is automatically assigned to the "checked" state; in the "unchecked" state the component returns an empty string in this case. You can assign a separate value for each state using the <code>valueMap</code> option.
    </td>
    <td>
      
    </td>
    <td>
      optional
    </td>
    <td>
      <p class="q">any default value?</p>
    </td>
  </tr>
  <tr>
    <td>
      <code>valueMap</code>
    </td>
    <td>
      Allows to associate values with binary states. The type of
      values should match the type of value from DataProvider.
    <p class="q">DataProvider or data provider?</p>
    </td>
    <td>
      Object. Expected structure:
      <pre>
{
  'true':  &lt;value_for_CHECKED_state&gt;,
  'false': &lt;value_for_UNCHECKED_state&gt;
}
</pre>
    </td>
    <td>
      optional
    </td>
    <td>
      <code>{}</code>
    </td>
  </tr>
  <tr>
    <td>
      <code>visible</code>
    </td>
    <td>
      Shows/hides the component from the page. In runtime component
      still exist and can be accessed.
    </td>
    <td>
      Boolean
    </td>
    <td>
      optional
    </td>
    <td>
      true
    </td>
  </tr>

</table>

The following image illustrates the difference between the label and description {#description}:

<img src="{{site.baseurl}}common/images/ui_checkbox_desc.png" />


The component calculates the initial state from data in DataProvider according to the following pattern:

<p class="q">what kind of entity DataProvider is?</p>


    $provider[ .. ]['Component_Index'] = true; // Checkbox appear to be checked
    $provider[ .. ]['Component_Index'] = false; // Checkbox appear to be unchecked
    $provider[ .. ]['Component_Index'] = null; // Will fallback to "default" state, false.
                                            // Because in example upper we define only "true" and "false" as allowed.
                                            // It could not obvious on first look.

## Public API (JS)

### `getReverseValueMap()`

    getReverseValueMap (value)
    @param {*} value
    @returns {Boolean}


Returns `true` if the passed argument is strictly equal to the `valueMap.true` setting. Otherwise returns `false`.

### `value()`

    value()
    @returns {*}

Getter, returns current checkbox `value`.

### `value(param)`

    value(param)
    @param {*} param

Setter, sets `param` as a `value`. Changes the checked state.

### `checked()`

    checked()
    @returns {Boolean}

Getter, returns current checkbox state: `true` if checked, `false` otherwise.

### `checked(param)`

    checked(param)
    @param {Boolean} param
    @returns void

Sets the checkbox state: checked, if `param` is `true`, unchecked if `param` is `false`.
In the current implementation the checkbox Ui component doesn't support [indeterminate](https://css-tricks.com/indeterminate-checkboxes/) state. Can affect `value`.


## Examples of use

### Example 1: Checkbox Ui Component as a Boolean state checker

You can use a checkbox Ui component to simply toggle some boolean flag. In this case you need to decide which pair of variables the checkbox Ui should process:

 - `true` / `false`, Boolean 
 - `'true'` / `'false'`, String
 - `1` / `0`, Number
 - `'1'` / `'0'`, String
 -  other pairs of variables of simple types.

The value pair is placed in the `valueMap` parameter. 
Consider, that data from the component is always sent using the POST method and all values are casted to strings. You controller/model must handle such situation.

<p class="q">such situation?</p>

If you decide that value pair to be of Boolean type, the component's configuration might look like following:

{%highlight xml%}
<field name="Component_Index">
    <argument name="data" xsi:type="array">
        <item name="config" xsi:type="array">
            <item name="formElement" xsi:type="string">checkbox</item>
            <item name="dataType" xsi:type="string">text</item>
            <item name="dataScope" xsi:type="string">Component_Index</item>
            <item name="vallueMap" xsi:type="array">
                <item name="true" xsi:type="boolean">true</item>
                <item name="false" xsi:type="boolean">false</item>
            </item>
        </item>
    </argument>
</field>
{%endhighlight%}


### Example 2: Checkbox Ui Component as a Boolean state checker with toggle button look

The UI representation of the checkbox component is defined by the value of the `prefer` option.

So the component's configuration in this case might look like following:
 
{%highlight xml%}
<field name="Component_Index">
    <argument name="data" xsi:type="array">
        <item name="config" xsi:type="array">
            <item name="formElement" xsi:type="string">checkbox</item>
            <item name="dataType" xsi:type="string">text</item>
            <item name="dataScope" xsi:type="string">Component_Index</item>

            <item name="vallueMap" xsi:type="array">
                <item name="true" xsi:type="boolean">true</item>
                <item name="false" xsi:type="boolean">false</item>
            </item>
        </item>
    </argument>
</field>
{%endhighlight%}

### Example 3: Checkbox Ui component as a some value on/off switcher
<p class="q">need better description of the use case</p>
When the `value` option is set, and `vallueMap` is not set, the component tries to toggle `initialValue` and empty string:

<p class="q">why initialvalue?</p>
 
{%highlight xml%}
<field name="Component_Index">
    <argument name="data" xsi:type="array">
        <item name="config" xsi:type="array">
            <item name="formElement" xsi:type="string">checkbox</item>
            <item name="dataType" xsi:type="string">text</item>
            <item name="dataScope" xsi:type="string">Component_Index</item>

            <item name="value" xsi:type="number">42</item>
        </item>
    </argument>
</field>
{%endhighlight%}

DataProvider will recieve `42` or `''` (empty string).


### Example 4: Checkbox Ui component as a part of a radio set or checkbox set.{#checkbox_set}

In this case you need to handle form elements that belong to different parts of the form Ui component. To do this, you need to configure the checkbox components as follows: 

- To ensure the radioset look and feel, in the checkboxes configurations, setup `prefer = radio`.
- To handle the `initialValue`, setup `value`.
- To ensure the collaborative work of all radio components, set the same value for the `dataScope` option of all checkbox components.

<p class="q">do the following instructions describe the other case&</p>
`prefer = checkbox` and several components with setup `value` will handle value of `dataScope` as collection.

Checked values will appear in `dataScope`-collection. Uncheck will remove it from `dataScope`-collection.

