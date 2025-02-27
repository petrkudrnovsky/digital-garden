Useful links:
[Vue.js official - examples of code](https://vuejs.org/examples/#hello-world)

There are two ways to use Vue.js - Option API (traditional, older) and Composition API (newer, better)
- [Vue.js project setup](https://vuejs.org/guide/quick-start.html) 

[[Vue.js troubleshooting]]
### Get Started
- using Vue.js 3 and the Options API (having data, methods etc.)
```js
import { createApp } from 'vue'

createApp({
  data() {
    return {
      titleClass: 'title'
    }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}).mount('#app')
```
### [v-bind](https://vuejs.org/guide/essentials/template-syntax.html)
- binds a dynamic property from `data()` to the attribute
```html
<div id="app">
  <h1 v-bind:class="titleClass">Make me red</h1>
  <h1 :class="titleClass">Make me red</h1>
</div>
```
- both ways are correct, `v-bind` is used so often, that is has it's own short
### v-on
```html
<div id="app">
  <button v-on:click="increment">Count is: {{ count }}</button>
  <button @click="increment">Count is: {{ count }}</button>
</div>
```

Možný layout v komponentě - `<style scoped>` znamená, že dané styly ovlivní pouze tu komponentu a nic jiného
```html
<template>
  <h2>Expense Tracker</h2>
</template>

<script>

</script>

<style scoped>

</style>
```


### Value-key v-for loop
```html
<button v-for="(value, key) in spielparameter.miner" v-on-click="increaseModifier(key)">{{ value.name }}</button>
```
### Options API vs. Composition API
```html
<script>
  export default {
    data() {
      return {
        transactions: [
          { id: 1, text: 'Flower', amount: "-9" },
          { id: 2, text: 'Paycheck', amount: "10" }
        ]
      }
    }, 
    methods: {
      
    }
  }
</script>
```

```html
<script>
  export default {
    setup() {
      const transactions = [
        { id: 1, text: 'Flower', amount: "-9" },
        { id: 2, text: 'Paycheck', amount: "10" }
      ]

      function myFunction() {
        console.log("example")
      }

      return {
        transactions,
        myFunction
      }
    }
  }
</script>
```
Nejnovější a nejlepší způsob (od Vue 3.2) 
```html
<script setup>
  const transactions = [
    { id: 1, text: 'Flower', amount: "-9" },
    { id: 2, text: 'Paycheck', amount: "10" }
  ]

  function myFunction() {
    console.log("dd")
  }
</script>
```