<template>
  <div class="hello" :show="overlayLoading">
    <h1>{{ msg }}</h1>
    <div
      v-if="currentPlanet !== null"
      :name="currentPlanet.name"
      :climate="currentPlanet.climate"
      :terrain="currentPlanet.terrain"
      :population="currentPlanet.population"
      :diameter="currentPlanet.diameter"
    >
      <ul>
        <li>{{ currentPlanet.name }}</li>
        <li>{{ currentPlanet.climate }}</li>
        <li>{{ currentPlanet.terrain }}</li>
        <li>{{ currentPlanet.population }}</li>
        <li>{{ currentPlanet.diameter }}</li>
      </ul>
      <div style="display: flex">
        <h6>Rating:</h6>
        <div
          class="rating-div"
          :class="
            currentPlanet.hoverRating >= item ||
            currentPlanet.givenRating >= item
              ? 'fill-rating'
              : ''
          "
          @click="selectPlanetRating(item)"
          @mouseover="hoverOnRating(item)"
          @mouseleave="leaveOnRating"
          v-for="item in maxRating"
          :key="item"
        ></div>
        <span>
          <i>/{{ currentPlanet.givenRating || currentPlanet.hoverRating }}</i>
        </span>
      </div>
    </div>
    <VueTailwindPagination
      :current="currentPage"
      :total="totalPlanets"
      :per-page="perPage"
      @page-changed="onPageClick($event)"
    />
  </div>
</template>

<script>
import "@ocrv/vue-tailwind-pagination/dist/style.css";
import VueTailwindPagination from "@ocrv/vue-tailwind-pagination";
// import "vue-good-table/dist/vue-good-table.css";
import axios from "axios";
export default {
  components: {
    VueTailwindPagination,
  },
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      planetsList: [],
      currentPage: 1,
      perPage: 1,
      isListReceived: false,
      maxRating: 5,
      overlayLoading: false,
    };
  },
  created() {
    this.getPlanetsList();
  },
  methods: {
    hoverOnRating(value) {
      this.currentPlanet.hoverRating = value;
    },
    leaveOnRating() {
      this.currentPlanet.hoverRating = 0;
    },
    selectPlanetRating(value) {
      this.currentPlanet.givenRating = value;
    },
    onPageClick(event) {
      this.currentPage = event;
    },
    getPlanetsList() {
      let vm = this;
      vm.planetsList = [];
      axios({
        method: "get",
        url: "https://swapi.dev/api/planets",
      })
        .then((res) => {
          if (res.status === 200 && res.data) {
            res.data.results.forEach((item) => {
              item.hoverRating = 0;
              item.givenRating = 0;
            });
            vm.planetsList = res.data.results;
          }
          vm.overlayLoading = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
  computed: {
    currentPlanet() {
      return this.planetsList.length > 0
        ? this.planetsList[this.currentPage - 1]
        : null;
    },
    totalPlanets() {
      return this.planetsList.length;
    },
  },
};
</script>

<style>
h1 {
  font-size: 4rem;
}
li {
  font-size: 2rem;
}
.rating-div {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 1px solid black;
  display: flex;
  margin-left: 1px;
}
.fill-rating {
  background-color: #d9d900;
  border-color: #767600;
}
</style>
