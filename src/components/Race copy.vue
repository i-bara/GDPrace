<script setup>

import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

import { ref, onMounted, reactive, watchEffect } from 'vue'

const count = 5;
const length = Array(count).fill().map(() => ref(0));

const width = 640;
const height = 400;
const marginTop = 20;
const marginRight = 20;
const marginBottom = 30;
const marginLeft = 40;

const svg = d3.create("svg")
        .attr("width", width)
        .attr("height", height);

const buttons = []

function b(i) {
    return () => {
        console.log(length.map(x => x.value));
        length[i].value += 10;
    }
}

let xBegin;
let yBegin;
// .attr("transform", `translate(0,${height - marginBottom})`)
let transform = Array(count).fill().map(_ => [0, 0]);
for (let i = 0; i < count; i++) {
    buttons.push(
        svg.append("circle")
        .attr("cx", 150 + 60 * i)
        .attr("cy", 30)
        .attr("r", 12)
        .on("click", b(i))
        .on("mouseover", function(d) {
                d3.select(this).transition().duration(300).style("fill", "deeppink").style("cursor", "pointer").attr("r", 20);
            })
        .on("mouseout", function(d) {
                d3.select(this).transition().duration(300).style("fill", "pink").style("cursor", "default").attr("r", 12);
            }
        )
        .style("fill", d3.color("pink"))
        .call(d3.drag()
            .on("start", function(event) {
                // [xBegin, yBegin] = d3.pointer(event);
                // console.log(xBegin, yBegin);
            })
            .on("drag", function(event) {
                const [x, y] = d3.pointer(event);
                console.log(x, y, xBegin, yBegin, i);
                if (xBegin != undefined && yBegin != undefined) {
                    transform[i][0] += x - xBegin;
                    transform[i][1] += y - yBegin;
                }
                [xBegin, yBegin] = [x, y];
                d3.select(this).attr("transform", `translate(${transform[i][0]}, ${transform[i][1]})`).transition().duration(100).style("fill", "deeppink").style("cursor", "pointer").attr("r", 20)
            })
            .on("end", function() {
                xBegin = undefined;
                yBegin = undefined;
            })
        )
    );
    // buttons[i]
}

onMounted(() => {
    // Declare the chart dimensions and margins.
    

    // Declare the x (horizontal position) scale.
    const x = d3.scaleLinear()
        .domain([0, 100])
        .range([marginLeft, width - marginRight]);

    // Declare the y (vertical position) scale.
    const y = d3.scaleLinear()
        .domain([0, 100])
        .range([height - marginBottom, marginTop]);

    // Create the SVG container.

    const rects = [];
    for (let i = 0; i < count; i++) {
        rects.push(svg.append("rect")
            .attr("x", x(10 + 20 * i))
            .attr("y", y(0))
            .attr("width", x(10 + 20 * i) - x(20 * i))
            .attr("height", y(0) - y(0))
            .style("fill", d3.color("steelblue")));
    }

    // const rect = svg.append("rect")
    //   .attr("x", 150)
    //   .attr("y", 370)
    //   .attr("width", 50)
    //   .attr("height", 0)
    //   .style("fill", d3.color("steelblue"));

    // Add the x-axis.
    let gx = reactive(svg.append("g")
        .attr("transform", `translate(0,${height - marginBottom})`)
        .call(d3.axisBottom(x)));

    // Add the y-axis.
    let gy = reactive(svg.append("g")
        .attr("transform", `translate(${marginLeft},0)`)
        .call(d3.axisLeft(y)));

    const div = document.createElement('div');

    // div.append(svg);

    // Append the SVG element.
    console.log(svg.html());

    console.log(document.getElementById("container"));

    document.getElementById("container").append(svg.node());

    const y2 = d3.scaleLinear()
        .domain([0, 50])
        .range([height - marginBottom, marginTop]);

    console.log(gy);

    gy.transition()
    .duration(3000)
    .call(d3.axisLeft(y2))
    .transition()
    .duration(3000)
    .call(d3.axisLeft(y));

    // watchEffect(() => {
    //     rect.transition().duration(1000).attr("y", 370 - length.value).attr("height", length[0].value);
    // })

    for (let i = 0; i < count; i++) {
        
        watchEffect(() => {
            console.log(y(length[i].value));
            rects[i].transition().duration(1000).attr("y", y(length[i].value)).attr("height", y(0) - y(length[i].value));
        })
    }
})



function a() {
    console.log(gy);

    const width = 640;
    const height = 400;
    const marginTop = 20;
    const marginRight = 20;
    const marginBottom = 30;
    const marginLeft = 40;

    // Declare the x (horizontal position) scale.
    const x = d3.scaleUtc()
        .domain([new Date("2023-01-01"), new Date("2024-01-01")])
        .range([marginLeft, width - marginRight]);

    // Declare the y (vertical position) scale.
    const y = d3.scaleLinear()
        .domain([0, 100])
        .range([height - marginBottom, marginTop]);

    const y2 = d3.scaleLinear()
        .domain([0, 50])
        .range([height - marginBottom, marginTop]);

    gy.transition()
    .duration(3000)
    .call(d3.axisLeft(y2))
    .transition()
    .duration(3000)
    .call(d3.axisLeft(y));
}

</script>

<template>
    <div id="container"/>
    <button @click="b">a</button>
    <!-- <span v-html="svg.node().html()"/> -->
</template>
