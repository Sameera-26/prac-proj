<template>
  <body class="h-screen bg-gray-100 py-6">

  <h1 class="font-bold text-2xl"> Weight Tracker </h1>
  <br />
  <form @submit.prevent="addWeight">
    <div class="current"> 
      <span> {{ currentWeight.weights }}  </span>
   
    <small class="text-gray-500 text-xs"> Current Weight in Kgs </small>
  </div>
  <input type="number" step="0.1" placeholder="  54" v-model="weight" class="border-2 border-[#511f75]  shadow-lg h-8 rounded-sm w-1/2" /> 
  <button type="submit" class="mx-1 bg-[#511f75] text-white shadow-md text-sm h-8 rounded-sm w-1/3 hover:bg-white hover:text-black hover:border-pink-500"> Add Weight </button>
</form>
<br />

<div v-if="weight_list && weight_list.length > 0 ">
  <h2 class="font-bold "> Last 7 Days </h2>
  <div class="canvas-box" >
    <canvas ref="weightChartEl" >
    </canvas>
  </div>
</div>
<div class="weight-history">
<h3 class="font-bold text-left text-gray-500 mx-4 ">Weight History</h3>
<ul>
  <li class="text-left mx-4" v-for="list in weight_list" :key="list" >
    <input  v-model="list.weights"  class="bg-white my-2 text-gray-500 mx-1   font-bold no-border focus:outline-none" />
    <small >  {{ new Date(list.date).toLocaleDateString() }}  </small>    
  <button @click="rem" class="bg-red-500/75 rounded-3xl    text-white font-bold text-md px-4 mx-3">x</button>
  </li>
</ul>
</div>


  </body>
</template>

<script>
import {ref, watch, shallowRef, nextTick, computed, onMounted} from 'vue'
import Chart from 'chart.js/auto'
export default {
  name: 'App',
  setup(){
    const weight= ref(60.0)
    const weight_list= ref([])
    const weightChartEl= ref(null)
    const weightChart = shallowRef(null) 
    //when we update our chart it will throw error if we dont use shallowRef
    //shallowRef stops the reactive nature of the Object

    const currentWeight = computed(() => { 
      return weight_list.value.slice().sort((a,b) => b.date - a.date )[0] || { weights: 0 }
    })
    
    watch(weight, (newVal) => {
      localStorage.setItem('weight',newVal)
    })
    function addWeight() {
      if ( weight.value === '' ) {
        return
      }
      weight_list.value.push({
        weights: weight.value,
        date: new Date().getTime()
      })
    }
    watch(weight_list, newWeights => {
      localStorage.setItem('weight-in',JSON.stringify(newWeights))
      const ws = [...newWeights]
      if (weightChart.value) {
        weightChart.value.data.labels = ws
            .sort((a, b) => a.date - b.date)
            .map(weights => new Date(weights.date).toLocaleDateString() )
            .slice(-7)
        weightChart.value.data.datasets[0].data = ws
            .sort((a, b) => a.date - b.date)
            .map(weights => weights.weights)
            .slice(-7)
      weightChart.value.update()
      return
    }
    nextTick(() => {
        weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
          type: 'line',
          data: {
            labels: ws
            .sort((a,b) => a.date - b.date)
            .map(weights => new Date(weights.date).toLocaleDateString()),
            datasets: [
              {
                label:'Weights' ,
                data: ws.sort((a,b) => a.date - b.date).map(weights => weights.weights),
                backgroundColor: 'rgba(81, 31, 117,0.2)',
                borderColor:'rgb(81, 31, 117)',
                borderWidth: 1,
                fill: true
              }
            ]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false
          }
        })
      })
    },
    
    {
      deep: true
    })
    onMounted(() => {
      weight_list.value= JSON.parse( localStorage.getItem('weight-in') ) || []
    })
    function rem() {
      weight_list.value.pop()
    }
    return {
      weight,
      weight_list,
      addWeight,
      rem,
      weightChart,
      weightChartEl,
      currentWeight
    }
    
  },
  components: {
    
  }
}
</script>

<style>
#app {
  font-family: 'montserrat', sans-serif ;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  
}
.current {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	width: 200px;
	height: 200px;
	text-align: center;
	background-color: white;
	border-radius: 999px;
	box-shadow: 0 4px 12px rgba(153, 1, 1, 0.1);
	border: 5px solid rgb(81, 31, 117);
	
	margin: 0 auto 2rem;
}
.current span {
	display: block;
	font-size: 2em;
	font-weight: bold;
	margin-bottom: 0.5rem;
}
.current small {
	color: #888;
	font-style: italic;
}
.canvas-box {
	width: 100%;
	max-width: 720px;
	background-color: white;
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	margin-bottom: 2rem;
}
.weight-history ul {
	list-style: none;
	padding: 0;
	margin: 0;
}
.weight-history ul li {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 0.5rem;
	cursor: pointer;
}
</style>
