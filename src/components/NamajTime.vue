<template>
  <div class="main">
    <h2>Namaz Times</h2>
    <div v-if="loading">Loading...</div>
    <div v-else>
      <table id="customers">
        <div></div>
        <tr id="custome_tr1">
          <th colspan="3" v-if="locationName">
            {{ locationName }}
          </th>
        </tr>
        <tr id="custome_tr2">
          <th colspan="3" id="colsapn_id">
            {{ currentDate }}
          </th>
        </tr>
        <tr>
          <td>Fajr</td>
          <td>:</td>
          <td>{{ prayerTimes.Fajr }}</td>
        </tr>
        <tr>
          <td>Sunrise</td>
          <td>:</td>
          <td>{{ prayerTimes.Sunrise }}</td>
        </tr>
        <tr>
          <td>Dhuhr</td>
          <td>:</td>
          <td>{{ prayerTimes.Dhuhr }}</td>
        </tr>
        <tr>
          <td>Asr</td>
          <td>:</td>
          <td>{{ prayerTimes.Asr }}</td>
        </tr>
        <tr>
          <td>Maghrib</td>
          <td>:</td>
          <td>{{ prayerTimes.Maghrib }}</td>
        </tr>
        <tr>
          <td>Isha</td>
          <td>:</td>
          <td>{{ prayerTimes.Isha }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      prayerTimes: {},
      loading: true,
      locationName: "",
      currentDate: "",
    };
  },
  methods: {
    async fetchPrayerTimes(latitude, longitude) {
      this.loading = true;
      try {
        const url = `https://api.aladhan.com/v1/timings?latitude=${latitude}&longitude=${longitude}&method=2`;
        const response = await axios.get(url);
        if (response.data && response.data.data) {
          this.prayerTimes = response.data.data.timings;
          this.locationName = response.data.data.meta.timezone;
          this.currentDate = new Date().toDateString();
        }
      } catch (error) {
        console.error("Error fetching prayer times:", error);
      } finally {
        this.loading = false;
      }
    },
  },
  mounted() {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          const latitude = position.coords.latitude;
          const longitude = position.coords.longitude;
          this.fetchPrayerTimes(latitude, longitude);
        },
        (error) => {
          console.error("Error getting current location:", error);
        }
      );
    } else {
      console.error("Geolocation is not supported by this browser.");
    }
  },
};
</script>

<style scoped>
.main {
  width: 250px;
  position: relative;
  left: 43%;
  margin-top: 10%;
}

#customers {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

#customers td,
#customers th {
  border: none;
  padding: 8px;
}

#customers tr:nth-child(even) {
  background-color: #f2f2f2;
}

#customers tr:hover {
  background-color: #ddd;
}

#customers th {
  text-align: center;
  margin: 0px;
  height: 20px !important;
}
#custome_tr1 {
  padding: 0px;
  background-color: rgb(177, 127, 21) !important;
  color: white;
}
#custome_tr2 {
  padding: 0px;
  background-color: rgb(255, 176, 6) !important;
  color: white;
}
p {
  padding: -10px;
}
@media only screen and (max-width: 600px) {
  .main {
    width: 250px;
    position: relative;
    left: 20%;
    margin-top: 5%;
  }
}
@media only screen and (max-width: 400px) {
  .main {
    width: 250px;
    position: relative;
    left: 15%;
    margin-top: 5%;
  }
}
</style>
