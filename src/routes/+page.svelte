<script lang="ts">
	import { onMount } from 'svelte';

	let link = '';
	let imgString = [];
	let img;
	let ctx;
	let imgData;
	let char = [
		'$',
		'@',
		'B',
		'%',
		'8',
		'&',
		'W',
		'M',
		'#',
		'*',
		'o',
		'a',
		'h',
		'k',
		'b',
		'd',
		'p',
		'q',
		'w',
		'm',
		'Z',
		'O',
		'0',
		'Q',
		'L',
		'C',
		'J',
		'U',
		'Y',
		'X',
		'z',
		'c',
		'v',
		'u',
		'n',
		'x',
		'r',
		'j',
		'f',
		't',
		'/',
		'\\',
		'|',
		'(',
		')',
		'1',
		'{',
		'}',
		'[',
		']',
		'?',
		'-',
		'_',
		'+',
		'~',
		'<',
		'>',
		'i',
		'!',
		'l',
		'I',
		';',
		':',
		',',
		'^',
		'`',
		'.'
	];
	console.log(char.length);

	const getAllChars = () => {
		let averages = getAllAverages();
		let characters = [];

		for (let i = 0; i < averages.length; i++) {
			let char = getChar(averages[i]);
			characters.push(char);
		}
		console.log(characters);
		imgString = characters;
		console.log(imgString);
	};

	const getAllAverages = () => {
		let averageArr = [];
		for (let y = 0; y < 500; y += 10) {
			for (let x = 0; x < 500; x += 10) {
				let avg = getAverageBrightness(x, y, 10, 10);
				averageArr.push(avg);
			}
		}
		return averageArr;
	};

	const getChar = (brightness: number) => {
		// 6.7 coming from arr length 67
		let charArrLen = char.length;
		let maxBrightness = 100;
		let index = Math.floor((charArrLen / maxBrightness) * brightness);
		return char[index];
	};

	const handleFileInput = (e) => {
		link = URL.createObjectURL(e.target.files[0]);
		draw();
	};

	onMount(() => {
		draw();
	});
	const draw = () => {
		const ctx = document.getElementById('canvas').getContext('2d');
		const img = new Image();
		img.src = '/testing.jpg';
		img.onload = () => {
			ctx.drawImage(img, 0, 0, 500, 500);
		};
	};

	const getAverageBrightness = (x: number, y: number, w: number, height: number) => {
		console.log('this ran too');
		let initX = x;
		let initY = y;
		let reds = [];
		let greens = [];
		let blues = [];
		for (let y = 0; y < height; y++) {
			for (let x = 0; x < w; x++) {
				let currentX = x + initX;
				let currentY = y + initY;
				let color = getColor(currentX, currentY);
				reds.push(color[0]);
				greens.push(color[1]);
				blues.push(color[2]);
			}
		}
		let redSum = reds.reduce((acc, cur) => acc + cur, 0);
		let redAvg = Math.round(redSum / reds.length);
		let greenSum = greens.reduce((acc, cur) => acc + cur, 0);
		let greenAvg = Math.round(greenSum / greens.length);
		let blueSum = blues.reduce((acc, cur) => acc + cur, 0);
		let blueAvg = Math.round(blueSum / blues.length);

		return Math.round((redAvg + greenAvg + blueAvg) / 3 / 10) * 4;
	};

	const getColor = (x: number, y: number) => {
		if (!ctx && !imgData) {
			ctx = document.getElementById('canvas').getContext('2d');
			imgData = ctx.getImageData(0, 0, 500, 500);
		}
		let index = (x + y * 500) * 4;
		const red = imgData.data[index];
		const green = imgData.data[index + 1];
		const blue = imgData.data[index + 2];
		//ctx.fillRect(x, y, 10, 10);
		return [red, green, blue];
	};
</script>

<div class="container">
	<h1>Image to A S C I I</h1>
	<div class="wrap">
		<div>
			<div class="menu">
				<h2>Input an image</h2>
				<p class="link">{link}</p>
				<input on:change={draw} class="linkInput" bind:value={link} placeholder="Add a link here" />
				<input class="fileInput" type="file" on:change={handleFileInput} />
			</div>
			<button class="convertBtn">Convert to ASCII art</button>
			<button on:click={() => getColor(300, 300)}>Get color</button>
			<button on:click={() => getAllChars()}>Get color average</button>
		</div>
		<canvas id="canvas" height="500" width="500" class="image" />
	</div>
	{#if imgString.length > 0}
		<div class="asciiImg">
			{#each imgString as s}
				<div class="letter">{s !== undefined ? s : ' '}</div>
			{/each}
		</div>
	{/if}
</div>

<style>
	* {
		font-family: 'Almarai', sans-serif;
	}
	.letter {
		width: 10px;
		height: 10px;
		font-size: 10px;
		text-align: center;
	}
	.asciiImg {
		width: 500px;
		height: 500px;
		font-size: 10px;
		font-family: 'VT323', monospace;
		display: flex;
		flex-wrap: wrap;
	}
	.test {
		font-family: 'VT323', monospace;
		font-size: 10px;
	}
	.container {
		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.wrap {
		margin-top: 4rem;
		display: flex;
		flex-direction: row;
		gap: 3rem;
	}
	.menu {
		margin: auto;
		display: flex;
		flex-direction: column;
		width: 15rem;
		gap: 1rem;
		padding: 2rem;
		border: 1px solid rgb(177, 46, 138);
		background-color: whitesmoke;
		border-radius: 10px;
		border-bottom-left-radius: 0;
		border-bottom-right-radius: 0;
	}
	button,
	input::file-selector-button {
		padding: 0.5rem;
		background-color: rgb(177, 46, 138);
		color: white;
		border-radius: 10px;
		border: none;
		cursor: pointer;
		margin-right: 0.5rem;
	}
	.convertBtn {
		width: 100%;
		border-top-right-radius: 0;
		border-top-left-radius: 0;
		padding: 1rem;
	}
	input {
		border: rgb(177, 46, 138) solid 1px;
		border-radius: 5px;
		width: 10rem;
	}
	.fileInput {
		border: none;
	}
	.linkInput {
		height: 1.5rem;
		background-color: transparent;
		padding: 0.5rem;
	}

	h2 {
		margin: auto;
	}
	.image {
		height: 500px;
		width: 500px;
	}
	.link {
		font-style: italic;
	}
	p {
		width: 6rem;
		overflow: hidden;
	}
</style>
