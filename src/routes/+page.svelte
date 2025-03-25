<script lang="ts">
	import 'media-chrome';
	import Modal from '$lib/components/Modal.svelte';

	let video: HTMLVideoElement;
	let audio: HTMLVideoElement;
	let canvas: HTMLCanvasElement;
	let context: CanvasRenderingContext2D | null = $state(null);

	let width = 160;
	let height = 120;

	let showModal = $state(true);

	const src = 'https://storage.googleapis.com/muxdemofiles/mux.mp4';

	$effect(() => {
		video.addEventListener('play', () => {
			console.log('play');
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

		setTimeout(() => {
			context?.drawImage(video, 0, 0, width, height);

			if (context) {
				context.filter = 'grayscale(55%) sepia(35%)';
			}

			video.requestVideoFrameCallback(paintCanvas);
		}, 1);
	}

	function play() {
		video.play();
		paintCanvas();

		setTimeout(() => {
			audio.play();
		}, 250);
	}

	function pause() {
		video.pause();
		audio.pause();
	}
</script>

<main>
	<section class="container flex flex-col items-center">
		<section
			class="videoboy bg-secondary flex h-200 w-120 flex-col items-center rounded-xl border border-gray-300 p-8 shadow-xl"
		>
			<section class="flex items-center justify-center rounded-lg bg-gray-800 p-8">
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

					<!-- svelte-ignore a11y_media_has_caption -->
					<video class="hidden" loop {src} bind:this={audio} disablepictureinpicture></video>
				</media-controller>

				<canvas
					width="160"
					height="120"
					bind:this={canvas}
					style="display: block; visibility: visible;"
				></canvas>
			</section>

			<section>
				<button onclick={play} class="btn btn-primary round">A</button>
				<button onclick={pause} class="btn btn-primary round">B</button>
			</section>
		</section>
	</section>
</main>

<Modal bind:showModal>
	{#snippet header()}
		<h2>COOKIES</h2>
	{/snippet}

	<p>THIS SITE USES COOKIES.</p>
</Modal>

<style>
	canvas {
		width: 320px;
		height: 240px;
	}

	.round {
		border-radius: 50%;
	}
</style>
