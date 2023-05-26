<template>
    <div v-show="chart">
        <canvas id="line-chart"></canvas>
    </div>
</template>
  
<script>
import axios from 'axios';
import Chart from 'chart.js';

export default {
    name: 'LineChart',
    data() {
        return {
            chart: null,
            city: '',
            dates: [],
            temps: [],
            loading: false,
            errored: false
        }
    },
    mounted() {
        this.setChart();
    },
    methods: {
        setChart() {
            this.loading = true;

            if (this.chart != null) {
                this.chart.destroy();
            }

            axios
                .get("https://api.openweathermap.org/data/2.5/forecast?units=metric", {
                    params: {
                        q: this.city,
                        appid: "9f2b2e8cd60541bbd9ad927b1a5cda93"
                    }
                })
                .then(response => {
                    this.dates = response.data.list.map(list => {
                        return list.dt_txt;
                    });

                    this.temps = response.data.list.map(list => {
                        return list.main.temp;
                    });

                    const ctx = document.getElementById('line-chart');
                    this.chart = new Chart(ctx, {
                        type: "line",
                        data: {
                            labels: this.dates,
                            datasets: [
                                {
                                    label: "Avg. Temp",
                                    backgroundColor: "rgba(54, 162, 235, 0.5)",
                                    borderColor: "rgb(54, 162, 235)",
                                    fill: false,
                                    data: this.temps
                                }
                            ]
                        },
                        options: {
                            title: {
                                display: false,
                            },
                            tooltips: {
                                callbacks: {
                                    label: function (tooltipItem, data) {
                                        var label =
                                            data.datasets[tooltipItem.datasetIndex].label || "";

                                        if (label) {
                                            label += ": ";
                                        }

                                        label += Math.floor(tooltipItem.yLabel);
                                        return label + "°C";
                                    }
                                }
                            },
                            scales: {
                                xAxes: [
                                    {
                                        type: "time",
                                        time: {
                                            unit: "hour",
                                            displayFormats: {
                                                hour: "DD/MM/YY, HH:mm"
                                            },
                                            tooltipFormat: "MMM. DD YYYY, HH:mm"
                                        },
                                        scaleLabel: {
                                            display: true,
                                            labelString: "Date/Time"
                                        }
                                    }
                                ],
                                yAxes: [
                                    {
                                        scaleLabel: {
                                            display: true,
                                            labelString: "Temperature (°C)"
                                        },
                                        ticks: {
                                            callback: function (value, index, values) {
                                                return value + '°C';
                                            }
                                        }
                                    }
                                ]
                            }
                        }
                    });
                })
                .catch(error => {
                    console.log(error);
                    this.errored = true;
                })
                .finally(() => (this.loading = false));
        },
        setCityValue: function (value) {
            this.city = value;
            this.setChart();
        }
    }
}
</script>