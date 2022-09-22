<script>
	let firstcolumn=[]
	let secondcolumn=[]
	let thirdcolumn=[]
	let fourthcolumn=[]
	let fifthcolumn=[]
	let sixthcolumn = []
	let columncollect=[
		firstcolumn, secondcolumn, thirdcolumn, fourthcolumn, fifthcolumn, sixthcolumn
	]

	let column = 0;
	let postitid = 0;
	let titleAdd="";
	let bodyAdd="";

	function addNote(){
		columncollect[column].push({id: postitid, title: titleAdd, body: bodyAdd});
		if(column<5){
			column++;
		}else{
			column=0;
		}
		postitid++;
		columncollect[column]=columncollect[column];
		console.log(columncollect)
	}

</script>

<main>
	<div id="navbar">
		<button on:click={addNote}>Click</button>
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
</main>

<style>
	#navbar{
		width: 100%;
		height: 5vh;
	}

	#notecontainer{
		display: flex;
		flex-direction: row;
		width: 100%;
		height: 95vh;
	}

	.column{
		display: flex;
		flex-direction: column;
		column-gap: 3vw;
		width:20%;
		height: fit-content;
		margin-right: 2vw;
	}

	.postit{
		display: flex;
		flex-direction: column;
		width:100%;
		height: 25vh;
		margin-bottom: 2vh;
		margin-right: 2vw;
		border-style: solid;
		border-radius: 10px;
		border-width: 1px;
	}
	
	.title{
		resize: none;
		overflow: clip;
		width: 100%;
		height: 20%;
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
		height: 80%;
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