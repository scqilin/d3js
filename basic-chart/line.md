# d3.js基本折线图绘制教程

本教程将介绍如何使用 d3.js 创建一个基本的折线图。

## 准备工作

在开始之前，需要引入 d3.js 库，并准备好包含数据的数组、画布大小、填充和比例尺等元素。具体代码如下：

```js
// 数据集合
const dataset = [10, 20, 30, 40, 33, 24, 12, 5];

// 设置画布大小和填充
const padding = 30;
const svgWidth = 600;
const svgHeight = 400;

// 定义x轴和y轴的比例尺（可视区域）
const xScale = d3.scaleBand()
    .domain(d3.range(dataset.length))
    .range([padding, svgWidth - padding])
    .padding(0.3);

const yScale = d3.scaleLinear()
    .domain([0, d3.max(dataset)])
    .range([svgHeight - padding, padding]);

// 创建并设置SVG画布大小以及边框
const svg = d3.select("body")
    .append("svg")
    .attr("width", svgWidth)
    .attr("height", svgHeight)
    .style('border', '1px solid #999999');
```

上述代码定义了必须的元素和比例尺（即x轴和y轴），并创建了SVG画布。

## 绘制折线

在创建比例尺后，我们需要使用 `d3.line()` 方法绘制折线。这个函数需要两个参数：`.x()` 和 `.y()`。

```js
// 创建一个折线生成器函数
const line = d3.line()
    .x((d, i) => xScale(i))
    .y(d => yScale(d))
    .curve(d3.curveCardinal);
```

以上代码创建了一个名为 `line` 的折线生成器函数。具体来说，`.x()` 方法告诉 D3.js 在哪里绘制折线，本例中从左侧开始绘制，通过传输每个数据点的索引值来建立所需的坐标系。`.y()` 方法确定在 y 轴上的位置，也可以用于计算每个数据点的 y 值。

`d3.curveCardinal` 是一种曲线类型，可以使折线更加平滑和自然。

接下来，将折线添加到画布上：

```js
// 添加路径元素到SVG画布上
svg.append("path")
    .datum(dataset)
    .attr("class", "line")
    .attr("d", line)
    .attr("fill", "none")
    .attr("stroke", "#69b3a2")
    .attr("stroke-width", "3px");
```

当输入参数为数据集时（即调用`datum()`方法），该路径元素将根据数据生成的坐标点绘制出一条连接各点的折线。

## 添加坐标轴

最后一步是添加 x 和 y 坐标轴，展示每个数据点的精确位置。在这里，我们使用 `d3.axisBottom()` 来创建 x 轴，使用 `d3.axisLeft()` 来创建 y 轴，并将它们添加到画布上。

```js
// 创建x轴和y轴
const xAxis = d3.axisBottom(xScale);
const yAxis = d3.axisLeft(yScale);

// 添加x轴 
svg.append("g")
    .attr("class", "x-axis")
    .attr("transform", "translate(0," + (svgHeight - padding) + ")")
    .call(xAxis);

// 添加y轴
svg.append("g")
    .attr("class", "y-axis")
    .attr("transform", "translate(" + padding + ",0)")
    .call(yAxis);
```

`.padding(0)`和`.paddingInner(1)`是D3.js中用于设置缩放比例尺的内部间距的方法。

在使用`d3.scaleBand()`创建一个序列比例尺时，可以使用`.padding()`方法来设置相邻的分组之间的间隔。该方法接受一个数字参数，表示所需的间隔大小（以像素为单位）。如果没有指定参数，则默认间隔大小为0。例如，`.padding(10)`将在每个带宽上添加10px的空白间隔。

在使用`.paddingInner()`方法时，参数表示相邻分组之间的内部间隔占一个组宽度的比例。例如，`.paddingInner(0.5)`将使每个分组之间保持50%的间距。通常情况下，`.paddingInner()`的值应低于1。如果不指定此方法，则默认内部间隔比例为0. 引入`.paddingInner()`是因为在一些情况下，`.padding()`已经足够灵活控制了比例尺的间距，而 `.paddingInner()` 方法更多的是作为 `.padding()` 的补充而存在。

因此，如果我们在使用 `d3.scaleBand()` 时想要设置两个相邻分组之间的间距，则可以使用`.padding()`方法来设置外部间距，`.paddingInner()`方法来设置内部间距，例如：`.padding(20).paddingInner(0.2)`将使所有分组之间的距离为20px，而每个内部分组之间的距离占一个组宽度的20%。

在以上代码中，我们使用了`.call()`方法来将坐标轴添加到SVG画布上。由于SVG坐标轴是一个单独的元素，因此我们必须使用`<g>`元素来对其进行分组。
