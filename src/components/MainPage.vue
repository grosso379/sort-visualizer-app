<template>
  <div class="wrap">
    <div class="navBar">
      <h1 class="title">Sort Algorithms Visualizer</h1>
      <div class="formsContainer">
        <select v-model="selectedAlgorithm" class="form-select" aria-label="Default select example" :disabled="running">
          <option value selected>Choose a sort algorithm</option>
          <option v-for="(algorithm, name) in algorithms" :key="name" :value="name" >
            {{name}}
          </option>
        </select>
        <select v-model="selectedSpeed" class="form-select" aria-label="Default select example" :disabled="running">
          <option value selected>Choose a speed</option>
          <option v-for="(value, key) in speeds" :key="key" :value="key" >
            {{key}}
          </option>
        </select>
        <select v-model="selectedSize" class="form-select" aria-label="Default select example" @change="randomArray(selectedSize);" onclick="this.selectedIndex = 0;" :disabled="running">
          <option value selected>Choose an array size</option>
          <option v-for="size in sizes" :key="size" :value="size" >
            {{size}}
          </option>
        </select>
      </div>
    </div>
    <div class="arrayContainer" :key="arrayKey">
      <div v-for="(obj, idx) in array" :key="idx" class="bar" :style="{height: (obj.num / array.length) * 80 + '%', 'background-color':obj.color}"></div>
    </div>
    <div class="footer">
      <button type="button" class="btn btn-primary" @click="sortArray()">Sort Array</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MainPage',
  data () {
    return {
      array: "",
      arrayKey:0,
      algorithms: 
        {
        "Insertion Sort": this.instertionSort, 
        "Selection Sort": this.selectionSort, 
        "Bubble Sort": this.bubbleSort,
        "Quick Sort": this.aux,
      },
      speeds:{1:50, 2:25, 3:10, 4:1, 5:0},
      sizes:[20, 40, 60, 80, 100],
      selectedAlgorithm: "",
      selectedSpeed:"",
      selectedSize:"",
      running:false,
    }
  },
  mounted() {
    this.randomArray(100)
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
      return Array.from(new Array(n), (x,i) => {return {'num':i+1, 'color':'lightblue'}})
    },

    randomArray(n){
      if (!Number.isInteger(n)){
        n = 100
      }
      let a = this.createArray(n)
      this.shuffle(a)
      this.array = a
    },

    async sortArray(){
      if (this.selectedAlgorithm != "" && this.selectedSpeed != ""){
        let algorithm = this.algorithms[this.selectedAlgorithm]
        this.running = true
        let start = new Date().getTime();
        await algorithm()
        let end = new Date().getTime();
        let time = end - start;
        alert(`It took ${time} ms to sort an array of size ${this.selectedSize} with ${this.selectedAlgorithm} - speed: ${this.selectedSpeed}`)
        this.running = false
      } else {
        alert("Please select a speed and an algorithm")
      }
    },

    async selectionSort(){
      let n = this.array.length;
      for(let i = 0; i < n; i++) {
        // Finding the smallest number in the subarray
        let min = i;
        for(let j = i+1; j < n; j++){
          await this.delay(this.speeds[this.selectedSpeed])
          this.array[j-1].color = (j-1 == min) ? 'yellow' : 'lightblue'
          this.array[j].color = 'red'
          if(this.array[j].num < this.array[min].num) {
            this.array[min].color = 'lightblue'
            min=j; 
            this.array[min].color = 'yellow'
          }
          this.arrayKey+=1
        }
        //Change all bar colors to lightblue after finish
        for (let obj of this.array){
          obj.color = 'lightblue'
        }
        if (min != i) {
          // Swapping the elements
          let tmp = this.array[i]; 
          this.array[i] = this.array[min];
          this.array[min] = tmp;
          this.arrayKey+=1
        }
      }
      return this.array;
    },

    async instertionSort(){
      let n = this.array.length;
      for (let i = 1; i < n; i++) {
        // Choosing the first element in our unsorted subarray
        let current = this.array[i];
        // The last element of our sorted subarray
        var j = i-1; 
        while ((j > -1) && (current.num < this.array[j].num)) {
          this.array[j+1] = this.array[j];
          this.array[j].color = 'red'
          this.array[j+1].color = 'red'
          await this.delay(this.speeds[this.selectedSpeed])
          this.array[j].color = 'lightblue'
          this.array[j+1].color = 'lightblue'
          j--;
          this.arrayKey += 1
          
        }
        this.array[j+1] = current;
      }
      this.array[j+1].number = j+2
      this.arrayKey += 1
    },

    async bubbleSort(){
      let len = this.array.length;
      let swapped;
      do {
        swapped = false;
        for (let i = 0; i < len; i++) {
          this.colorArray[i] = 'red'
          this.colorArray[i + 1] = 'red'
          if (this.array[i] > this.array[i + 1]) {
            let tmp = this.array[i];
            this.array[i] = this.array[i + 1];
            this.array[i + 1] = tmp;
            swapped = true;
          }
          this.arrayKey += 1;
          await this.delay(this.speeds[this.selectedSpeed])
          this.colorArray[i] = 'lightblue'
          this.colorArray[i+1] = 'lightblue'
        }
      } while (swapped);
      this.colorArray = Array(len).fill('lightblue')
      return this.array;
    },

    aux(){
      this.quickSort(this.array, 0, this.array.length - 1)
    },

    quickSort(arr, start, end){
      // Base case or terminating case
      if (start >= end) {
          return;
      }
      
      // Returns pivotIndex
      let index = this.partition(arr, start, end);
      
      // Recursively apply the same logic to the left and right subarrays
      this.quickSort(arr, start, index - 1);
      this.quickSort(arr, index + 1, end);
    },

    partition(arr ,start, end){
      let pivotValue = arr[end];
      let pivotIndex = start; 
      for (let i = start; i < end; i++) {
          if (arr[i] < pivotValue) {
          // Swapping elements
          [arr[i], arr[pivotIndex]] = [arr[pivotIndex], arr[i]];
          // Moving to next element
          pivotIndex++;
          }
      }
      
      // Putting the pivot value in the middle
      [arr[pivotIndex], arr[end]] = [arr[end], arr[pivotIndex]] 
      return pivotIndex;
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
  padding: 10px;
}

.bar{
  flex-grow: 1;
  /* background-color: lightblue; */
  margin: 1px;
}

</style>
