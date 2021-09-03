---
title: NoCodeFactory(in modeling process)
layout: page
---

## Inspiration

Management systems mean quite a lot to companies in manufacturing industry. 
However, the managing systems available depend highly on forms and it has some
threshold to study on. Alse, since the process of developing depends on skillful 
programmers, the price is always unacceptable for small/medium sized companies.
So we wanted to do something to lower or remove this threshold, both for developer and user.

## What it does

* This management system implements a lot of modules, including craft management, 
  inventory management, manufacturing management, demand analysis, purchasing management etc.
* This management provides strategies for smart manufacturing, smart grounding and smart purchasing. 
* This management system provides graphical user interface in most modules, which makes
it independent of forms.
* This management system depends on components. The template of components are designed
in code but they are implemented in the application by users or no-code-developers, 
  which lowers the developing threshold of a customized management system.
* This management system can be combined with most devices like smartphone or RFIDs.

## How we built it

* This project is still in modeling process. 
* We take an appending strategy instead of a writing strategy so that The list of component-templates
  will be larger and larger as we append new component template to it. Users can pick up
  what they want when implementing a component.
* We will use vue as the frontend framework, springboot as the backend framework,
  mybatis as our persistence framework and postgres as our database.
* We used edrawmax to draw flowcharts, UMLs and database tables.
* We went to our customer's company and did a lot of research so that our design can
fit more to the application scenarios.

## Challenge we ran into

* The design in demand analysis module is very clever. We maintain formulas
whose input and output are both maintained in our material module. We use these formulas
  to split our orders into checking out tasks, manufacturing tasks and purchasing tasks.
  The logic is, if our on-stock inventory satisfies the demand of a specified material,
  we produce a pre-check-out task and occupy the inventory. Otherwise, we produce a 
  pre-check-out task and occupy the existed inventory and choose to manufacture it or to purchase it.
  These manufacturing tasks or purchasing tasks are bounded to the pre-check-out task
  and the products produced by them will be directly allocated to the pre-check-out task
  when they are put in storage. 
  
* We want to recover the tree structure after we reenter
  the page, so every time after an operation is taken by user, we store the current 
  tree strcture into database.
  
* Also, some materials can either be manufactured or purchased. If we choose to manufacture
it ourselves, we should care about the materials which are input of the formula. But if
  we choose to purchase it, we don't need to care about any of these. So if we want the users
  to choose a method to get these materials, we should make sure that the subtasks are
  invalidated when the user chooses purchasing and validate them when the user chooses
  manufacturing(both visually and logically), so we store the dependency of different 
  nodes in the tree and store their activating status. Also, for a material, we should
  record how the user will get it(purchasing, manufacturing or None)
  
* It's a problem what we should do when the user reanalyzes. We should clean any
brand of the pre-check-out task related to current tree and then delete these pre-check-out tasks.
  After that, we can analyze the order as usual.
  
  