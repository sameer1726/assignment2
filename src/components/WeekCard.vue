<template>
  <div class="week-cards">
    <div
      v-for="(thisDay, index) in res.forecast.forecastday"
      :key="index"
      @click="callingHourCard(thisDay)"
      class="week-card"
    >
      <div class="image">
        <!-- <img src="@/assets/images/sunny-day.png" alt="loading.." /> -->
        <!-- {{ iconQueryMaker(thisDay.day.condition.text) }} -->
        <img
          :src="iconQueryMaker(thisDay.day.condition.text)"
          alt="loading.."
        />
      </div>
      <div class="temp">
        <p class="data">
          {{ thisDay.day.maxtemp_f }}º<sup>F</sup> •
          {{ thisDay.day.mintemp_f }}º<sup>F</sup>
        </p>
      </div>
      <div class="date">
        {{ dateTimeManager(thisDay.date) }}
      </div>
      <div class="type">
        {{ thisDay.day.condition.text }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    res: {
      type: Object,
      require: true,
    },
  },

  data() {
    return {
      current_date: "",
      current_day: "",
      current_icon: "",
      icon: {
        sunny: "sunny-day.png",
        clear: "sunny-day.png",
        cloudy: "cloudy.png",
        partly_cloudy: "cloudy.png",
        overcast: "overcast.png",
        rain: "rainy.png",
        moderate_rain: "rainy.png",
        patchy_rain_possible: "rainy.png",
        heavy_rain: "rainy.png",
        snow: "snow.png",
        moderate_snow: "snow.png",
        light_snow: "snow.png",
      },
    };
  },

  methods: {
    dateTimeManager(thisDate) {
      const days = {
        0: "Sunday",
        1: "Monday",
        2: "Tuesday",
        3: "Wednesday",
        4: "Thursday",
        5: "Friday",
        6: "Saturday",
      };
      const cd = new Date(thisDate.split());
      let day = cd.getDay();
      this.current_day = days[day]; //day
      let dateArr = thisDate.split("-"); //date
      this.current_date = dateArr[2] + "th";
      const current_day_date_display =
        this.current_day.substring(0, 3) + " " + this.current_date;
      return current_day_date_display;
    },

    callingHourCard(thisDay) {
      console.log("Days Details: ", thisDay);
      this.$root.$emit("days_details", thisDay);
    },

    iconQueryMaker(weatherType) {
      let temp = weatherType.toLowerCase();
      temp = temp.replaceAll(" ", "_");
      let finalPath = require("@/assets/images/" + this.icon[temp]);
      console.log("final path: ", finalPath);
      return finalPath;
      // return "@/assets/images/snow.png";
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

.week-cards {
  /* padding-top: 1%; */
  display: flex;
  justify-content: center;
  width: 100vw;
  height: 28.5vh;
  padding-right: 0.2%;
  padding-left: 0.2%;
}

.week-card {
  min-width: 8vw;
  height: 25.5vh;
  padding: 1%;
  margin: 0.2%;
  margin-right: 1.2%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;

  background: rgba(255, 255, 255, 0.04);
  border: 1px solid rgba(0, 0, 0, 0.16);
  border-radius: 20px;
}

.week-card:hover {
  box-shadow: 0px 4px 3px rgba(0, 0, 0, 0.45);
  cursor: pointer;
}

.image {
  width: 100%;
  height: 14vh;
  flex: none;
  order: 0;
  align-self: stretch;
  flex-grow: 1;
}

.image img {
  height: 12vh;
  object-fit: cover;
}

/* .temp .header {
  font-size: 15px;
  opacity: 0.8;
} */

.temp .data {
  font-size: 19px;
  font-weight: 550;
  letter-spacing: 0.05em;
}

.temp sup {
  font-size: 10.5px;
  font-weight: 500;
  opacity: 0.8;
}

.date p {
  font-size: 15px;
  opacity: 0.6;
}

.type {
  padding-top: 3%;
  opacity: 0.6;
  font-size: 14px;
  text-transform: capitalize;
}
</style>
