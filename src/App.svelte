<script>
	import Sparkline from "sparklines"
	import items from './data'
	let div
	let canvas
	let index = null
	let current
	let data = items.reverse()
	let clientX 

	let options = {
			minValue: 0,
			maxValue: 19,
			lineWidth: 5,
			lineColor: "rgb(10, 20, 110)",
			dotRadius: 5,
			minColor: "transparent",
			maxColor: "red",
			endColor: "transparent"
		}

	const formatDate = date => {
		const [day, month, year] = date.split('.')
		return new Intl.DateTimeFormat('tr-TR', {
			dateStyle: 'long'
		}).format(new Date(`${year}-${month}-${day}`))
	}
	$: if(div){
		canvas = new Sparkline(div, {
			...options,
			width: window.innerWidth + 18,
			height: window.innerHeight / 2,
		})
	}
	$: if(index !== null) {
		current = data[index]
 		canvas.draw(
		// 	data.slice(0, index + 1).reduce((acc, current) =>{
		// 	return acc = [...acc, acc.length <= index ? parseFloat(current.now.replace(',', '.')) : 0]
		// }, [])	
		data.reduce((acc, current) =>{
			return acc = [...acc, acc.length <= index ? parseFloat(current.now.replace(',', '.')) : 0]
		}, [])	
		) 
	}
	$: if(canvas){

		document.addEventListener("mousemove", e => {
			const percent = (e.clientX / (window.innerWidth - 1) )* 100;
			const currentIndex = Math.ceil((percent * data.length) / 100)
			index = currentIndex > 0 ? currentIndex -1 : 0
			clientX = e.clientX + 'px'
			})
		}
		window.addEventListener("resize", e =>{
			canvas = new Sparkline(div, {
			...options,
			width: window.innerWidth + 18,
			height: window.innerHeight / 2,
		})
		})
</script> 

<div class="container">
	{#if clientX}
		<div class="line" style={`--position: ${clientX}`}></div>
	{/if}
	<div class="headline">
		{#if current}
			<h3>{formatDate(current.date)}</h3>
			<p>
				1 USD = {current.now} TRY
			</p>
		{:else}
			<h3>Siçanı hərəkət etdirin</h3>
		{/if}
	</div>
	<div bind:this={div} class="sparkline"></div>
</div>

<style>
.container{
	height: 100%;
	display: flex;
	flex-direction: column;
	position: relative;
}
.container .line{
	width: 1px;
	height: 100%;
	background-color: red;
	position: absolute;
	top: 0;
	left: 0;
	transform: translateX(var(--position));
}
.container > div{
	height: 50%;
}
.sparkline{
	transform: translateX(-4px);
}
.headline{
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: column;
	gap: 20px;
}
.headline h3{
	font-size: 7vh;
}
.headline p{
	font-size: 3vh;
	opacity: 0.7;
}
</style>