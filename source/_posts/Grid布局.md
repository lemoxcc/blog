---
title: Grid布局
date: 2021-03-16 15:23:41
update: 2021-3-02 15:47:34
tags:
  - CSS
  - Grid
categories:
  - CSS
description: 'CSS Grid'
keywords: 'CSS,Grid,响应式'
top_img: /assert/index-image.png
cover: false
---

## <center>Grid布局</center>

CSS Grid是一个灵活的布局系统，用于创建基于网格的设计。它允许您在网格中的行和列中排列元素，而不必依赖传统的float和clear属性。CSS Grid布局是CSS3的一部分，并且是现代Web设计的标准。本文将介绍CSS Grid布局，包括它的基础知识和如何使用它来构建响应式网站

# 基本概念

在开始使用CSS Grid之前，我们需要了解一些基本概念。CSS Grid由一个网格容器和网格项组成。网格容器是一个HTML元素，其中包含了一组网格项。网格项是网格容器的直接子元素，并在网格中占据一个或多个单元格

## 网格容器

要创建一个网格布局，首先要定义一个网格容器。您可以使用CSS的display属性来定义一个元素为网格容器。例如：

```CSS
.grid-container {
  display: grid;
}
```

在这个示例中，我们将一个元素命名为“grid-container”，并使用CSS的display属性将其定义为一个网格容器。这意味着该元素内的所有子元素都可以成为网格项

## 网格项

要将一个元素定义为网格项，我们需要将其直接放置在网格容器内。可以使用CSS的grid-column和grid-row属性来定义网格项在网格中的位置。例如：

```CSS
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 10px;
}

.grid-item {
  grid-column: 1 / 3;
  grid-row: 1;
}
```

在这个示例中，我们首先定义了一个具有三个列的网格，每个列的宽度都相同。然后，我们将一个元素命名为“grid-item”，并使用CSS的grid-column和grid-row属性将其放置在网格的第一行和前两列

## 网格行和列

CSS Grid允许您自定义网格的行和列。使用grid-template-columns和grid-template-rows属性可以定义网格的列和行。例如：

```CSS
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);
  grid-gap: 10px;
}
```

在这个示例中，我们使用grid-template-columns和grid-template-rows属性定义了一个具有三行和三列的网格。每个列和行的宽度和高度都相同，并且使用repeat()函数和1fr单位定义。网格项将占据一个或多个单元格，具体取决于它们在grid-column和grid-row属性中指定的位置

## 网格模板

CSS Grid的网格模板定义了网格的行和列。可以使用grid-template-areas和grid-template-columns属性定义网格模板。例如：

```CSS
.grid-container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  grid-template-rows: auto;
  grid-template-areas: "header header header" "sidebar content content" "footer footer footer";
}

.header {
  grid-area: header;
}

.sidebar {
  grid-area: sidebar;
}

.content {
  grid-area: content;
}

.footer {
  grid-area: footer;
}
```

在这个示例中，我们使用grid-template-areas属性定义了一个网格模板，其中网格项被命名为“header”，“sidebar”，“content”和“footer”。每个网格项的位置由网格模板中的区域定义。网格模板中的区域由多个单元格组成，可以通过指定单元格的名称来创建。在我们的示例中，每个区域都由一个单独的名称组成。然后，我们使用grid-area属性将每个网格项放置在网格模板中的相应位置

## 网格行和列的间距

可以使用grid-gap属性在网格行和列之间设置间距。grid-gap属性定义了网格的行和列之间的空间大小。例如：

```CSS
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 10px;
}
```

在这个示例中，我们使用grid-gap属性定义了网格的行和列之间的间距为10像素。这意味着网格项之间有10像素的空间

## 自适应网格

CSS Grid允许您创建自适应网格，这意味着您可以让网格自动调整大小以适应不同的屏幕尺寸和设备。可以使用CSS的media query和grid-template-columns属性来实现自适应网格。例如：

```CSS
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-gap: 10px;
}
```

在这个示例中，我们使用grid-template-columns属性定义了一个自适应网格。minmax()函数定义了网格列的最小和最大宽度。auto-fit关键字表示网格应该自适应屏幕大小，而1fr单位表示网格列的剩余空间应该分配给所有列。这意味着网格将自动调整大小以适应不同的屏幕尺寸，并在屏幕较小时缩小网格列的宽度

# 响应式布局

CSS Grid布局使创建响应式布局变得非常容易。响应式布局是指可以在不同的设备和屏幕尺寸上自适应的布局。可以使用CSS的media query和CSS Grid布局来创建响应式布局。以下是一些创建响应式布局的技巧：

## 使用自适应网格

如上所述，可以使用CSS的media query和grid-template-columns属性创建自适应网格。这意味着网格将自动调整大小以适应不同的屏幕尺寸和设备。这是一种快速和简单的方法来创建响应式布局

## 使用媒体查询

另一种常见的方法是使用CSS的media query来针对不同的设备和屏幕尺寸应用不同的样式。以下是一个使用media query创建响应式布局的示例：

```CSS
.grid-container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  grid-template-rows: auto;
  grid-template-areas:
    "header header header"
    "sidebar content content"
    "footer footer footer";
}

@media only screen and (max-width: 768px) {
  .grid-container {
    grid-template-columns: 1fr;
    grid-template-areas:
      "header"
      "sidebar"
      "content"
      "footer";
  }
}
```

在这个示例中，我们使用media query在屏幕宽度小于或等于768像素时改变网格的布局。在这种情况下，我们将网格模板更改为只有一列，并将每个网格项放置在单独的行中

## 使用flexbox和网格

CSS Grid布局与flexbox布局非常兼容，并且可以一起使用来创建更复杂的响应式布局。例如，在以下示例中，我们使用CSS Grid布局和flexbox布局来创建一个具有三列的响应式布局：

```HTML
<div class="container">
  <div class="sidebar">
    <p>Sidebar content</p>
  </div>
  <div class="content">
    <p>Main content</p>
  </div>
  <div class="extra">
    <p>Extra content</p>
  </div>
</div>
```

```CSS
.container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-gap: 20px;
}

.sidebar {
  background-color: #ddd;
  padding: 20px;
}

.content {
  background-color: #eee;
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.extra {
  background-color: #ddd;
  padding: 20px;
}
```

在这个示例中，我们使用了CSS Grid布局来创建一个自适应网格，该网格有三列，每个网格项的最小宽度为200像素。我们还使用了flexbox布局来在中心显示主要内容。在屏幕较小的情况下，网格将自动缩小以适应屏幕大小，并且主要内容将在垂直方向上居中

# 总结

CSS Grid布局是一种强大的布局技术，可以使网站和应用程序更易于布局和排版。它提供了一个灵活的网格系统，可以轻松地处理各种不同的布局需求。使用CSS Grid布局，您可以轻松地创建响应式布局，这意味着您的网站将适应不同的屏幕尺寸和设备类型。通过使用CSS Grid布局，您可以大大减少代码的复杂性，并以一种更直观的方式组织您的布局

在使用CSS Grid布局时，有一些最佳实践和技巧可以使您的工作更轻松和高效。例如，始终使用命名网格区域来更清晰地定义您的布局，以及使用自动重复网格模板来避免在更改布局时重复输入代码。此外，使用网格行列间距和网格项间距可以增加布局的可读性和可维护性

最后，记住在设计响应式布局时使用媒体查询和flexbox与CSS Grid布局一起使用。这些技术可以让您更好地控制网站在不同设备和屏幕上的外观和布局
