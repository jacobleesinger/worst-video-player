<script lang="ts">
	import 'media-chrome';
	import Modal from '$lib/components/Modal.svelte';

	let video: HTMLVideoElement;
	let audio: HTMLVideoElement;
	let canvas: HTMLCanvasElement;
	let context: CanvasRenderingContext2D | null = $state(null);

	let width = 320;
	let height = 180;

	let showModal = $state(true);

	const src = 'https://stream.mux.com/DS00Spx1CV902MCtPj5WknGlR102V5HFkDe/high.mp4';

	$effect(() => {
		video.addEventListener('play', () => {
			paintCanvas();

			setTimeout(() => {
				audio.play();
			}, 250);
		});

		video.addEventListener('pause', () => {
			audio.pause();
		});

		context = canvas.getContext('2d');
	});

	function paintCanvas() {
		if (video.paused || video.ended) {
			return;
		}

		context?.drawImage(video, 0, 0, width, height);
		video.requestVideoFrameCallback(paintCanvas);
	}
</script>

<media-controller>
	<!-- svelte-ignore a11y_media_has_caption -->
	<video
		autoplay
		muted
		slot="media"
		loop
		{src}
		controls
		bind:this={video}
		disablepictureinpicture
		style="visibility: hidden; height:0; width:0;"
	></video>
	<canvas width="320" height="180" bind:this={canvas} style="display: block; visibility: visible;"
	></canvas>
	<!-- svelte-ignore a11y_media_has_caption -->
	<video class="hidden" loop {src} bind:this={audio} disablepictureinpicture></video>
</media-controller>
<Modal bind:showModal>
	{#snippet header()}
		<h2>COOKIES</h2>
	{/snippet}

	<p>THIS SITE USES COOKIES.</p>
</Modal>
