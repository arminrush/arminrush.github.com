---
title: SOLID principles
layout: post
category: Java
splitor: <!--more-->
---

[SOLID](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)原则对于在使用面向对象语言的开发人员来说，是非常熟悉的。遵循SOLID原则，意在于帮助写出整洁、可维护、可扩展的代码。

SOLID单词其实是五个原则的首字母缩写

 * S: Single responsibility principle 单一职责原则
 * O: Open/closed principles 开闭原则
 * L: Liskov substitution principle  里式替换原则
 * I: Interface segregation principle 接口隔离原则
 * D: Dependency inversion principle  依赖反转原则

<!--more-->

## Single responsibility principle 单一职责原则

一个类或者模块，只有一个职责，只做一件事情。同理得到，一类或或者模块只有一个原因需要修改。

## Open/closed principles 开闭原则

对扩展开发，对修改关闭。

## Liskov substitution principle  里式替换原则

一个对象可以被任何它的子类对象所替换。

## Interface segregation principle 接口隔离原则

客户端不应该依赖于他们不适用的方法。该原则提倡将大的接口拆分为多个小的接口，从而帮助客户端减少对不适用方法的依赖。

当拆分后为更细粒度的接口后，耦合更少，使得代码更容易重构、修改和部署。

## Dependency inversion principle  依赖反转原则

Dependency inversion principle(SIP)提倡你写代码依赖于抽象，而不是依赖于具体的实现。
