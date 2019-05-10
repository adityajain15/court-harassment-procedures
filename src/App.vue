<template>
  <div id="app" class="oswald pa2">
    <a href="https://thewire.in/"><img src="logo.svg" style="background: red;" class="pa2 w-60 w-20-ns center cursor db"/></a>
    <div id="victim_options">
      <span class="db bold f3">Choose the Victim's identity</span>
      <button
        v-for="(victim, index) in victim_categories"
        :key="`victim-${index}`"
        class="pa2 ma2 bg-white"
        @click="setVictim(victim)"
        :style="victim_selected === victim ? 'background: black; color: white;':''">
        {{victim}}
      </button>
    </div>

    <div id="accused_options" class="mv4">
      <span class="db bold f3">Choose the Accused's identity</span>
      <button v-for="(accused, index) in accused_categories"
      :key="`accused-${index}`"
      class="pa2 ma2  bg-white"
      @click="setAccused(accused)"
      :style="accused_selected === accused ? 'background: black; color: white;':''">
        {{accused}}
      </button>
    </div>

    <div id="cardContainer" class="w-100">
      <div
      v-if="scenarios[victim_selected] !== undefined && scenarios[victim_selected][accused_selected]!==undefined"
      v-for="(scenario, index) in scenarios[victim_selected][accused_selected]"
      :key="`card-${index}`"
      class="w-100 w-25-ns dib v-top pa2">
        <div class="ba bw2">
          <span :class="`f3 bg-${scenario.outcome === 'yes' ? 'green' : scenario.outcome === 'no' ? 'red' : 'yellow'} bold db pa2`">{{scenario.card}}</span>
          <p class="pa2 georgia f4 bg-white">{{scenario.description}}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { csv } from 'd3-fetch'
import { nest } from 'd3-collection'
export default {
  name: 'app',
  data () {
    return {
      scenarios: {},
      victim_categories: [],
      accused_categories: [],
      victim_selected: 'Non-Court Staff',
      accused_selected: 'Court Staff'
    }
  },
  async mounted () {
    const data = await csv('./scenarios.csv')
    this.scenarios = nest()
      .key(d => d.victim)
      .key(d => d.accused)
      .object(data)

    for (let victim in this.scenarios) {
      if (!this.victim_categories.includes(victim)) {
        this.victim_categories.push(victim)
      }
      for (let accused in this.scenarios[victim]) {
        if (!this.accused_categories.includes(accused)) {
          this.accused_categories.push(accused)
        }
      }
    }
  },
  methods: {
    setVictim (victim) {
      this.victim_selected = victim
    },
    setAccused (accused) {
      this.accused_selected = accused
    }
  }
}
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background:slategray;
}

/*#app::after {
  content: "";
  background-image: url('assets/scourt.jpeg');
  filter: grayscale(1);
  background-size: cover;
  opacity: 0.6;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  position: absolute;
  z-index: -1;
}*/

button {
  background: transparent;
  box-shadow: none;
  border: 3px solid black;
  cursor: pointer;
}

.oswald {
  font-family: 'Oswald', sans-serif;
  font-weight: 400;
}

.bold {
  font-weight: 700;
}
</style>
