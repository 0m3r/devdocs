---
layout: default
group: mtf-guide
subgroup: 40_Approach
title: Create a test in the Magento Testing Framework
menu_title: CREATE A TEST
menu_node: parent
github_link: mtf/create_test.md
---

## Preface

The Magento testing framework (MTF) works with functional tests only. Functional tests check that an application meets business requirements. These requirements usually are collected in the functional specifications that describe expected behaviour of the application.

Tests usually cover functionality of a business entity. A goal is to find discrepancies between expected and real behaviour of the product.
Magento provides functional tests in the `<magento2>/dev/tests/functional/` directory. In this guide, we call them [out-of-the-box tests][]. You can use them to test the default Magento functionality. Also, you can create a test [extending from the out-of-the-box test][], or [create your own functional tests][].

## Related topics

[Out-of-the-box test][]

[New functional test][]

[Create a new test][]

<!-- LINK DEFINITIONS -->

[out-of-the-box tests]: {{site.gdeurl}}mtf/create_test/out-of-the-box.html
[Out-of-the-box test]: {{site.gdeurl}}mtf/create_test/out-of-the-box.html
[extending from the out-of-the-box test]: {{site.gdeurl}}mtf/create_test/new_test.html#extending-oob-test
[create your own functional tests]: {{site.gdeurl}}mtf/create_test/new_test.html#create-test
[New functional test]: {{site.gdeurl}}mtf/create_test/new_test.html
[Create a new test]: {{site.gdeurl}}mtf/create_test.html