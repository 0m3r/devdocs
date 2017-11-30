---
layout: default
group: mftf
title: Actions in the Magento Functional Testing Framework cests
version: 2.3
github_link: magento-functional-testing-framework/cest/actions.md
---

This topic contains a reference list of available action type tags available in the MFTF cests.

## Overview

Actions in the MFTF allow you to automate different scenarios of Magento user's actions.
They are mostly XML implementations of [Codeception actions](http://codeception.com/docs/modules/WebDriver#Actions).
Some actions drive browser elements, while others use REST APIs.

All actions contain the following attributes that are useful for merging needs:

* `mergeKey` is a required attribute that stores a unique identifier of the action. Example: `"conditionalClickStep1"`.
* `remove` is an optional attribute that removes the action when merging same test in different modules
* `before` is an optional attribute that stores `mergeKey` of an action that will be executed one step before the current one
* `after` is an optional attribute that stores `mergeKey` of an action that will be executed next

## Principles

`mergeKey` value format principles:

* Must be unique within [`<test>`](../cest.html#test)
* Naming should be as descriptive as possible
  * Should describe the action performed
  * Should briefly describe the purpose
  * May describe which data is in use
* Should be in camelCase with lowercase first letter
* Should be the last attribute of an element

## Example
{%raw%}
The following example contains four actions:

1. [Open the Sign In page for a Customer](#example-step1)
2. [Enter customer's e-mail](#example-step2)
3. [Enter customer's password](#example-step3)
4. [Click the Sign In button](#example-step4)

```xml
<amOnPage mergeKey="amOnSignInPage"  url="{{StorefrontCustomerSignInPage}}"/>
<fillField  mergeKey="fillEmail" userInput="$$customer.email$$" selector="{{StorefrontCustomerSignInFormSection.emailField}}"/>
<fillField  mergeKey="fillPassword" userInput="$$customer.password$$" selector="{{StorefrontCustomerSignInFormSection.passwordField}}"/>
<click mergeKey="clickSignInAccountButton" selector="{{StorefrontCustomerSignInFormSection.signInAccountButton}}"/>
```

#### 1. Open the Sign In page for a Customer {#example-step1}

```xml
<amOnPage mergeKey="amOnSignInPage"  url="{{StorefrontCustomerSignInPage.url}}"/>
```

The Customer Sign In page is declared in the _.../Customer/Page/StorefrontCustomerSignInPage.xml_.
The given relative URI is declared in `StorefrontCustomerSignInPage.url`

The _StorefrontCustomerSignInPage.xml_ source code:

```xml
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="../../../../../../vendor/magento/magento2-functional-testing-framework/src/Magento/FunctionalTestingFramework/Page/etc/PageObject.xsd">
    <page name="StorefrontCustomerSignInPage" url="/customer/account/login/" module="Magento_Customer">
        <section name="StorefrontCustomerSignInFormSection" />
    </page>
</config>
```

[amOnPage](#amonpage) is an action that opens a page for a given URI. It has a key `"amOnSignInPage"` that will be used as a reference for merging needs in other modules.
This action uses value of the `url` attribute for the given relative URI to open in browser.
Here, `url` contains a pointer to a `url` attribute of the `StorefrontCustomerSignInPage`.

#### 2. Enter customer's e-mail  {#example-step2}

```xml
<fillField  mergeKey="fillEmail" userInput="$$customer.email$$" selector="{{StorefrontCustomerSignInFormSection.emailField}}"/>
```

[fillField](#fillfield) fills a text field with the given string.

The customer's e-mail is stored in the `email` parameter of the `customer` entity created somewhere earlier it the test using a tag [createData](#createdata).
`userInput` points to that data.

`selector` points to the field where to enter the data.
A required selector is stored in the `emailField` element of the `StorefrontCustomerSignInFormSection` section.

This section is declared in _.../Customer/Section/StorefrontCustomerSignInFormSection.xml_:
{: #section-code}

```xml
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="../../../../../../vendor/magento/magento2-functional-testing-framework/src/Magento/FunctionalTestingFramework/Page/etc/SectionObject.xsd">
    <section name="StorefrontCustomerSignInFormSection">
        <element name="emailField" type="input" selector="#email"/>
        <element name="passwordField" type="input" selector="#pass"/>
        <element name="signInAccountButton" type="button" selector="#send2" timeout="30"/>
    </section>
</config>
```

#### 3. Enter customer's password  {#example-step3}

```xml
<fillField  mergeKey="fillPassword" userInput="$$customer.password$$" selector="{{StorefrontCustomerSignInFormSection.passwordField}}"/>
```

The action here is very similar to the action in a previous step.
The only difference is that different data assigned to the attributes which set a field with password.


#### 4. Click the Sign In button {#example-step4}

```xml
<click mergeKey="clickSignInAccountButton" selector="{{StorefrontCustomerSignInFormSection.signInAccountButton}}"/>
```

Here, [click](#click) performs a click on a button that can be found by selector that is stored in the `signInAccountButton` of the `StorefrontCustomerSignInFormSection`.
See the _StorefrontCustomerSignInPage.xml_ code in [step 2](#section-code)
{%endraw%}
## Available actions

The following list contains reference documentation about all action elements available in the MFTF.
If description of an element does not includes a link to Codeception analogue, it means that the action is developed by Magento for specific MFTF needs.

### acceptPopup

[See acceptPopup docs on codeception.com](http://codeception.com/docs/modules/WebDriver#acceptPopup){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### amOnPage

[See amOnPage docs on codeception.com](http://codeception.com/docs/modules/WebDriver#amOnPage){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### amOnSubdomain

[See amOnSubdomain docs on codeception.com](http://codeception.com/docs/modules/WebDriver#amOnSubdomain){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### amOnUrl

[See amOnUrl docs on codeception.com](http://codeception.com/docs/modules/WebDriver#amOnUrl){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### appendField

[See appendField docs on codeception.com](http://codeception.com/docs/modules/WebDriver#appendField){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### attachFile

[See attachFile docs on codeception.com](http://codeception.com/docs/modules/WebDriver#attachFile){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### cancelPopup

[See cancelPopup docs on codeception.com](http://codeception.com/docs/modules/WebDriver#cancelPopup){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### checkOption

[See checkOption docs on codeception.com](http://codeception.com/docs/modules/WebDriver#checkOption){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### click

[See click docs on codeception.com](http://codeception.com/docs/modules/WebDriver#click){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### clickWithLeftButton

[See clickWithLeftButton docs on codeception.com](http://codeception.com/docs/modules/WebDriver#clickWithLeftButton){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
x|string|optional|
y|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### clickWithRightButton

[See clickWithRightButton docs on codeception.com](http://codeception.com/docs/modules/WebDriver#clickWithRightButton){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
x|string|optional|
y|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### closeAdminNotification

Remove from the DOM all elements with the CSS classes `.modal-popup` or `.modals-overlay`

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### closeTab

[See closeTab docs on codeception.com](http://codeception.com/docs/modules/WebDriver#closeTab){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### conditionalClick

Click on `selector` if and only if `dependentSelector` has visibility of the `visible` flag.

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
dependentSelector|string|optional|
visible|boolean|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### createData

[See the Data topic for more information.](../data.html)

Attribute|Type|Use|Default
---|---|---|---
entity|string|required|	
mergeKey|string|required	
remove| boolean|optional|false
before|string|optional|	
after|string|optional|	
storeCode|string|optional|

This action can optionally contain one or more `required-entity` child elements.

#### required-entity

[See the Data topic for more information.](../data.html)

Attribute|Type|Use|Default
---|---|---|---
createDataKey|string|required|	
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### deleteData

[See the Data topic for more information.](../data.html)

Attribute|Type|Use|Default
---|---|---|---
createDataKey|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|
storeCode|string|optional|

### dontSee

[See the codeception.com documentation for more information about this action.](http://codeception.com/docs/modules/WebDriver#dontSee){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
selector|string|optional|
selectorArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeCheckboxIsChecked

[See dontSeeCheckboxIsChecked docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeCheckboxIsChecked){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeCookie

[See dontSeeCookie docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeCookie){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeCurrentUrlEquals

[See dontSeeCurrentUrlEquals docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeCurrentUrlEquals){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeCurrentUrlMatches

[See dontSeeCurrentUrlMatches docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeCurrentUrlMatches){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeElement

[See dontSeeElement docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeElement){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
parameterArray|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeElementInDOM

[See dontSeeElementInDOM docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeElementInDOM){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
parameterArray|string|optional|
attributeArray|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeInCurrentUrl

[See dontSeeInCurrentUrl docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeInCurrentUrl){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeInField

[See dontSeeInField docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeInField){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeInFormFields

[See dontSeeInFormFields docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeInFormFields){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
parameterArray|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeInPageSource

[See dontSeeInPageSource docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeInPageSource){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeInSource

[See dontSeeInSource docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeInSource){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
html|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeInTitle

[See dontSeeInTitle docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeInTitle){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeJsError

Assert that there are no Javascript errors.

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeLink

[See dontSeeLink docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeLink){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dontSeeOptionIsSelected

[See dontSeeOptionIsSelected docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dontSeeOptionIsSelected){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### doubleClick

[See doubleClick docs on codeception.com](http://codeception.com/docs/modules/WebDriver#doubleClick){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### dragAndDrop

[See dragAndDrop docs on codeception.com](http://codeception.com/docs/modules/WebDriver#dragAndDrop){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector1|string|optional|
selector2|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### executeInSelenium

[See executeInSelenium docs on codeception.com](http://codeception.com/docs/modules/WebDriver#executeInSelenium){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
function|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### executeJS

[See executeJS docs on codeception.com](http://codeception.com/docs/modules/WebDriver#executeJS){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
function|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### fillField

[See fillField docs on codeception.com](http://codeception.com/docs/modules/WebDriver#fillField){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### formatMoney

[See PHP docs](http://php.net/manual/en/function.money-format.php){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
locale|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabAttributeFrom

[See grabAttributeFrom docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabAttributeFrom){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
returnVariable|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabCookie

[See grabCookie docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabCookie){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
parameterArray|string|optional|
returnVariable|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabFromCurrentUrl

[See grabFromCurrentUrl docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabFromCurrentUrl){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
returnVariable|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabMultiple

[See grabMultiple docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabMultiple){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
returnVariable|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabPageSource

[See grabPageSource docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabPageSource){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
returnVariable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabTextFrom

[See grabTextFrom docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabTextFrom){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
returnVariable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### grabValueFrom

[See grabValueFrom docs on codeception.com](http://codeception.com/docs/modules/WebDriver#grabValueFrom){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
returnVariable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### loadSessionSnapshot

[See loadSessionSnapshot docs on codeception.com](http://codeception.com/docs/modules/WebDriver#loadSessionSnapshot){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
returnVariable|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### loginAsAdmin

A convenient helper that will go to the Magento admin page, fill in the username and password, click login, and finally
calls [closeAdminNotification](#closeAdminNotification)

Attribute|Type|Use|Default
---|---|---|---
username|string|optional|
password|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### makeScreenshot

[See makeScreenshot docs on codeception.com](http://codeception.com/docs/modules/WebDriver#makeScreenshot){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### maximizeWindow

[See maximizeWindow docs on codeception.com](http://codeception.com/docs/modules/WebDriver#maximizeWindow){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### moveBack

[See moveBack docs on codeception.com](http://codeception.com/docs/modules/WebDriver#moveBack){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### moveForward

[See moveForward docs on codeception.com](http://codeception.com/docs/modules/WebDriver#moveForward){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### moveMouseOver

[See moveMouseOver docs on codeception.com](http://codeception.com/docs/modules/WebDriver#moveMouseOver){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
x|string|optional|
y|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### mSetLocale

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
locale|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### mResetLocale

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### openNewTab

[See openNewTab docs on codeception.com](http://codeception.com/docs/modules/WebDriver#openNewTab){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### parseFloat

Parse float number with thousands seperator.

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### pauseExecution

[See pauseExecution docs on codeception.com](http://codeception.com/docs/modules/WebDriver#pauseExecution){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### performOn

[See performOn docs on codeception.com](http://codeception.com/docs/modules/WebDriver#performOn){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
function|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### pressKey

[See pressKey docs on codeception.com](http://codeception.com/docs/modules/WebDriver#pressKey){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### reloadPage

[See reloadPage docs on codeception.com](http://codeception.com/docs/modules/WebDriver#reloadPage){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### resetCookie

[See resetCookie docs on codeception.com](http://codeception.com/docs/modules/WebDriver#resetCookie){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### resizeWindow

[See resizeWindow docs on codeception.com](http://codeception.com/docs/modules/WebDriver#resizeWindow){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
width|string|optional|
height|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### saveSessionSnapshot

[See saveSessionSnapshot docs on codeception.com](http://codeception.com/docs/modules/WebDriver#saveSessionSnapshot){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### scrollTo

[See scrollTo docs on codeception.com](http://codeception.com/docs/modules/WebDriver#scrollTo){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
x|string|optional|
y|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### scrollToTopOfPage

A convenience function that executes `window.scrollTo(0,0)` as Javascript thus returning to the top of the page.

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### searchAndMultiSelectOption

Search for and select options from a Magento multi-select drop down menu.
e.g. The drop down menu you use to assign Products to Categories.

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
parameterArray|string|optional|
requiredAction|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### see

[See see docs on codeception.com](http://codeception.com/docs/modules/WebDriver#see){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
selector|string|optional|
selectorArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeCheckboxIsChecked

[See seeCheckboxIsChecked docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeCheckboxIsChecked){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeCookie

[See seeCookie docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeCookie){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeCurrentUrlEquals

[See seeCurrentUrlEquals docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeCurrentUrlEquals){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeCurrentUrlMatches

[See seeCurrentUrlMatches docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeCurrentUrlMatches){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeElement

[See seeElement docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeElement){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
parameterArray|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeElementInDOM

[See seeElementInDOM docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeElementInDOM){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
parameterArray|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInCurrentUrl

[See seeInCurrentUrl docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInCurrentUrl){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInField

[See seeInField docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInField){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
selectorArray|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInFormFields

[See seeInFormFields docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInFormFields){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
parameterArray|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInPageSource

[See seeInPageSource docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInPageSource){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
html|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInPopup

[See seeInPopup docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInPopup){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInSource

[See seeInSource docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInSource){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
html|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeInTitle

[See seeInTitle docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeInTitle){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeLink

[See seeLink docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeLink){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
url|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeNumberOfElements

[See seeNumberOfElements docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeNumberOfElements){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### seeOptionIsSelected

[See seeOptionIsSelected docs on codeception.com](http://codeception.com/docs/modules/WebDriver#seeOptionIsSelected){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### selectOption

[See selectOption docs on codeception.com](http://codeception.com/docs/modules/WebDriver#selectOption){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### setCookie

[See setCookie docs on codeception.com](http://codeception.com/docs/modules/WebDriver#setCookie){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
parameterArray|string|optional|
value|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### submitForm

[See submitForm docs on codeception.com](http://codeception.com/docs/modules/WebDriver#submitForm){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
parameterArray|string|optional|
button|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### switchToIFrame

[See switchToIFrame docs on codeception.com](http://codeception.com/docs/modules/WebDriver#switchToIFrame){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### switchToNextTab

[See switchToNextTab docs on codeception.com](http://codeception.com/docs/modules/WebDriver#switchToNextTab){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### switchToPreviousTab

[See switchToPreviousTab docs on codeception.com](http://codeception.com/docs/modules/WebDriver#switchToPreviousTab){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### switchToWindow

[See switchToWindow docs on codeception.com](http://codeception.com/docs/modules/WebDriver#switchToWindow){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### typeInPopup

[See typeInPopup docs on codeception.com](http://codeception.com/docs/modules/WebDriver#typeInPopup){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### uncheckOption

[See uncheckOption docs on codeception.com](http://codeception.com/docs/modules/WebDriver#uncheckOption){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### unselectOption

[See unselectOption docs on codeception.com](http://codeception.com/docs/modules/WebDriver#unselectOption){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
userInput|string|optional|
parameterArray|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### wait

[See wait docs on codeception.com](http://codeception.com/docs/modules/WebDriver#wait){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForAjaxLoad

Wait for all AJAX calls to finish.

Attribute|Type|Use|Default
---|---|---|---
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForElementChange

[See waitForElementChange docs on codeception.com](http://codeception.com/docs/modules/WebDriver#waitForElementChange){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
function|string|optional|
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForElement

[See waitForElement docs on codeception.com](http://codeception.com/docs/modules/WebDriver#waitForElement){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForElementNotVisible

[See waitForElementNotVisible docs on codeception.com](http://codeception.com/docs/modules/WebDriver#waitForElementNotVisible){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForElementVisible

[See waitForElementVisible docs on codeception.com](http://codeception.com/docs/modules/WebDriver#waitForElementVisible){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
selector|string|optional|
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForJS

[See waitForJS docs on codeception.com](http://codeception.com/docs/modules/WebDriver#waitForJS){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
function|string|optional|
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForLoadingMaskToDisappear

Wait for all Magento loading overlays to go away. Note: The CSS class for loading masks is not used consistently throughout Magento. Therefore, this convenience function tries to wait for various specific selectors.

```
# Wait for these classes to not be visible

//div[contains(@class, "loading-mask")]
//div[contains(@class, "admin_data-grid-loading-mask")]
//div[contains(@class, "admin__data-grid-loading-mask")]
//div[contains(@class, "admin__form-loading-mask")]
//div[@data-role="spinner"]
```
 

Attribute|Type|Use|Default
---|---|---|---
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForPageLoad

Wait for AJAX, Magento loading overlays, and `document.readyState == "complete"`

Attribute|Type|Use|Default
---|---|---|---
time|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|

### waitForText

[See waitForText docs on codeception.com](http://codeception.com/docs/modules/WebDriver#waitForText){:target='_blank'}

Attribute|Type|Use|Default
---|---|---|---
userInput|string|optional|
time|string|optional|
selector|string|optional|
variable|string|optional|
mergeKey|string|required|
remove|boolean|optional|false
before|string|optional|
after|string|optional|


<!-- Abbreviations -->

*[MFTF]: Magento Functional Testing Framework
