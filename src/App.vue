<template>
  <img alt="Vue logo" height="150" src="./assets/logo.png">
  <p>Input text: <input type="text"  v-model="code" /> </p>
  <p>Difficulty: <input type="range" min="0" max="1" step="0.1" v-model="difficulty" /> {{ difficulty }}</p>
  <p><button @click="createButtons">Create Puzzle</button></p>
  <hr>
  <p>Drag here</p>
  <!-- An outlined area (drage from) containing the buttons -->
  <div class="draggable-area">
    <div v-for="(button, index) in buttons" :key="index" class="draggable" draggable="true" @dragstart="dragStart(index)">
      <button id="index">{{ button }}</button> 
    </div>
  </div>
  <!-- A horizontal line separator -->
  <hr>
  <p>Drop here</p>
  <!-- An outlined area (drop to) containing the slots when a button is dopped the text is exchanged with the dragged button -->
  <div class="droppable-area">
    <div v-for="(slot, index) in slots" :key="index" class="droppable" @dragover.prevent @drop="dragDrop(index)">
      <button id="index">{{ slot }}</button> 
    </div>
  </div>

  <p><button @click="checkPuzzle">Check</button></p> 
  <p v-if="checkResult === true">Correct!</p>
  <p v-else-if="checkResult === false">Incorrect!</p>
  
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {},
  //data stores variables used in the template
  data: function() {
    return {
      original: [],
      buttons: [],
      slots: [],
      code: "We hold these truths to be self-evident",
      difficulty: 0.5,
      empty: "EMPTY",
      fromIndex: -1,
      toIndex: -1,
      checkResult: false
    }
  },
  //methods stores functions used in the template
  methods: {
    //the function createButtons splits the code into an array of words
    //and creates a button for each word
    createButtons: function() {
      console.log("create");
      //empty the buttons array
      this.original=[];this.buttons=[];this.slots=[];
      //split the code into an array of words
      var words = this.code.split(" ")
      //for each word in the array
      words.forEach(
        word => {
          word = word.trim();
          if (word.length>0) {
            //add the button to the buttons array
            this.original.push(word);
            // generate a random number with probability equal to difficulty
            if (Math.random() < this.difficulty) {
              this.buttons.push(word);
              this.slots.push(this.empty);
            } else {
              this.slots.push(word);
            }
            //shuffle the array buttons
            this.buttons.sort(() => Math.random() - 0.5);

          }
        }

      );
    },
    dragStart: function(index) {
      this.fromIndex = index;
    },      
    dragDrop:function(index) {
      this.toIndex = index;
      var from = this.buttons[this.fromIndex];
      var to = this.slots[this.toIndex];
      var temp = to;
      this.slots[this.toIndex] = from;
      this.buttons[this.fromIndex] = temp;
    },
    checkPuzzle: function() {
    console.log("check");
    var correct = true;
    for (var i=0; i<this.original.length; i++) {
      if (this.original[i] != this.slots[i]) {
        correct = false;
        break;
      }
    }
    this.checkResult = correct;
  }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
