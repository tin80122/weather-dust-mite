<template>
  <div id="app">
		<!-- <HelloWorld msg="My first component" /> -->
  	<router-view></router-view>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
	name: 'app',
	components: {
		HelloWorld
	}
}
</script>

<style>
</style>
