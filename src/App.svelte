<script>
    import { element } from "svelte/internal";


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

	let postitid = 0;
	let titleAdd="";
	let bodyAdd="";


	let newpost;

	function clearColumnState(currentValue){
		currentValue.length=0;
	}

	function populateColumns(currentValue, index){
		columncollect[index%6].push(currentValue);
	}

	function addNote(){
		columncollect.forEach(clearColumnState);
		allElements.unshift({id: postitid, title: titleAdd, body: bodyAdd});
		allElements.forEach(populateColumns);
		columncollect=columncollect;
		postitid++;
		newpost.value="";
		titleAdd="";
		bodyAdd="";
		console.log(columncollect)
	}

</script>

<main>
	<div id="page">
	<div id="newNote">
		<div class="newpostit">
			<textarea placeholder="Prova" id="newtitle" class="title" bind:this={newpost} bind:value={titleAdd}></textarea>
		<div id="noteAdd">
			<button id="addnote" on:click={addNote}>Click</button>
		</div>
		</div>
	</div>
	<div id="notecontainer">
		{#each columncollect as column}
			<div class="column">
				{#each column as postit}
					<div class="postit">
						<textarea class="title" bind:value={postit.title}></textarea>
						<textarea class="body" bind:value={postit.body}></textarea>
					</div>
				{/each}
			</div>
		{/each}
	</div>
	</div>
</main>

<style>
	:global(body){
		margin: 0px;
		padding: 0px;
	}

	#page{
		width: 100%;
	}
	#noteAdd{
		display: flex;
		justify-content: center;
		align-items: center;
		margin: 0px;
		width: 100%;
		height: fit-content;
		border-bottom-style: solid;
		border-bottom-width: 1px;
	}

	#addnote{
		margin-top: 2px;
		margin-bottom: 2px;
	}

	#newNote{
		display: flex;
		justify-content: center;
		width: 100%;
		height: fit-content;
	}

	.newpostit{
		width: 598px;
		white-space: 0px;
		height: fit-content;
		border-style: solid;
		border-width: 1px;
	}

	#notecontainer{
		display: flex;
		flex-direction: row;
		justify-content: center;
		width: 100%;
		height: 95vh;
	}

	.column{
		display: flex;
		margin: 5px;
		flex-direction: column;
		width: 240px;
		height: fit-content;
	}

	.postit{
		display: flex;
		flex-direction: column;
		margin-bottom: 5px;
		width:240px;
		height: fit-content;
		border-style: solid;
		border-radius: 10px;
		border-width: 1px;
	}
	
	.title{
		display: flex;
		flex-direction: column;
		align-items: center;
		resize: none;
		overflow: clip;
		width: 100%;
		height: fit-content;
		border-top-left-radius: 15px;
		border-top-right-radius: 15px;
		border-top-width: 0px;
		border-right-width: 0px;
		border-left-width: 0px;
		border-bottom-style: solid;
		border-bottom-color: black;
		outline: 0px;
		margin: 0px;
	}

	.body{
		resize: none;
		overflow: clip;
		vertical-align: text-top;
		width: 100%;
		height: fit-content;
		border: none;
		outline: none;
		margin: 0px;
		margin-bottom: 1.5vh;
		padding: 1vw;
		border-radius: 15px;
	}

	.body:focus{
		overflow: auto;
	}
</style>