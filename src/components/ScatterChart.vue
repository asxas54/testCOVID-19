<template>
    <div style="width:540px; height:400px;border: 1px solid lightgray;">
        <h3 style="text-align: center;">XXX日全国各省政府响应指数和确诊人数的关系</h3>
        <div id="scatterPlot" style="width: 100%; height: 300px;">
            <!-- 定义散点图的容器 -->
            <div id="scatter-plot-container"></div>
        </div>
        <div style="width: 100%; text-align: center;">
            <span style="display: inline-block; width: 10px; height: 10px; background-color: blue;"></span>
            政府响应指数
            <span
                style="display: inline-block; width: 10px; height: 10px; background-color: red; margin-left: 20px;"></span>
            确诊人数
        </div>
    </div>
</template>

<script>
window.addEventListener('load', function () {
    // 定义图表边距
    var margin = { top: 20, right: 20, bottom: 30, left: 40 },
        width = 540 - margin.left - margin.right,
        height = 300 - margin.top - margin.bottom;
    // 定义x轴比例尺
    var x = d3.scaleLinear().range([0, width]);
    // 定义y轴比例尺
    var y = d3.scaleLinear().range([height, 0]);

    // 定义x轴坐标轴
    var xAxis = d3.axisBottom(x).ticks(9);

    // 定义y轴坐标轴
    var yAxis = d3.axisLeft(y).ticks(9);
    var jsonData = [];
    getdata();
    function getdata() {
        $.ajax({
            type: 'GET',
            url: "src/data/OxCGRT_CHN_latest - 2-6.csv",
            dataType: 'text',
            async: false,
            success: function (data) {
                jsonData = $.csv.toObjects(data);
            },
            error: function (e) {
                alert('An error occurred while processing API calls');
                console.log("API call Failed: ", e);
            },
        });
    }
    console.log(jsonData);
    // 创建SVG元素
    var svg = d3
        .select("#scatter-plot-container")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

    for (var a = 0; a < jsonData.length; a++) {
        x.domain(d3.extent(jsonData[a], function (d) { return d.StringencyIndex_Average_ForDisplay; })).nice();
        y.domain(d3.extent(jsonData[a], function (d) { return d.GovernmentResponseIndex_Average_ForDisplay; })).nice();
        

    }
console.log(jsonData[2000]);
    svg.append("g").call(xAxis).attr("transform", "translate(0," + height + ")");
    svg.append("g").call(yAxis);

    svg
        .selectAll(".dot")
        .data(jsonData)
        .enter()
        .append("circle")
        .attr("class", "dot")
        .attr("cx", function (d) { return x(d.x); })
        .attr("cy", function (d) { return y(d.y); })
        .attr("r", 2);


    svg.append("g")
        .attr("class", "grid")
        .attr("transform", "translate(0," + height + ")")
        .call(xAxis
            .tickSize(-height)
            .tickFormat("")
        )
        .selectAll(".tick")
        .classed("tick--minor", function (d) {
            return d;
        });

    svg.append("g")
        .attr("class", "grid")
        .call(yAxis
            .tickSize(-width)
            .tickFormat("")
        )
        .selectAll(".tick")
        .classed("tick--minor", function (d) {
            return d;
        });
})

</script>

<style>
/* 定义散点图容器的样式 */
#scatter-plot-container {
    margin: 0 auto;
}

h1 {
    font-size: 24px;
    font-weight: bold;
    text-align: center;
    margin-top: 20px;
}

/* 定义坐标轴的样式 */
.axis {
    font-size: 12px;
}

/* 定义坐标轴的线条样式 */
.axis path,
.axis line {
    fill: none;
    /* 填充 */
    stroke: #000;
    /* 线条颜色 */
    shape-rendering: crispEdges;
    /* 边缘修整 */
}

.axis line {
    stroke: #ddd;
    stroke-dasharray: 2 2;
}

/* 定义散点的样式 */
.dot {
    fill: blue;
    /* 填充颜色 */
    stroke: blue;
    /* 线条颜色 */
    stroke-width: 1.5px;
    /* 线条宽度 */
}
</style>