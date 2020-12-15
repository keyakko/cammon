<script lang="ts">
	import { onMount } from 'svelte'
	let videoEl: HTMLVideoElement
	let deviceList: MediaDeviceInfo[] = []
	let currentDeviceId: string = ''
	let mediaStream: null | MediaStream = null

	onMount(async () => {
		try {
			await fetchDeviceList()
			await startStream()

		} catch(e) {
			console.error(e)
		}
	})

	async function fetchDeviceList() {
		const baseDevices = await navigator.mediaDevices.enumerateDevices()
			deviceList = baseDevices.filter(d => d.kind === 'videoinput')
			if (deviceList[0] !== undefined) {
				currentDeviceId = deviceList[0].deviceId
			}
	}

	async function startStream() {
		mediaStream = await navigator.mediaDevices.getUserMedia({
				audio: false,
				video: {
					deviceId: { exact: currentDeviceId },
					width: 1920,
				}
			})
			videoEl.srcObject = mediaStream
			videoEl.onloadedmetadata = () => videoEl.play()
	}

	function stopCurrentStream() {
		if (mediaStream !== null) {
			mediaStream
				.getTracks()
				.forEach(t => t.stop())
		}
	}

	function changeCamera(e) {
		stopCurrentStream()
		currentDeviceId = e.target.value
		startStream()
	}
</script>

<main>
	<div class="video_wrapper">
		<video
			bind:this={videoEl}
			autoplay
			playsinline
			controls
		/>
	</div>
	<select
		value={currentDeviceId}
		on:change={changeCamera}
	>
		{#each deviceList as d}
			<option	value={d.deviceId}>{ d.label }</option>
		{/each}
	</select>
</main>

<style>
	.video_wrapper {
		width: 100vw;
		height: 0;
		padding-top: 56.25%;
		position: relative;
	}
	.video_wrapper > video {
		position: absolute;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		width: 100%;
		height: 100%;
	}
</style>