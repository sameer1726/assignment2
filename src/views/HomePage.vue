<template>
  <div class="home-page">
    <div class="top-bar">
      <div class="brand-name">
        <p>WeatherSure</p>
      </div>
      <div class="search-bar">
        <!-- <img src="@/assets/images/search-icon.png" /> -->
        <input
          type="search"
          name="search-bar"
          class="s-box"
          placeholder="Search for Location"
          v-model="query"
          @keydown.enter="keyPressed"
        />
      </div>
    </div>
    <div class="mid-bar">
      <div class="date">
        {{ dateTimeManager() }}
        <div class="date-time">
          <p>{{ this.current_time }}</p>
        </div>
        <div class="date-day">
          <p>{{ this.current_day }} {{ this.current_date }}</p>
        </div>
      </div>
      <div class="temp">
        <div class="location">
          <p>{{ res.location.name }}, {{ res.location.region }}</p>
        </div>
        <div class="t">
          <div class="number">
            <h2 v-if="ref_temp">{{ res.current.temp_f }}º</h2>
            <h2 v-if="!ref_temp">{{ res.current.temp_c }}º</h2>
          </div>
          <div class="f-c">
            <p
              @click="getF"
              :style="{
                fontWeight: ref_temp ? 900 : 400,
              }"
            >
              F
            </p>
            <p
              @click="getC"
              :style="{
                fontWeight: !ref_temp ? 900 : 400,
              }"
            >
              C
            </p>
          </div>
        </div>
        <div class="type">
          <p>{{ res.current.condition.text }}</p>
        </div>
      </div>
      <div class="details">
        <p>
          Feels Like : {{ res.current.feelslike_f }}ºF <b>|</b>
          {{ res.current.feelslike_c }}ºC
        </p>
        <p>Humidity : {{ res.current.humidity }}</p>
        <p>Cloud : {{ res.current.cloud }}</p>
        <p>Wind Speed : {{ res.current.wind_mph }}mph</p>
        <p>Visibility : {{ res.current.vis_km }}km</p>
      </div>
    </div>
    <div class="week-title">
      <p>3-Day</p>
    </div>
    <week-card :res="res"></week-card>
    <div class="hour-title">
      <p>Hourly</p>
    </div>
    <hour-card :res="res"></hour-card>
  </div>
</template>

<script>
import HourCard from "@/components/HourCard.vue";
import WeekCard from "@/components/WeekCard.vue";
import axios from "axios";

