<template>
  <main :style="{ background: backgroundColor }">
    <h1>Quel temps fait-il ?</h1>
    <h2>Quartier : {{ city }}</h2>
    <img :src="weatherImgUrl" alt="" class="weather-img" />
    <div class="text">
      <p>{{ weatherDescription }}</p>
      <p>{{ dateData }}</p>
    </div>
    <p class="temp">{{ temperature }}°C</p>
    <input
      list="dataList"
      id="ice-cream-choice"
      name="ice-cream-choice"
      placeholder="choisir une ville"
    />
    <datalist id="dataList">
      <option
        v-for="(city, index) in citysList"
        :key="index"
        :value="city.name"
      ></option>
    </datalist>
  </main>
</template>

<script>
export default {
  name: "BodyComponent",
  data() {
    return {
      city: "",
      weatherImgUrl: "",
      weatherDescription: "",
      temperature: "",
      dateData: "done",
      backgroundColor: "",
      citysList: [],
    };
  },
  methods: {
    getDateData: function () {
      let date = new Date();
      let options = {
        weekday: "long",
        year: "numeric",
        month: "long",
        day: "numeric",
      };
      this.dateData = date.toLocaleDateString("fr-FR", options);
    },
    getCity: function (rows, start) {
      fetch(
        "https://public.opendatasoft.com/api/records/1.0/search/?dataset=correspondance-code-insee-code-postal&q=&rows=" +
          rows +
          "&start=" +
          start
      )
        .then((response) => response.json())
        .then((data) => {
          console.log(data);
          data.records.forEach((record) => {
            let lat = record.geometry.coordinates[1];
            let lon = record.geometry.coordinates[0];
            let commune = record.fields.nom_comm;
            this.citysList.push({
              name: commune,
              latitude: lat,
              longitude: lon,
            });
          });
        });
    },
    setCities: function (rows) {
      for (let index = 0; index < 10000; index += rows) {
        this.getCity(rows, index);
      }
    },
    getPosition: function () {
      return new Promise((resolve, reject) => {
        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(function (position) {
            let latitude = position.coords.latitude;
            let longitude = position.coords.longitude;

            resolve({ lat: latitude, lon: longitude });
          });
        } else {
          reject("your browser doesn't support geolocation API");
        }
      });
    },
    getWeather: function () {
      this.getPosition().then((position) => {
        let url = `https://api.openweathermap.org/data/2.5/weather?lang=fr&units=metric&lat=${position.lat}&lon=${position.lon}&appid=f4eeeaf692bacdd4dd0934360afa81a2`;
        fetch(url)
          .then((response) => response.json())
          .then((data) => {
            this.weatherDescription = data.weather[0].description;
            this.temperature = data.main.temp.toFixed(1);
            this.city = data.name;
            this.weatherImgUrl = `http://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`;

            if (this.temperature > 15) {
              this.backgroundColor = `linear-gradient(to top,
    rgba(90, 119, 236, 1) 30%,
    rgb(245, 116, 11) 100%,
    rgba(252, 176, 69, 1) 100%
  )`;
            } else {
              this.backgroundColor = `linear-gradient(to bottom
,
    rgba(90, 119, 236, 1) 30%,
    rgb(245, 116, 11) 100%,
    rgba(252, 176, 69, 1) 100%
  )`;
            }
          })
          .catch((error) => console.log(error));
      });
    },
    getOptionValue: function () {
      console.log(this);
    },
  },
  mounted() {
    this.setCities(10000);
    this.getWeather();
    setInterval(() => {
      this.getWeather();
    }, 100000);
  },
  beforeMount() {
    this.getDateData();
    setInterval(() => {
      this.getDateData();
    }, 100000);
    this.getOptionValue();
  },
};
</script>

<style scoped>
main {
  width: 100%;
  height: 100%;
  color: #fff;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 30px;
}

h1 {
  margin: 30px 0;
}

main img {
  margin: 0;
  padding: 0;
  width: 70%;
  transform: scale(1.2);
}

.text {
  font-size: 1.2rem;
}

.temp {
  margin-top: 30px;
  font-size: 60px;
}
</style>
