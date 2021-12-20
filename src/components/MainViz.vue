<template lang="pug">
#main-div.d-flex.flex-column.justify-content-center.align-items-center
    .svg-container.mx-auto.d-flex.flex-column.bg-light.px-2.border.border-success.border-3
        .header.text-center.border-bottom.border-success.border-3.py-3
            h1.text-success.text-opacity-85 Population Density around the World
            p.text-success.mt-2.text-opacity-75 Top 10 countries with highest population density in sq.km
            button.btn.btn-primary.bg-gradient.mt-2(@click="sortViz") Reorder
        svg#main-svg.flex-grow-1.mt-3
</template>

<script>
import * as d3 from "d3";
import popDensity from "../assets/prj_popdensity";

export default {
    data(){
        return{
            selOrder: "descending"
        }
    },
    methods:{
        sortViz(){
            this.selOrder = this.selOrder === "descending" ? "ascending" : "descending"
            this.makeViz();
        },
        makeViz(){
            const svgWidth = d3.select("#main-svg").node().clientWidth;
            const svgHeight = d3.select("#main-svg").node().clientHeight;
            
            let densityList = [];
            popDensity.forEach((d)=>{
                densityList.push(d.density);
            })
           
           const popMax = Math.max(...densityList);
           
            const xPopScale = d3.scaleLinear()
                .domain([0, popMax])
                .range([0, svgWidth])
            
            const colorScale = d3.scaleLinear()
                .domain([0, popMax])
                .range(["#C0D8CF","#37584C"])

            const yPopScale = d3.scaleLinear()
                .domain([0, popDensity.length - 1])
                .range([0, svgHeight - 65])
            
            let selOrder = this.selOrder;
            const rects = d3.select("svg")
                .selectAll("rect")
                .data(popDensity)
                .join("rect")
                .sort((a, b) => {
                    return selOrder === "descending" ? b.density - a.density : a.density - b.density
                })
                .attr("x", "0")
                .attr("y", (d, i) => {
                    return 5 + yPopScale(i)
                })
                .attr("width", d => xPopScale(d.density))
                .attr("height", svgHeight / densityList.length - 5)
                .attr("fill", d => colorScale(d.density))
                .attr("rx", "5")
                .attr("ry", "5")
            
            const texts = d3.select("svg")
                .selectAll("text")
                .data(popDensity)
                .join("text")
                .sort((a, b) => {
                    return selOrder === "descending" ? b.density - a.density : a.density - b.density
                })
                .attr("x", "5")
                .attr("y", (d, i) => {
                    return (5 + yPopScale(i)) + (svgHeight / densityList.length) - 10
                })
                .text(d => `${d.country}: ${d.density}`)
        }
    },
    mounted(){
        this.makeViz();
    }
}
</script>

<style scoped>
*{
    margin: 0;
}
#main-div{
    height: 100vh;
}

.svg-container{
    height: 80%;
    width: 80%;
}
</style>