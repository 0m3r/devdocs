---
layout: default
group: extension-dev-guide
subgroup: 99_Module Development
title: Dependency injection
menu_title: Dependency injection
menu_order: 5
github_link: extension-dev-guide/depend-inj.md
redirect_from: /guides/v1.0/extension-dev-guide/depend-inj.html
---
##{{page.menu_title}}
{:.no_toc}

* TOC
{:toc}

### Dependency injection overview

Magento 2 uses *dependency injection* as an alternative to the Magento 1.x `Mage` class. [Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection){:target="_blank"} is when an object's dependencies are provided to it by its external environment instead of manually creating them internally.

In simple terms, when object A requires object or value B to fulfill a function, then B is a dependency of A.

A dependency creates a degree of coupling between objects in your code. A large amount of coupling limits code reuse and makes moving components to new projects difficult. Using dependency injection allows for a loose coupling in your code.

#### Dependency inversion principle

When using dependency injection, we encourage you to follow the  [dependency inversion principle](http://www.oodesign.com/dependency-inversion-principle.html){:target="_blank"}, a coding principle that stipulates you use abstractions to reduce code dependencies. This means that high level classes should use the interface of a low level class instead of working with it directly.

### Object manager overview

Since dependency management is the responsibility of the environment, some kind of object manager must be present in complex systems to avoid boilerplate code. The object manager is present only when composing code, and in larger applications(such as Magento), composing code is performed early in the bootstrapping process and boilerplate code is moved to the configuration of the object manager.

In Magento, the object manager is represented by the appropriately named [Object Manager]({{ site.mage2000url }}lib/internal/Magento/Framework/ObjectManager/ObjectManager.php){:target="_blank"}.

### Object Manager Configuration

There are three types of configuration that is required by Magento's object manager:

*	[Class configuration(definitions)](#class-configurations) - These configurations describe the class dependencies for an object.
* [Type configuration](#type-configuration) - These configurations describe how objects are instantiated and their lifestyle.
* [Abstraction-Implementation mappings](#abstraction-implementation-mappings) - These configurations map which concrete implementations to use when interfaces are requested.

#### Class configurations

Magento uses class constructor signatures, not doc-block annotations, to retrieve information about class dependencies; i.e. to define what dependencies are to be passed to an object. If you write your code in a regular way using the dependency inversion principle, you do not have to worry about class definitions.

##### Compiling dependencies
{:.no_toc}
By default, class definitions are read using reflection, but reflection is slow in PHP. To make Magento's ObjectManager as fast as possible, a definition compiler was introduced. One of the things the compiler does is generate all non-existing dependency injection service classes (proxies, factories and interceptors) declared in code or configuration.

*	If you have one website and one store, see [single-tenant compiler]({{ site.gdeurl }}config-guide/cli/config-cli-subcommands-compiler.html#config-cli-subcommands-single).
*	If you have multiple websites and stores, see [multi-tenant compiler]({{ site.gdeurl }}config-guide/cli/config-cli-subcommands-compiler.html#config-cli-subcommands-run).

#### Type configurations

Type configurations describe the parameters used to instantiate a class and lifestyle of class instances. Depending on it's scope, the configuration for Magento's object manager is stored in the following XML files:

* `app/etc/di.xml` - This is the global area application configuration.
* `<moduleDir>/etc/di.xml` - This is the module global area configuration.
* `<moduleDir>/etc/<area>/di.xml` - This is the module area-specific configuration.

The `<area>` can be any Magento area such as `adminthtml` or `frontend`. Each scope overrides any previously existing configuration when it is loaded. Configurations for each scope are merged across modules, so there is no way to create a configuration that is only seen by a single module.

All object manager configuration is located under the config node in the xml files:

{% highlight xml %}
<config>
    <!-- Object Manager configuration -->
</config>
{% endhighlight %}

These configurations are validated by the XML Schema file called [`config.xsd`]({{ site.mage2000url }}lib/internal/Magento/Framework/ObjectManager/etc/config.xsd){:target="_blank"}.

Magento reads all the configuration files declared in the system and merges them all together by appending all nodes.

The overall configuration is loaded in the following stages:

1. Initial (`app/etc/di.xml`)
2. Global (`<moduleDir>/etc/di.xml`)
3. Area-specific (`<moduleDir>/etc/<area>/di.xml`)

##### Declaring Type
{:.no_toc}

Type can be declared in your di.xml configuration node in the following ways:

{% highlight xml %}
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <type name="Magento\Core\Model\Session" /> // Default instance of Magento\Core\Model\Session type. Exists by default, can be omited.
    <virtualType name="moduleConfig" type="Magento\Core\Model\Config"> // Instance with global name "moduleConfig" of Magento\Core\Model\Config type
        <arguments>
            <argument name="type" xsi:type="string">system</argument>
        </arguments>
    </virtualType>
    <type name="Magento\Core\Model\App">
        <arguments>
            <argument name="config" xsi:type="object">moduleConfig</argument>
        </arguments>
    </type>
</config>
{% endhighlight %}

The preceding example declares the following types:

*	`Magento\Core\Model\Session`: If the type is not set explicitly, it is taken from the name.
*	`moduleConfig`: A virtual type that extends type `Magento\Core\Model\Config`.
*	`Magento\Core\Model\App`: All instances of this type receive an instance of `moduleConfig` as a dependency.

<div class="bs-callout bs-callout-info" id="info">
  <b>Virtual Type</b><br/>
  <p>A virtual type allows you to change the arguments of a specific injectable dependency and effectively create a new type of a particular class. This allows you to use a customized class without affecting other classes that have a dependency on the original.</p>
</div>

##### Arguments
{:.no_toc}

Class constructor arguments are configured in your di.xml in the argument node. All these arguments will be injected into the class during creation. The name of the argument configured in the XML file must correspond to the name of the parameter in the constructor in the configured class.

The example below creates instances of `Magento\Core\Model\Session` with the class constructor argument `$sessionName` set to a value of `adminhtml`:

<script src="https://gist.github.com/xcomSteveJohnson/8907e3d1d6f2cd691d46.js"></script>

**Argument Types:**

`object`

Node Formats:

: `<argument xsi:type="object">{typeName}</argument>`
: `<argument xsi:type="object" shared="{shared}">{typeName}</argument>`

Creates an instance of `typeName` type and passes it in as an argument. Any class name, interface name, or virtual type name can be passed as `typeName`.

Setting the `shared` property defines the lifestyle of a created instance. See [Lifecycle Management](#object-lifetime-management).

---

`string`

Node Formats:

: `<argument xsi:type="string">{strValue}</argument>`
: `<argument xsi:type="string" translate="true">{strValue}</argument>`

Any value for this argument node will be interpreted as a string.

---

`boolean`

Node Format:

: `<argument xsi:type="boolean">{boolValue}</argument>`

Any value for this argument node will be converted into a boolean value. See table below:

| Input Type | Data     | Boolean Value |
| ---------- | -------- | ------------- |
| Boolean    | true     | true          |
| Boolean    | false    | false         |
| String     | "true"*  | true          |
| String     | "false"* | false         |
| String     | "1"      | true          |
| String     | "0"      | false         |
| Integer    | 1        | true          |
| Integer    | 0        | false         |

<small>*These String literals are case-sensitive</small>

---

`number`

Node Format:

: `<argument xsi:type="number">{numericValue}</argument>`

Acceptable values for this type include: integers, floats, or [numeric strings](http://us3.php.net/is_numeric){:taget="_blank"}.

---

`init_parameter`

Node Format:

: `<argument xsi:type="init_parameter">{Constant::NAME}</argument>`

The global application initialization argument represented by `Constant::NAME` is looked up and passed as argument.

---

`const`

Node Format:

: `<argument xsi:type="const">{Constant::NAME}</argument>`

The `Constant::NAME` constant value will be looked up and passed as the argument.

---

`null`

Node Format:

: `<argument xsi:type="null"/>`

A null value will be passed in as an argument.

---

`array`

Node Format:

: ~~~
  <argument xsi:type="array">
    <item name="someKey" xsi:type="<type>">someVal</item>
  </argument>
  ~~~

An array with elements corresponding to the items will be passed as the argument. The array can contain an infinite number of items. Each array item can be of any object type including an array itself.

When the configuration files for a given scope are merged, array arguments with the same name are merged into a new array. If a new configuration is loaded at a later time, either by a more specific scope or through code, then any array definitions in the new configuration will completely replace the previously loaded config instead of being merged.

---

**Argument Examples:**

<script src="https://gist.github.com/xcomSteveJohnson/24ffa1426734520f58a1.js"></script>

<div class="bs-callout bs-callout-info" id="merging-info">
  <b>Merging and Arguments</b><br/>
  <p>During merging, arguments with the same name are completely replaced if their type is different. If the argument type is the same, then they are overridden.</p>
</div>

#### Abstraction-Implementation mappings

The abstraction-implementation mappings are used by Magento's object manager when the constructor signature of a class requests an object by its interface. The object manager uses these mappings to determine what the default implementation is for that class for a particular scope.

The default implementation is specified using the `preference` node:

~~~
<!--  File: app/etc/di/config.xml -->
<config>
    <preference for="Magento\Core\Model\UrlInterface" type="Magento\Core\Model\Url" />
</config>
~~~

Since this mapping is in `app/etc/di/config.xml`, wherever there is a request for the `Magento\Core\Model\UrlInterface` in the global scope, the `Magento\Core\Model\Url` implementation class will be used.

~~~
<!-- File: app/code/core/Magento/Backend/etc/adminhtml/di.xml -->
<config>
    <preference for="Magento\Core\Model\UrlInterface" type="Magento\Backend\Model\Url" />
</config>
~~~

Since this mapping is in `app/code/core/Magento/Backend/etc/adminhtml/di.xml`, wherever there is a request for the `Magento\Core\Model\UrlInterface` in the admin area, the `Magento\Backend\Model\Url` implementation class will be used.

#### Parameter configuration inheritance

Parameters configured for a class type are automatically configured for all of its descendant classes. Any descendant can override the parameters configured for its supertype; i.e. the parent class or interface:

<script src="https://gist.github.com/xcomSteveJohnson/8ef9264be06fba085a03.js"></script>

In the preceding example, [`Magento\Backend\Block\Context`]({{ site.mage2000url }}app/code/Magento/Backend/Block/Context.php){:target="_blank"} is a descendant of [`Magento\Framework\View\Element\Context`]({{ site.mage2000url }}lib/internal/Magento/Framework/View/Element/Context.php){:target="_blank"}.

The first entry configures all instances of `Magento\Framework\View\Element\Context` as well as its children to pass in [`Magento\Core\Model\Url`]({{ site.mage2000url }}lib/internal/Magento/Framework/Url.php){:target="_blank"} as `$urlBuilder` in their constructors.

The second entry overrides this and configures all instances of `Magento\Backend\Block\Context` to use [`Magento\Backend\Model\Url`]({{ site.mage2000url }}app/code/Magento/Backend/Model/Url.php){:target="_blank"} as the `$urlBuilder` instead.

#### Object Lifetime management

The main responsibility of the object manager is object creation and wiring, but it can also determine how many instances of that object can exist; e.g. its **lifestyle**.

Magento's object manager supports the following lifestyles:

*	**singleton**(default) - Only one instance of this class exists and it is created at the first request. Subsequent use of the class will use that one instance. The instance is released when the container with which it is registered is disposed.
*	**transient** - A new instance of the class is created every time the class is requested.

The `shared` property determines the lifestyle of both `argument` and `type` configurations.

~~~
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <type name="Magento\Filesystem" shared="false">
        <arguments>
            <argument name="adapter" xsi:type="object" shared="false">Magento\Filesystem\Adapter\Local</argument>
        </arguments>
    </type>
</config>
~~~

In this example `Magento\Filesystem` is configured as non-shared, so all clients will retrieve separate instances of `Magento\Filesystem`. Also, every instance of `Magento\Filesystem` will get separate instance of `$adapter`, because it too is non-shared.

### Injectable and Newable Objects

**Injectable:** Objects that can be obtained through dependency injection. Any object that can be instantiated by the object manager, such as singletons and factories, fall into this category.

**Newable:** Objects that can only be obtained by creating a new class instance every time. Transient objects, such as those that require external input from the user or database, fall into this category. Attempts to obtain these objects using dependency injections will return an undefined object.

> For example a model object such as [`app/code/Magento/User/Model/User.php`]({{ site.mage2000url }}app/code/Magento/Catalog/Model/Product.php){:target="_blank"} cannot be used for dependency injection. You need to provide a product id or explicitly request a new, empty instance of that object, and since this cannot be done in the constructor signature, the object cannot be injected.

### Rules for using dependency injection

* Injectable objects may request dependent objects in their constructors but only if those objects are also injectable.
* If an injectable object needs to produce newable objects, it must ask for a [factory]({{ site.gdeurl }}extension-dev-guide/factories.html) in its constructor since factories are injectable.
* If an injectable object needs to perform some actions on newable object, it must receive that object as a function method argument.
* You can create newable objects in services with object [factories]({{ site.gdeurl }}extension-dev-guide/factories.html) or you can pass them in as method parameters.
* Newable objects should not hold a field reference to an injectable object nor should they request one in their constructor. This is a [Law of Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter){:target="_blank"} violation.

---

**Related Topics**

*	<a href="{{ site.gdeurl }}extension-dev-guide/plugins.html">Plugins</a>
*	<a href="{{ site.gdeurl }}extension-dev-guide/routing.html">Routing</a>
*	<a href="{{ site.gdeurl }}config-guide/bootstrap/magento-bootstrap.html">Magento application initialization and bootstrap</a>
*	<a href="{{ site.gdeurl }}architecture/modules/mod_depend.html">Module dependencies</a>
*	<a href="{{ site.gdeurl }}extension-dev-guide/api-concepts.html">Programming concepts</a>
