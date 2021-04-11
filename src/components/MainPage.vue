<template>
  <div class="wrap">
    <div class="navBar">
      <h1 class="title">Sort Algorithms Visualizer</h1>
      <div class="formsContainer">
        <select v-model="selectedAlgorithm" class="form-select" aria-label="Default select example" @change="sortArray(selectedAlgorithm)">
          <option value selected>Choose a sort algorithm</option>
          <option v-for="(algorithm, name) in algorithms" :key="name" :value="name" >
            {{name}}
          </option>
        </select>
        <select v-model="selectedSpeed" class="form-select" aria-label="Default select example">
          <option value selected>Choose a speed</option>
          <option v-for="speed in speeds" :key="speed" :value="speed" >
            {{speed}}
          </option>
        </select>
        <select v-model="selectedSize" class="form-select" aria-label="Default select example" @change="randomArray(selectedSize)">
          <option value selected>Choose an array size</option>
          <option v-for="size in sizes" :key="size" :value="size" >
            {{size}}
          </option>
        </select>
      </div>
    </div>
    <div class="arrayContainer" :key="arrayKey">
      <div v-for="(num, idx) in array" :key="idx" class="bar" :style="{height: (num / array.length) * 80 + '%'}"></div>
    </div>
    <div class="footer">
      <h2>This is a footer</h2>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MainPage',
  data () {
    return {
      array: [],
      arrayKey:0,
      algorithms: 
        {
        "Insertion Sort": this.instertionSort, 
        "Selection Sort": this.selectionSort, 
        "Bubble Sort": this.bubbleSort,
      },
      speeds:[1,2,3,4,5],
      sizes:[10, 25, 50, 75, 100],
      selectedAlgorithm: "",
      selectedSpeed:"",
      selectedSize:"",
    }
  },
  mounted() {
    this.array = this.createArray(100)
  },
  methods : {
    delay(ms){
      return new Promise(res => setTimeout(res, ms));
    },

    shuffle(array) {
      array.sort(() => Math.random() - 0.5);
    },

    createArray(n) {
      //Array of size n starting from 1 to n
      return Array.from(new Array(n), (x,i) => i+1)
    },

    randomArray(n){
      if (!Number.isInteger(n)){
        n = 100
      }
      let a = this.createArray(n)
      this.shuffle(a)
      this.array = a
    },

    sortArray(selectedAlgorithm){
      let a = this.algorithms[selectedAlgorithm]
      console.log(a())
    },

    selectionSort(arr){
      return arr
    },

    instertionSort(arr){
      return arr
    },

    async bubbleSort(){
      let len = this.array.length;
      let swapped;
      do {
        swapped = false;
        for (let i = 0; i < len; i++) {
          if (this.array[i] > this.array[i + 1]) {
            let tmp = this.array[i];
            this.array[i] = this.array[i + 1];
            this.array[i + 1] = tmp;
            console.log("Swapping")
            this.arrayKey += 1;
            await this.delay(1)
            swapped = true;
          }
        }
      } while (swapped);
      return this.array;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.wrap{
  display: flex;
  flex-direction: column;
  height: 100%;
  color: white;
}

.navBar{
  background-color:#34495e;
}

.title {
  font-size: 1.7rem;
  padding: 20px 0px 10px;
}

.formsContainer{
  display: flex;
  flex-direction: row;
}

.form-select{
  margin: 0px 20px 20px 10px;
}

.arrayContainer{
  display: flex;
  align-items: flex-end;
  flex-direction: row;
  flex-grow: 1;
  width: 100%;
  padding: 10px 25px;
}

.footer{
  background-color: #34495e;
}

.bar{
  flex-grow: 1;
  background-color: lightblue;
  margin: 1px;
}

</style>
