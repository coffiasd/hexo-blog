---
title: how to write a hackathon readme
date: 2023-02-23 23:36:21
tags:
  - hackathon
categories:
  - hackathon
---

## overview

A hackathon is an event where developers, designers, and other tech enthusiasts come together to collaborate on a project. At the end of the event, all teams present their projects to a panel of judges. A hackathon readme document is a crucial part of presenting your project. It provides details about your project, how to use it, and how to contribute to it. In this blog post, we will discuss how to write a hackathon readme document that will help you present your project effectively.

## why we need a readme document

The purpose of writing a README document is to provide information about a project or software. It should include instructions on how to install and use the software, as well as any dependencies or requirements. The document should also provide an overview of the project and its goals, as well as any relevant background information. Additionally, it should cover any edge cases or potential issues that users might encounter, and provide instructions on how to address them. Finally, it should include information on how to contribute to the project, as well as any testing or quality assurance protocols that are in place.

## what should be included in readme document

### live links

After the entire project is finished,we should deploy it on the server. Thus hackathon judgers can review our project by click the link.Additionally we should upload a video about how to use it to youtube.Here is the example:

- [front-end]()
- [youtube]()

### directory tree

Directory tree gives a quick view of our whole project.Hakcathon judgers can quickly find what they want from the directory tree.And it is quite easy to generate the directory tree by using linux command:

> tree -L {max-depth}

Here goes the output of this command
!["tree"](images/readme-tree.png)

### flowchart

A project flowchart is a visual representation of the sequence of steps in a project. It helps in identifying process inefficiencies, improving communication, and ensuring everyone involved in the project is on the same page.I recommend to use ["miro"]("https://miro.com/") to draw a flowchart. Miro is the online collaborative whiteboard platform that enables distributed teams to work effectively together, from brainstorming with digital sticky notes to planning and managing agile workflows.
Here goes a example generate with miro:
!["flowchart"](images/Hyperlane-Flowchart.jpg)

### install

The install command is depends on the teck stack you use.Let us say you building with react and node.We have to install all dependencies before we run the server.

```shell
npm install
npm run dev
```

### api document

API stands for Application Programming Interface. It is a set of protocols, routines, and tools for building software and applications. Documentation is an essential part of the development process because it enables developers to understand the functionality of the API.

### tech stack

When it comes to building a software project, one of the most important decisions you'll make is choosing the right tech stack. A tech stack is the combination of technologies and programming languages used to build a software application. It’s important to choose the right tech stack because it can affect the performance, scalability, and maintainability of your project.We can also list the teck stack in our readme document like this:

- next.js
- tailwindcss
- solidity
- uniswap v3 protocol

### reference

References are important for several reasons. First, they provide evidence to support your arguments, which adds credibility to your work. Second, references allow readers to verify the information you have presented. Third, references demonstrate that you have done your research and are knowledgeable about your topic. Finally, they show that you are giving credit to the original authors for their ideas and work.

- [1] Hyperlane Docs:<https://docs.hyperlane.xyz/hyperlane-docs/introduction/readme>
- [2] Uniswap V3 Docs:<https://docs.uniswap.org/sdk/v3/overview>
- [3] Hardhat:<https://hardhat.org/>

### license

A code license is a legal agreement that outlines the terms and conditions for the use, distribution, and modification of a piece of code.
SPDX short identifier: MIT
