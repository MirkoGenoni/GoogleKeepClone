<svelte:head>
	<link href="//fonts.googleapis.com/css?family=Google+Sans:400,500&amp;subset=cyrillic,cyrillic-ext,greek,greek-ext,latin,latin-ext,vietnamese" rel="stylesheet" nonce="">
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
</svelte:head>
<script>
    import NewNote from "./NewNote.svelte"
	import { afterUpdate } from 'svelte';
	import Toolbar from "./Toolbar.svelte"

	let firstcolumn=[]
	let secondcolumn=[]
	let thirdcolumn=[]
	let fourthcolumn=[]
	let fifthcolumn=[]
	let sixthcolumn = []
	let columncollect=[
		firstcolumn, secondcolumn, thirdcolumn, fourthcolumn, fifthcolumn, sixthcolumn
	]
	let allElements = [];

	function clearColumnState(currentValue){
		currentValue.length=0;
	}

	function populateColumns(currentValue, index){
		columncollect[index%6].push(currentValue);
	}

	function addNote(){
		columncollect.forEach(clearColumnState);
		allElements.forEach(populateColumns);
		columncollect=columncollect;
	}

	const fullpostit = [];
	let imagescontainer = [];
	let images = {};
	const titles = [];
	const bodies = [];

	afterUpdate(async () => {
		titles.forEach((title, index) => {
			/*fix to white note border color, the background is set to white but the border must be set to #e0e0e0*/
			let backgroundCol = getComputedStyle(fullpostit[index]).getPropertyValue("background-color");
			if(backgroundCol!=="rgb(255, 255, 255)"){
				fullpostit[index].style="border-color: " + backgroundCol + ";" + "--background-color: " + backgroundCol + ";"
			} else {
				fullpostit[index].style="border-color: #e0e0e0;" + "--background-color: " + backgroundCol + ";"
			}

			title.style = "height: 0px;"
			bodies[index].style = "height: 0px;"
			images[index].style = "height: 0px;"
			let imageclass = new String(images[index].getAttribute("class"));

			if(title.value==="" && bodies[index].value==="" && !imageclass.includes("noimage")){
				imagescontainer[index].style="height:100%;";
				images[index].style = "border-radius: 8px;"

				title.parentElement.style = "padding: 0px; height: 0px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			}

			if((title.value!=="" && bodies[index].value==="") || ((title.value!=="" && bodies[index].value!=="")) && !imageclass.includes("noimage")){
				imagescontainer[index].style="height:154px;";
				images[index].style = "border-top-left-radius: 8px; border-top-right-radius:8px;";

				title.parentElement.style="height: fit-content;  padding: 12px 16px 12px 16px;"
				title.style = "height: " + title.scrollHeight + "px;  max-height: 48px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			}

			if((title.value==="" && bodies[index].value!=="") && !imageclass.includes("noimage")){
				imagescontainer[index].style="height:154px;";
				images[index].style = "border-top-left-radius: 8px; border-top-right-radius:8px;";

				bodies[index].parentElement.style = "height: fit-content; padding: 12px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px; max-height: 60px;"

				title.parentElement.style = "padding: 0px; height: 0px;"
			}

			if(title.value!=="" && bodies[index].value==="" && imageclass.includes("noimage")){
				title.parentElement.style="height: fit-content;  padding: 12px 16px 12px 16px;"
				title.style = "height: " + title.scrollHeight + "px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			} 

			if(bodies[index].value!=="" && title.value==="" && imageclass.includes("noimage")) {
				bodies[index].parentElement.style = "height: fit-content; padding: 12px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px;"

				title.parentElement.style = "padding: 0px; height: 0px;"

			}
			
			if(title.value!=="" && bodies[index].value!=="" && title.scrollHeight+bodies[index].scrollHeight<193 && imageclass.includes("noimage")){
				title.parentElement.style="height: fit-content;  padding: 12px 16px 5px 16px;"
				title.style = "height: " + title.scrollHeight + "px;"

				bodies[index].parentElement.style = "height: fit-content; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px;"
			}

			if(title.value!=="" && bodies[index].value!=="" && title.scrollHeight+bodies[index].scrollHeight>193
			   && title.scrollHeight<=bodies[index].scrollHeight && imageclass.includes("noimage")){
				title.parentElement.style="height: 48px;  padding: 12px 16px 5px 16px;"
				title.style = "height: 48px;"

				bodies[index].parentElement.style = "height: fit-content; max-height: 144px; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px; max-height: 144px;"
			}

			if(title.value!=="" && bodies[index].value!=="" && title.scrollHeight+bodies[index].scrollHeight>193
			   && title.scrollHeight>bodies[index].scrollHeight && imageclass.includes("noimage")){
				title.parentElement.style="height: fit-content; max-height: 138px; padding: 12px 16px 5px 16px;"
				title.style = "height: " + title.scrollHeight + "px; max-height: 138px;"

				bodies[index].parentElement.style = "height: 54px; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: 60px;"
			}
		});
	});

	let isPalette = false;
	let currentBackground ="--background-color: #e0e0e0;";

	function submitimage(){

	}
	const setBackground = (color) => {
		currentBackground = "--background-color: "+ color +";";
	}
</script>

<main>
	<NewNote {allElements} {addNote}/>
	<div id="notecontainer">
		{#each columncollect as column, i}
			<div class="column">
				{#each column as postit}
					<div class="postit" bind:this={fullpostit[i]} style={postit.colorbkg}>
						{#if postit.image!=""}
							<div id={"image" + i} class="noteimagecontainer" bind:this={imagescontainer[i]}>
								<img class="noteimage" bind:this={images[i]} src={postit.image} alt="note"/>
							</div>
						{:else}
							<div class="noimage" bind:this={images[i]}></div>
						{/if}
						<div id={"titlecontainer" + i} class="textcontainer">
							<textarea readonly class="titlepostit" bind:this={titles[i]} bind:value={postit.title} style={postit.colorbkg}></textarea>
						</div>
						<div id={"bodycontainer" + i} class="textcontainer">
							<textarea readonly class="bodypostit" bind:this={bodies[i]} bind:value={postit.body} style={postit.colorbkg}></textarea>
						</div>
						<div id="toolbarcontainer" style={postit.colorbkg}>
							<Toolbar {isPalette} {setBackground} {submitimage} mini={true}/>
						</div>
					</div>
				{/each}
			</div>
		{/each}
	</div>
</main>

<style>
	
	:global(body){
		height: fit-content;
		margin: 0px;
		padding: 0px;
		font-family: "Google Sans", Roboto, Arial, sans-serif;
	}

	.noimage{
		height: 0px;
	}

	#notecontainer{
		display: flex;
		flex-direction: row;
		justify-content: center;
		width: 100%;
		height: fit-content;
	}

	.column{
		display: flex;
		margin: 5px;
		flex-direction: column;
		width: 240px;
		height: max-content;
	}

	.postit{
		position:relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		position: relative;
		margin-bottom: 5px;
		width: 240px;
		height: 260px;
		border: solid 1px transparent;
		border-radius: 8px;
		border-color: #e0e0e0;
		background-color: var(--background-color);
	}

	.postit:hover{
		box-shadow: 0 1px 2px 0 rgb(60 64 67 / 30%), 0 1px 3px 1px rgb(60 64 67 / 15%);
	}
	
	.postit:hover > #toolbarcontainer{
		opacity: 100%;
	}

	.textcontainer{
		width: 208px;
		height: fit-content;
		padding-top: 12px;
		padding-left: 16px;
		padding-right: 16px;
		padding-bottom: 0px;
	}

	.noteimagecontainer{
		width: 240px;
		height: 154px;
		display: flex;
		justify-content: center;
		border-top: none;
		border-top-left-radius: 8px;
		border-top-right-radius: 8px;
	}

	.noteimage{
		width: 100%;
		object-fit:cover;
		object-position: 0%;
		border-top-left-radius: 8px;
		border-top-right-radius: 8px;
	}

	.titlepostit{
		cursor: default;
		color: #202124;
		font-size: 16px;
		letter-spacing: .00625em;
		line-height: 1.5rem;
		font-weight: 500;
		resize: none;
		overflow: clip;
		width: 100%;
		max-height: 197px;
		padding: 0px;
		outline: 0px;
		margin: 0px;
		border: none;
		white-space: pre-wrap;
		background-color: var(--background-color);
	}

	#bodycontainer{
		padding-top: 0px;
	}

	.bodypostit{
		cursor: default;
		color: #202124;
		font-size: 14px;
		letter-spacing: .01428571em;
		line-height: 1.25rem;
		font-weight: 400;
		resize: none;
		overflow: clip;
		vertical-align: text-top;
		width: 100%;
		max-height: 199px;
		border: none;
		outline: none;
		margin: 0px;
		padding: 0px;
		white-space: pre-wrap;
		user-select: none;
		background-color: var(--background-color);
	}

	.bodypostit:focus{
		overflow: auto;
	}

	#toolbarcontainer{
		position: absolute;
		margin-bottom: 5px;
		bottom : 0px;
		width: 210px;
		margin-left: 15px;
		margin-right: 15px;
		display: flex;
		flex-direction: row;
		justify-content: center;
		opacity: 0%;
		border-radius: 8px;
		background-color: var(--background-color);
	}
</style>