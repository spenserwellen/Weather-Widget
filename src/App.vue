<template>
	<div id="app" v-bind:class="backgroundPicker(weather)">
		<main>
			<div class="container">
				<div class="content-container">
					<div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
						<div class="location-box">
							<div class="location">
								{{ weather.name }}, {{ weather.sys.country }}
							</div>
							<div class="date">
								{{ dateBuilder() }}
							</div>
						</div>
						<div class="weather-box">
							<div class="temp">{{ Math.round(weather.main.temp) }}&#176;F</div>
							<div class="weather">{{ weather.weather[0].main }}</div>
						</div>
					</div>
					<div class="weather-wrap" v-else>
						<div class="location-box">
							<div class="location">Firewatch Weather Provider</div>
						</div>
					</div>
					<div class="search-box">
						<input
							type="text"
							class="search-bar"
							placeholder="Enter a City, State, or Country..."
							v-model="query"
							@keypress="fetchWeather"
						/>
					</div>
				</div>
			</div>
		</main>
	</div>
</template>

<script>
export default {
	name: 'App',
	data() {
		return {
			api_key: process.env.VUE_APP_API_KEY,
			url_base: 'https://api.openweathermap.org/data/2.5/',
			query: '',
			weather: {},
		};
	},
	methods: {
		dateBuilder() {
			let d = new Date();
			let months = [
				'January',
				'February',
				'March',
				'April',
				'May',
				'June',
				'July',
				'August',
				'September',
				'October',
				'November',
				'December',
			];
			let days = [
				'Sunday',
				'Monday',
				'Tuesday',
				'Wednesday',
				'Thursday',
				'Friday',
				'Saturday',
			];

			let day = days[d.getDay()];
			let date = d.getDate();
			let month = months[d.getMonth()];
			let year = d.getFullYear();

			return `${day} ${date} ${month} ${year}`;
		},
		fetchWeather(e) {
			if (e.key == 'Enter') {
				fetch(
					`${this.url_base}weather?q=${this.query}&units=imperial&APPID=${this.api_key}`
				)
					.then((res) => {
						return res.json();
					})
					.then(this.setResults);
			}
		},
		setResults(results) {
			this.weather = results;
		},
		backgroundPicker(weather) {
			if (typeof weather.main != 'undefined') {
				if (weather.main.temp > 88) {
					return 'warm';
				} else if (weather.main.temp < 32) {
					return 'cold';
				} else {
					switch (weather.weather[0].main) {
						case 'Clear':
							return 'clear';
						case 'Drizzle':
							return 'drizzle';
						case 'Rain':
							return 'rain';
						case 'Snow':
							return 'snow';
						default:
							return '';
					}
				}
			}
		},
	},
};
</script>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}
body {
	font-family: 'montserrat', sans-serif;
}
#app {
	background-image: url('./assets/regular.jpg');
	background-size: cover;
	background-position: center center;
	transition: 350ms;
}
#app.clear {
	background-image: url('./assets/clear.jpg');
}
#app.drizzle {
	background-image: url('./assets/drizzle.jpg');
}
#app.rain {
	background-image: url('./assets/rain.jpg');
}
#app.snow {
	background-image: url('./assets/snow.jpg');
}
#app.cold {
	background-image: url('./assets/cold-bg.jpg');
}
#app.warm {
	background-image: url('./assets/warm-bg.jpg');
}
main {
	min-height: 100vh;
	padding: 25px;
	background-image: linear-gradient(
		to bottom,
		rgba(0, 0, 0, 0.05),
		rgba(0, 0, 0, 0.35)
	);
}
.search-box {
	width: 100%;
	max-width: 350px;
	margin: 5em auto;
	margin-bottom: 60px;
}
.search-box .search-bar {
	display: block;
	width: 100%;
	padding: 15px;
	padding-left: 30px;
	max-width: 1200px;
	margin: 0 auto;
	color: #313131;
	font-size: 20px;
	appearance: none;
	border: none;
	outline: none;
	background: none;
	box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
	background-color: rgba(255, 255, 255, 0.65);
	border-radius: 0px 16px 0px 16px;
	transition: 0.4s;
}
.search-box .search-bar:focus {
	box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
	background-color: rgba(255, 255, 255, 0.75);
	border-radius: 16px 0px 16px 0px;
}
.location-box .location {
	color: #fff;
	font-size: 32px;
	font-weight: 500;
	text-align: center;
	text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
	color: #fff;
	font-size: 20px;
	font-weight: 300;
	font-style: italic;
	text-align: center;
}
.weather-box {
	text-align: center;
}
.weather-box .temp {
	display: inline-block;
	padding: 10px 25px;
	color: #fff;
	font-size: 3em;
	font-weight: 900;
	text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
	background-color: rgba(255, 255, 255, 0.4);
	border-radius: 16px;
	margin: 30px 0px;
	box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
	color: #fff;
	font-size: 1.7em;
	font-weight: 700;
	font-style: italic;
	text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.container {
	background: rgba(75, 75, 75, 0.55);
	backdrop-filter: blur(3px);
	max-width: 500px;
	max-height: 600px;
	margin: 15vh auto;
	padding: 0 20px;
	border-radius: 1.5rem;
}

.content-container {
	padding: 2rem 1rem 0.5rem;
}

@media screen and (max-width: 450px) {
	.search-box .search-bar {
		font-size: 16px;
	}
}

@media screen and (max-width: 392px) {
	.search-box .search-bar {
		font-size: 14px;
		padding-left: 15px;
	}

	.content-container {
		padding-left: 0;
		padding-right: 0;
	}
}

@media screen and (max-width: 316px) {
	main {
		padding-left: 7.5px;
		padding-right: 7.5px;
	}
}
</style>
