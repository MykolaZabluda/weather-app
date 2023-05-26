<template>
    <div id="wrapper" class="wrapper">
        <div class="alert" v-show="showAlert">
            To many cards
        </div>
        <div class="input-container">
            <div id="selectCity" class="select-city">
                <input id="field" v-on:input="setup" v-model="searchCity" type="text" pattern="\d*" class="input"
                    placeholder="Search city" />
                <div v-show="cities" class="dropdown-cities">
                    <div v-for="city in cities" v-bind:id="`city-${i}`" @click="setCityCard(city)">
                        {{ city }}
                    </div>
                </div>
            </div>
        </div>
        <div class="cards-block">
            <div v-for="(card, i) in info" v-bind:id="`card-${i}`" class="card card-1" @click="setChartByCity(card.name)">
                <div id="delete" class="delete" @click="deleteCityCard(i)">
                    <img id="close" class="close" src="../assets/close.svg" />
                </div>
                <h3 class="town-name">{{ card.name }}</h3>
                <p class="status">{{ card.weather[0].main }}<span>Sunrise {{ card.sys.sunrise | formatDate }} <span class="dot">•</span> Sunset
                        {{ card.sys.sunset | formatDate }}</span></p>
                <p class="temperature">{{ card.main.temp | formatTemp }}°C</p>
            </div>
        </div>
        <LineChart class="chart" ref="selectCity" />
    </div>
</template>

<script>
import axios from 'axios';
import LineChart from './LineChart.vue';
import { format } from 'date-fns'
import { ref } from 'vue';

export default {
    components: {
        LineChart
    },
    data() {
        return {
            info: [],
            clientCity: '',
            cities: [],
            searchCity: '',
            showAlert: false,
        }
    },
    mounted() {
        this.getClientIP();
    },
    filters: {
        formatDate: function (date) {
            return format(new Date(date * 1000), 'HH:mm');
        },
        formatTemp: function (temp) {
            return Math.round(temp);
        }
    },
    methods: {
        getClientIP() {
            axios.get('https://ipapi.co/json/', { headers: { 'Content-Type': 'application/json' } })
                .then(responce => {
                    this.clientCity = responce.data.city;
                    this.setChartByCity(this.clientCity)
                    this.getWeatherByCity(this.clientCity);
                    this.getForecast(this.clientCity);
                });
        },
        getWeatherByCity(cityName) {
            axios
                .get(`https://api.openweathermap.org/data/2.5/weather?q=${cityName}&units=metric&appid=9f2b2e8cd60541bbd9ad927b1a5cda93`, { headers: { 'Content-Type': 'application/json' } })
                .then(response => {
                    if (this.info.length < 5) {
                        this.info.push(response.data);
                    } else {
                        this.showAlert = true;
                        setTimeout(() => {this.showAlert = false}, 2000)
                    }
                });
        },
        getForecast(cityName) {
            axios
                .get(`https://api.openweathermap.org/data/2.5/forecast?q=${cityName}&units=metric&appid=9f2b2e8cd60541bbd9ad927b1a5cda93`, { headers: { 'Content-Type': 'application/json' } })
                .then(response => console.log(response));
        },
        setChartByCity: function (cityName) {
            this.$refs.selectCity.setCityValue(cityName);
        },
        setup() {
            axios
                .get(`https://api.openweathermap.org/geo/1.0/direct?q={${this.searchCity}}&units=metric&limit=10&appid=9f2b2e8cd60541bbd9ad927b1a5cda93`, { headers: { 'Content-Type': 'application/json' } })
                .then(response => {
                    this.cities = [];
                    response.data.forEach(item => {
                        this.cities.push(item.name)
                        console.log(this.cities);
                    });
                });
            console.log(this.cities);

            return this.cities;
        },
        setCityCard(cityName) {
            this.getWeatherByCity(cityName);
        },
        deleteCityCard(index) {
            this.info.splice(index, 1);
        }
    },
    watch: {
        search(searchCity) {
            this.getCities(searchCity);
        }
    }
}

</script>

<style lang="scss">
.wrapper {
    width: 100%;
    height: 550px;

    .alert {
        width: 45%;
        height: auto;
        margin: auto;
        padding: 10px;
        border-radius: 3px 3px 3px 3px;
        color: #D8000C;
        background-color: #FFBABA;
    }

    .input-container {
        display: flex;
        width: 80%;
        justify-content: left;
        margin: auto;
        margin-bottom: 25px;
        
        .input {
            height: 25px;
            margin: auto;
            border: none;
            border-color: transparent;
            border-bottom: 1px solid #000;
        }

        textarea:focus,
        input:focus {
            outline: none;
        }

        .select-city {
            display: flex;
            flex-direction: column;
            width: 30%;

            .dropdown-cities {
                text-align: left;
                margin: auto;
            }
        }

        .favorite-city {
            width: 8%;
            height: 25px;
            margin: auto;
            padding: 8px 10px 4px 10px;
            border: 1px solid #000;
        }
    }

    .cards-block {
        display: flex;
        flex-direction: row;
        margin-bottom: 25px;

        .card {
            width: 17%;
            height: auto;
            padding: 10px;
            border-radius: 10px;
            box-shadow: -5px 5px 15px 5px rgba(150, 150, 150, 0.2);
            margin: auto;
            text-align: left;

            .town-name {
                font-size: 22px;
                margin-bottom: 7px;
            }

            .status {
                font-size: 18px;
                font-weight: 600;
            }

            span {
                font-size: 14px;
                margin-left: 5px;
                color: #999;
                font-weight: 300;

                .dot {
                    font-size: 14px;
                    margin-right: 5px;
                }
            }

            .temperature {
                font-size: 32px;
                font-weight: 900;
            }

            table {
                position: relative;
                margin-top: 15px;
                width: 100%;
                text-align: center;
                margin-bottom: 10px;
            }

            .delete {
                position: absolute;
                width: 20px;
                height: 20px;
                border-radius: 50px;
                background: #ff6961;
                margin-left: 285px;
                margin-top: -19px;

                .close {
                    width: 13px;
                    height: 13px;
                    margin-top: 4px;
                    margin-left: 4px;
                }
            }
        }
    }

    .chart {
        width: 100%;
        height: auto;
    }
}

@media (min-width: 1000px) and (max-width: 1300px)  {
    .cards-block {
        .card {
            width: 33%;
        }
    }
}
</style>