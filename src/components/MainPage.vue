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
      <div v-for="(obj, idx) in array" :key="idx" class="bar" :style="{height: '100%', 'background-color': obj.backColor ? obj.backColor : ''}">
        <div :style="{height: (obj.num / array.length) * 80 + '%', 'background-color':obj.color}"></div>
      </div>
    </div>
    <div class="footer">
      <button type="button" class="btn btn-primary" @click="sortArray()" :disabled="running">Sort Array</button>
    </div>
  </div>
</template>

<script>

import Vue from "vue";
import Toast from "vue-toastification";
import "vue-toastification/dist/index.css";

Vue.use(Toast, {
  transition: "Vue-Toastification__bounce",
  maxToasts: 20,
  newestOnTop: true
});

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
        "Quick Sort": this.quickSortAux,
        "Merge Sort": this.mergeSortAux,
      },
      speeds:{1:100, 2:50, 3:25, 4:10, 5:0},
      sizes:[5, 10, 20, 40, 60, 80, 100, 250],
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
      return Array.from(new Array(n), (x,i) => {return {'num':i+1, 'color':'lightblue', 'backColor':""}})
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
        let message = `It took ${time} ms to sort an array of size ${this.selectedSize} with ${this.selectedAlgorithm} - speed: ${this.selectedSpeed}`
        this.$toast.success(message, {
          position: "top-right",
          timeout: 5000,
          closeOnClick: true,
          pauseOnFocusLoss: true,
          pauseOnHover: true,
          draggable: true,
          draggablePercent: 0.6,
          showCloseButtonOnHover: false,
          hideProgressBar: true,
          closeButton: "button",
          icon: true,
          rtl: false
        });
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
        for (let i = 0; i < len - 1; i++) {
          this.array[i].color = 'red'
          this.array[i + 1].color = 'red'
          if (this.array[i].num > this.array[i + 1].num) {
            let tmp = this.array[i];
            this.array[i] = this.array[i + 1];
            this.array[i + 1] = tmp;
            swapped = true;
          }
          this.arrayKey += 1;
          await this.delay(this.speeds[this.selectedSpeed])
          this.array[i].color = 'lightblue'
          this.array[i+1].color = 'lightblue'
        }
      } while (swapped);
      //Change all bar colors to lightblue after finish
      for (let obj of this.array){
        obj.color = 'lightblue'
      }
    },

    async quickSortAux(){
      // await this.quickSort(this.array, 0, this.array.length - 1)
      await this.quickSort(0, this.array.length - 1)
    },

    async quickSort(start, end){
      //Show area that is being worked on
      for (let i = start; i <= end; i++){
        this.array[i].backColor = '#d8e6e1'
      }
      this.arrayKey++;

      
      //At the beggining start is 0 and end is length - 1
      let n = (end - start) + 1
      // If n <= 1 means length is 1 or 0 so array is already sorted
      if (n <= 1){
        //Area back to normal
        for (let i = start; i <= end; i++){
          this.array[i].backColor = ''
        }
        return
      } 

      let pivotIdx =  start + Math.floor(n / 2)
      for (let i = start ; i <= end ; i++){
        this.array[pivotIdx].color = "yellow"        
        this.arrayKey++;
        await this.delay(this.speeds[this.selectedSpeed])
        if (i == pivotIdx){
          //Empty
        } else if (this.array[i].num <= this.array[pivotIdx].num && i > pivotIdx) {
          //Delete element from its position and send it to start index
          this.array.splice(start, 0, this.array.splice(i, 1)[0])
          //Move pivot to the right
          pivotIdx++;
          this.array[pivotIdx - 1].color = "lightblue"
          this.array[pivotIdx].color = "yellow"
        } else if (this.array[i].num >= this.array[pivotIdx].num && i < pivotIdx){
          //Delete element from its position and send it to end index
          this.array.splice(end, 0, this.array.splice(i, 1)[0])
          //Move pivot to the left and substract from i so stays in same index
          pivotIdx--;
          this.array[pivotIdx + 1].color = "lightblue"
          this.array[pivotIdx].color = "yellow"
          i--;
        }
        // console.log(`ITER: ${i}, Start: ${start}, End: ${end}, Pivot: ${pivotIdx}, Array: ${JSON.stringify(Array.from(this.array, element => element.num))}`)
      }
      this.array[pivotIdx].color = "lightblue"
      //Call recursive of left part plus recursive of right part

      //Area back to normal
      for (let i = start; i <= end; i++){
        this.array[i].backColor = ''
      }
      this.arrayKey++;

      await this.quickSort(start, pivotIdx - 1)
      await this.quickSort(pivotIdx + 1, end)
      this.arrayKey++;
    },

    async mergeSortAux(){
      await this.mergeSort(0, this.array.length - 1)
    },

    async merge(start, mid, end) {      
      let start2 = mid + 1
      // If the direct merge is already sorted
      if (this.array[mid].num <= this.array[start2].num){
        return
      }
      // Two pointers to maintain start
      // of both arrays to merge
      while (start <= mid && start2 <= end){
        this.array[start].color = 'red'
        this.array[start2].color = 'red'
        this.arrayKey++;
        await this.delay(this.speeds[this.selectedSpeed])
        // If element 1 is in right place
        if (this.array[start].num <= this.array[start2].num){
          start += 1
        } 
        else{
          let value = this.array[start2]
          let index = start2
          // Shift all the elements between element 1
          // element 2, right by 1.
          while (index != start){
            this.array[index] = this.array[index - 1]
            index -= 1
          }
          this.array[start] = value
          // Update all the pointers
          start += 1
          mid += 1
          start2 += 1
  
          this.array[start2 - 1].color = 'lightblue'
        }
        this.array[start - 1].color = 'lightblue'
        this.arrayKey++;
      }
      //Change all bar colors to lightblue after finish
      for (let obj of this.array){
        obj.color = 'lightblue'
      }
    },

    async mergeSort(left, right) {
      if (left < right){ 
        let mid = left + Math.floor((right - left) / 2)

        // Sort first and second halves
        await this.mergeSort(left, mid)
        await this.mergeSort(mid + 1, right)

        //Show area that is being worked on
        for (let i = left; i <= right; i++){
          this.array[i].backColor = '#d8e6e1'
        }
        this.arrayKey++;

        await this.merge(left, mid, right)

        //Area back to normal
        for (let i = left; i <= right; i++){
          this.array[i].backColor = ''
        }
        this.arrayKey++;
      }
    },

    abort(){
      throw new Error('This is not an error. This is just to abort javascript');
    },

  },    
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.btn{
  margin: 0px 20px;
}

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
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  /* background-color: lightblue; */
  margin: 1px;
}

</style>
