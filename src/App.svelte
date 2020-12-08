<script lang="ts">
	import { onMount } from 'svelte'
	import WebCam from 'webcam-easy'
	let videoEl

	onMount(() => {
		const webcam = new WebCam(videoEl)
		webcam
			.start()
			.then(result => {
				console.log(result)
			})
			.catch(err => {
				console.error(err)
			})
	})
</script>

<main>
	<video
		bind:this={videoEl}
		autoplay
		playsinline
		width=640
		height=480
	/>
</main>

<style>
</style>