export default {
  components: {
    HourCard,
    WeekCard,
  },

  data() {
    return {
      query: "",
      ref_temp: true,
      current_day: "",
      current_date: "",
      current_time: "",
      res: undefined,
    };
  },

  beforeCreate: function () {
    let initialLocation = "Mumbai";
    if (sessionStorage.getItem("query") === null) {
      sessionStorage.setItem("query", initialLocation);
    }
  },

  created: async function () {
    const response1 = await axios.get(
      "http://api.weatherapi.com/v1/forecast.json?key=" +
        "b30baba695d04eed82692742230802" +
        "&q=" +
        sessionStorage.getItem("query") +
        "&days=7&aqi=no&alerts=no"
    );
    console.log("Response 1: ", response1.data);
    this.res = response1.data;
    this.$root.$emit("change_bg", this.res);
  },

  methods: {
    async keyPressed() {
      let prevQuery = sessionStorage.getItem("query");
      let currentQuery = this.query;
      sessionStorage.setItem("query", currentQuery);
      console.log("Key was pressed with query: ", currentQuery);

      const response2 = await axios
        .get(
          "http://api.weatherapi.com/v1/forecast.json?key=" +
            "b30baba695d04eed82692742230802" +
            "&q=" +
            sessionStorage.getItem("query") +
            "&days=7&aqi=no&alerts=no"
        )
        .catch((errors) => {
          sessionStorage.setItem("query", prevQuery);
          alert(
            "Query entered not present in the dataset.\nTry with a different query."
          );
          console.log("ERRORS: ", errors.code);
        });
      // console.log("Response 2: ", response2.data);
      this.res = response2.data;
      this.$root.$emit("change_bg", this.res);
    },

    getF() {
      this.ref_temp = true;
    },

    getC() {
      this.ref_temp = false;
    },

    dateTimeManager() {
      const dateTime = this.res.location.localtime;
      const days = {
        0: "Sunday",
        1: "Monday",
        2: "Tuesday",
        3: "Wednesday",
        4: "Thursday",
        5: "Friday",
        6: "Saturday",
      };
      const cd = new Date(dateTime);
      let day = cd.getDay();

      this.current_day = days[day]; //day

      let datetime = dateTime.split(" "); //date
      let dateArr = datetime[0].split("-");
      this.current_date = dateArr[2] + "." + dateArr[1] + "." + dateArr[0];

      this.current_time = datetime[1]; //time
    },
  },
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.top-bar {
  height: 8vh;
  width: 100vw;
  padding: 1.5%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.brand-name p {
  font-style: normal;
  font-weight: 700;
  font-size: 36px;

  letter-spacing: 0.015em;
  background: linear-gradient(
    180deg,
    #d8e3ff 50%,
    rgba(216, 227, 255, 0) 85.42%
  );
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  backdrop-filter: blur(2px);
}

.search-bar {
  mix-blend-mode: luminosity;
}

.s-box {
  padding: 2%;
  background: rgba(255, 255, 255, 0.16);
  border-radius: 50px;
  border: 2px solid rgba(255, 255, 255, 0);
  width: 28vw;
  height: 4vh;
}

input[type="search"] {
  color: white;
}

input[type="search"]::-webkit-search-cancel-button {
  /* Remove default */
  /* -webkit-appearance: none; */
  height: 10px;
  width: 10px;
  color: white;
}

::placeholder {
  font-size: 13px;
  text-align: center;
  color: white;
}

.mid-bar {
  height: 22vh;
  width: 100vw;
  margin-top: 2.5%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.date {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-content: center;
  margin-left: 3%;
  margin-bottom: 11%;
  gap: 4px;
}

.date-time p {
  font-size: 35px;
  font-weight: 200;
  margin-left: 10%;
  letter-spacing: 0.4em;
}

.date-day p {
  font-weight: 800;
  font-size: 19px;
}

.temp {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-left: 10%;
  margin-bottom: 3.5%;
}

.location {
  font-weight: 100;
  font-size: 24px;
}

.t {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 20px;
}

.number h2 {
  font-size: 66px;
}

.f-c p {
  transition: 0.6s;
}

.f-c p:hover {
  cursor: pointer;
  font-size: 19px;
  font-weight: 900;
}

.f-c p {
  padding: 30%;
}

.type {
  font-size: 19px;
  font-weight: 500;
  text-transform: capitalize;
}

.details {
  padding: 0.4%;
  background: rgba(255, 255, 255, 0.12);
  border-radius: 10px;
  margin-right: 3.8%;
  margin-bottom: 3.2%;

  display: flex;
  flex-direction: column;
  width: 23vw;
  height: 20vh;

  font-weight: 400;
  font-size: 18px;
  line-height: 170%;

  text-align: center;
}

.details p {
  font-size: 16px;
  font-weight: 400;
  /* opacity: 0.8; */
  letter-spacing: 0.06em;
}

.week-title {
  text-align: left;
  margin-left: 1%;
}

.hour-title {
  text-align: left;
  margin-left: 1%;
}

.week-title p,
.hour-title p {
  font-weight: 700;
  font-size: 28px;
  text-transform: uppercase;
  font-style: normal;
  color: rgba(255, 255, 255, 0.6);
  flex: none;
  order: 0;
  flex-grow: 1;
  opacity: 1;
}
</style>
