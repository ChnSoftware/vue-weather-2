<template>
    <div v-if="isLoading" class="loading">
        <span></span>
    </div>
    <div
        v-else
        id="app"
        :class="new Date().getHours() > 15 ? 'image-1' : 'image-2'"
    >
        <main>
            <div class="search-box">
                <input
                    type="text"
                    class="search-bar"
                    placeholder="Search"
                    v-model="query"
                    @keypress.enter="fetchWeather"
                    ref="query"
                />
            </div>

            <div class="weather-wrap">
                <div class="location-box">
                    <div class="date">
                        <span>
                            {{
                                new Date().toLocaleString("en-US", {
                                    weekday: "long",
                                })
                            }},
                            {{
                                new Date().toLocaleString("en-US", {
                                    day: "2-digit",
                                })
                            }}
                            {{
                                new Date().toLocaleString("en-US", {
                                    month: "short",
                                })
                            }}
                        </span>
                    </div>
                    <div class="location">
                        {{ cityName }}, {{ weather.sys.country }}
                    </div>
                </div>

                <div class="weather-box">
                    <div class="temp">
                        {{ Math.round(this.weather.main.temp) }}&deg;C
                    </div>
                    <div class="weather">
                        <span>{{ this.weather.weather[0].description }}</span>
                        <img
                            :src="
                                require(`../public/conditions/${this.weather.weather[0].icon}.svg`)
                            "
                            alt=""
                        />
                    </div>
                </div>
            </div>
        </main>
    </div>
</template>

<script>
    import axios from "axios"

    export default {
        name: "App",
        data() {
            return {
                api_key: "41bd4fa2b41aefb9a36a0ba6fe068dbb",
                url_base: "https://api.openweathermap.org/data/2.5/",
                query: "Sinop",
                weather: {},
                isLoading: true,
            }
        },
        methods: {
            async fetchWeather() {
                if (this.query.trim() == "") {
                    alert("Field cannot be empty")
                }
                else {
                    try {

                        this.isLoading = true
                        const response = await axios.get(`${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}`)
                        const data = response.data
                        if (data) {
                            this.weather = data
                            this.isLoading = false
                            this.query = ""
                        }
                    }
                    catch (e) {
                        this.isLoading = false
                        alert(`${this.query} not found`)
                        this.query = ""
                    }
                }
            }
        },
        created() {
            this.fetchWeather()
        },
        computed: {
            cityName() {
                return this.weather.name.split(" ")[1] == "Province" ? this.weather.name.split(" ")[0] : this.weather.name
            }
        }
    }
</script>

<style lang="scss">
    @import url("https://fonts.googleapis.com/css2?family=Lato:wght@100;300;400;700&display=swap");

    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    body {
        font-family: "Lato", sans-serif;
    }

    .image-1 {
        background: url("https://raw.githubusercontent.com/skabdullah9/weather-app/master/src/assets/sunny.gif")
            no-repeat center center/cover;
    }
    .image-2 {
        background: url("https://raw.githubusercontent.com/skabdullah9/weather-app/master/src/assets/rain.gif")
            no-repeat center center/cover;
    }

    #app {
        transition: 400ms;

        main {
            display: flex;
            align-items: center;
            flex-direction: column;
            min-height: 100vh;
            padding: 25px;

            background-image: linear-gradient(
                to top,
                rgba(black, 0.1),
                rgba(black, 0.5)
            );

            .search-box {
                width: 100%;
                margin-bottom: 30px;

                .search-bar {
                    display: block;
                    width: 100%;
                    padding: 15px;
                    color: #313131;
                    color: white;
                    font-size: 20px;
                    appearance: none;
                    border: none;
                    outline: none;
                    background: none;
                    background-color: rgba(white, 0.2);
                    border-radius: 8px;
                    box-shadow: 0 0 10px rgba(white, 1);
                    transition: all 400ms;
                    text-transform: capitalize;
                    text-align: center;

                    &:focus {
                        border-radius: 16px;

                        &::placeholder {
                            color: rgba(white, 0.7);
                        }
                    }
                    &::placeholder {
                        color: white;
                    }
                }
            }

            .weather-wrap {
                margin: auto;
                display: flex;
                flex-direction: column;
                align-items: center;
                background-color: rgba(white, 0.1);
                padding: 50px;
                border-radius: 50px;
                box-shadow: 0 0 10px rgba(white, 1);

                @media (max-width: 400px) {
                    padding: 30px;
                    margin-top: 30px;
                }

                .location-box {
                    display: flex;
                    align-items: center;
                    flex-direction: column;

                    .location {
                        color: white;
                        font-size: 32px;
                        font-weight: 500;
                        padding: 10px;
                        border-radius: 10px;
                        text-align: center;
                    }
                    .date {
                        border-radius: 10px;
                        color: #fff;
                        font-size: 20px;
                        font-weight: 300;
                        font-style: italic;
                        text-align: center;
                        padding: 10px;
                    }
                }

                .weather-box {
                    display: flex;
                    align-items: center;
                    flex-direction: column;

                    .temp {
                        padding: 10px 25px;
                        color: white;
                        font-size: 100px;
                        font-weight: 700;
                        background-color: rgba(white, 0.25);
                        box-shadow: 0 0 10px rgba(white, 1);
                        border-radius: 20px;
                        margin: 30px 0;
                    }

                    .weather {
                        color: white;
                        font-size: 48px;
                        text-transform: capitalize;
                        text-align: center;

                        img {
                            margin-left: 20px;
                            height: 32px;
                        }
                    }
                }
            }
        }
    }
    .loading {
        @keyframes spin {
            to {
                transform: rotateZ(360deg);
            }
        }
        display: flex;
        height: 100vh;
        width: 100%;
        justify-content: center;
        align-items: center;

        span {
            display: block;
            width: 60px;
            height: 60px;
            margin: 0 auto;
            border: 2px solid transparent;
            border-top-color: #313131;
            border-radius: 50%;
            animation: spin ease 1000ms infinite;
        }
    }
</style>