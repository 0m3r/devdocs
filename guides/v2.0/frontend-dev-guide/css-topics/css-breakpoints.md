---
layout: default
group: fedg
subgroup: D_CSS
title: Add a custom breakpoint
menu_order: 7
menu_title: Add a custom breakpoint
github_link: frontend-dev-guide/css-topics/css-breakpoints.md
---

<h2>What's in this topic</h2>

Breakpoints are used in stylesheets to set up the screen width at which the design switches from the mobile to the desktop version. Magento out of the box themes implement a list of [default breakpoints]({{site.gdeurl}}frontend-dev-guide/responsive-web-design/rwd_css.html#fedg_rwd_css_break). This topic describes how to set breakpoints in your theme. 


* TOC
{:toc}

## Overview
To add a custom breakpoint to your theme you need to do the following:

1. Define a variable for the new breakpoint.
2. Override the library `_responsive.less` file and add the new rule for the new breakpoint. 
3. Implement the screen changes for the new breakpoint.

## Add a new breakpoint variable

In your custom theme directory, add a `/web/css/source/variables.less` in one of the following ways:

- if your theme [inherits]({{site.gdeurl}}frontend-dev-guide/themes/theme-inherit.html) from the other, then copy the parent's `variables.less`.
- if your theme is a standalone one, add a new empty file.

In your `variable.less`, add the variable for your new breakpoint.

For example:

    @your__breakpoint: 1280px;

For varibles naming rules see [Less coding standards](http://devdocs.magento.com/guides/v2.0/coding-standards/code-standard-less.html#variables).

## Override `_responsive.less` from the library

The approach implmented in the Magento UI library is based on recursive call of `.media-width()` mixin defined anywhere in project but invoked in one place in `lib/web/css/source/lib/_responsive.less`. 

To add a new breakpoint we need to edit `.media-width()` mixin by adding the appropriate rule there. Tha is why we need to override it in our theme. 

If the parent theme already overrides the library file, that is the `<your_parent_theme_dir>/web/css/source/lib/_responsive.less` file exists, copy it to `<your_theme_dir>/web/css/source/lib/`.

If the file in the parent theme does not exist, or there is no parent theme, copy `/lib/web/css/source/lib/_responsive.less` to `<your_theme_dir>/web/css/source/lib/`.

In your `_responsive.less`, add the `.media-width` mixin rule for your breakpoint in the correspoding section (desktop or mobile, depending on the type of breakpoint you add).

Example:

    & when (@media-target = 'desktop'), (@media-target = 'all') {
    …
        @media all and (min-width: @your__breakpoint) {
            .media-width('min', @your__breakpoint);
        }
    }

You can find information about the `_responsive.less` file in the generated Magento UI library documentation available in a HTML view in the following location in your Magento installation: `<your_Magento_installation>/pub/static/frontend/Magento/blank/en_US/css/docs/responsive.html`.

## Declare the screen changes for the breakpoint

In one of your theme `.less` files, add a new `.media-width()` mixin call and implement the required screen changes there.

Example:

    .media-width(@extremum, @break) when (@extremum = 'min') and (@break = @your__breakpoint) {
        //  Customisation for @your__breakpoint breakpoint
    }
