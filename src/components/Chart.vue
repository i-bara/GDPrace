<script setup>

import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

import { ref, onMounted, reactive, watchEffect } from 'vue'

let gx, gy = reactive(null);

let yMax = ref(100);

onMounted(() => {
    // Declare the chart dimensions and margins.
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

    // Create the SVG container.


    const svg = d3.create("svg")
        .attr("width", width)
        .attr("height", height);

    // Add the x-axis.
    gx = reactive(svg.append("g")
        .attr("transform", `translate(0,${height - marginBottom})`)
        .call(d3.axisBottom(x)));

    // Add the y-axis.
    gy = reactive(svg.append("g")
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

    watchEffect(() => {
        let y = d3.scaleLinear()
        .domain([0, yMax.value])
        .range([height - marginBottom, marginTop]);
        gy.transition()
        .duration(1000)
        // .call(d3.axisLeft(y));
        .attr("transform", `translate(${marginLeft},${yMax.value})`);
        console.log(yMax.value);
    }, { immediate: false })
})

function b() {
    yMax.value += 100;
}

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
