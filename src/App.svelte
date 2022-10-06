<svelte:head>
	<link href="//fonts.googleapis.com/css?family=Google+Sans:400,500&amp;subset=cyrillic,cyrillic-ext,greek,greek-ext,latin,latin-ext,vietnamese" rel="stylesheet" nonce="">
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
</svelte:head>
<script>
    import NewNote from "./NewNote.svelte"
	import { afterUpdate } from 'svelte';

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

	const titles = [];
	const bodies = [];

	afterUpdate(async () => {
		titles.forEach((title, index) => {
			title.style = "height: 0px;"
			bodies[index].style = "height: 0px;"
			if(title.value!=="" && bodies[index].value===""){
				console.log(title.value)
				title.parentElement.style="height: fit-content;  padding: 12px 16px 12px 16px;"
				title.style = "height: " + title.scrollHeight + "px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			} 

			if(bodies[index].value!=="" && title.value==="") {
				bodies[index].parentElement.style = "height: fit-content; padding: 12px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px;"

				title.parentElement.style = "padding: 0px; height: 0px;"

			}
			
			if(title.value!=="" && bodies[index].value!=="" && title.scrollHeight+bodies[index].scrollHeight<193){
				title.parentElement.style="height: fit-content;  padding: 12px 16px 5px 16px;"
				title.style = "height: " + title.scrollHeight + "px;"

				bodies[index].parentElement.style = "height: fit-content; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px;"
			}

			if(title.value!=="" && bodies[index].value!=="" && title.scrollHeight+bodies[index].scrollHeight>193
			   && title.scrollHeight<=bodies[index].scrollHeight){
				title.parentElement.style="height: 48px;  padding: 12px 16px 5px 16px;"
				title.style = "height: 48px;"

				bodies[index].parentElement.style = "height: fit-content; max-height: 144px; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px; max-height: 144px;"
			}

			if(title.value!=="" && bodies[index].value!=="" && title.scrollHeight+bodies[index].scrollHeight>193
			   && title.scrollHeight>bodies[index].scrollHeight){
				title.parentElement.style="height: fit-content; max-height: 138px; padding: 12px 16px 5px 16px;"
				title.style = "height: " + title.scrollHeight + "px; max-height: 138px;"

				bodies[index].parentElement.style = "height: 54px; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: 54px;"
			}
		});
	});

</script>

<main>
	<NewNote {allElements} {addNote}/>
	<div id="notecontainer">
		{#each columncollect as column, i}
			<div class="column">
				{#each column as postit}
					<div class="postit">
						<div id={"titlecontainer" + i} class="textcontainer">
							<textarea readonly class="titlepostit" bind:this={titles[i]} bind:value={postit.title}></textarea>
						</div>
						<div id={"bodycontainer" + i} class="textcontainer">
							<textarea readonly class="bodypostit" bind:this={bodies[i]} bind:value={postit.body}></textarea>
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
		display: flex;
		flex-direction: column;
		position: relative;
		margin-bottom: 5px;
		width: 240px;
		height: 260px;
		border: solid 1px transparent;
		border-radius: 8px;
		border-color: #e0e0e0;
	}
	
	.textcontainer{
		height: fit-content;
		padding-top: 12px;
		padding-left: 16px;
		padding-right: 16px;
		padding-bottom: 0px;
	}

	.titlepostit{
		color: #202124;
		font-size: 15px;
		color: black;
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
	}

	#bodycontainer{
		padding-top: 0px;
	}

	.bodypostit{
		font-size: 14px;
		resize: none;
		overflow: clip;
		vertical-align: text-top;
		width: 100%;
		max-height: 197px;
		border: none;
		outline: none;
		margin: 0px;
		padding: 0px;
		white-space: pre-wrap;
	}

	.bodypostit:focus{
		overflow: auto;
	}

</style>