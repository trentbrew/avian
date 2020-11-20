<script>
export default {
    name: 'Weather',
    data() {
        return {
            weather: {
                temp: null,
                location: null,

            }
        }
    },
    mounted() {
        this.getWeather();
        console.log('weather mounted');
    },
    methods: {
        getWeather() {
        const weather = require('../plugins/weather-js');

        weather.find(
            { 
            search: this.weather.location, 
            degreeType: this.weather.degreeType 
            },
            (error, result) => {
            if (error) console.error(error);

            console.log(JSON.stringify(result, null, 2));
            console.log('chicago temp: ', result[0].current.temperature);
            this.weather.currentWeather = result[0].current.temperature + '°' + this.weather.degreeType;
            },
        )
        },
    }
}
</script>

<template>
    <div class="weather-dialog-container">
        
    </div>  
</template>

<style lang="scss" scoped>
@import '../assets/styles/global';

.weather-dialog-container {
    background: black;
    height: 300px;
    width: 250px;
    border-radius: $rad;
}

</style>