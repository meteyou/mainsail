<template>
    <div id="historyAllPrintStatus" style="height: 250px; width: 100%;"></div>
</template>

<script>
import { mapState } from 'vuex'
import * as echarts from 'echarts'

export default {
    components: {

    },
    data: function() {
        return {
            chart : null,
            chartOptions: {
                darkMode: true,
                animation: false,
                grid: {
                    top: 0,
                    right: 0,
                    bottom: 0,
                    left: 0,
                },
                tooltip: {
                    trigger: 'item'
                },
                series: [{
                    type: 'pie',
                    data: [],
                    radius: '50%',
                }]

            },
        }
    },
    computed: {
        ...mapState({
            intervalChartUpdate: state => state.gui.tempchart.intervalChartUpdate,
            intervalDatasetUpdate: state => state.gui.tempchart.intervalDatasetUpdate,
            boolTempchart: state => state.gui.dashboard.boolTempchart,
        }),
        getAllPrintStatusArray: {
            get: function() {
                return this.$store.getters["server/history/getAllPrintStatusArray"]
            }
        },
    },
    methods: {
        createChart() {
            if (document.getElementById("historyAllPrintStatus") && this.chart === null) {
                this.chart = echarts.init(document.getElementById("historyAllPrintStatus"), null, { renderer: 'svg' })
                this.updateChart()
                window.console.log("create AllPrintStatus")
            } else
                setTimeout(() => {
                    this.createChart()
                }, 500)
        },
        updateChart() {
            if (this.chart) {
                const chartOptions = { series: this.chartOptions.series }
                chartOptions.series[0].data = this.filamentUsageArray
                this.chart.setOption(chartOptions)
                this.chart.resize()
                window.console.log("update AllPrintStatus")
            }
        },
    },
    created() {
        window.addEventListener('resize', () => {
            if (this.chart) this.chart.resize()
        })
    },
    mounted: function() {
        this.createChart()
    },
    watch: {
        getAllPrintStatusArray: {
            deep: true,
            handler() {
                this.updateChart()
            }
        },
    }
}
</script>