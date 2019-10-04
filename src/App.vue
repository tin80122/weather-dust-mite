<template>
  <div id="app">
		<weatherPage />
  	<router-view></router-view>
  </div>
</template>

<script>
import weatherPage from './components/WeatherPage.vue'

export default {
	name: 'app',
	components: {
		weatherPage
	}
}
</script>

<style>
</style>
