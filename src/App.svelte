<svelte:head>
	<link href="//fonts.googleapis.com/css?family=Google+Sans:400,500&amp;subset=cyrillic,cyrillic-ext,greek,greek-ext,latin,latin-ext,vietnamese" rel="stylesheet" nonce="">
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
</svelte:head>
<script>
    import NewNote from "./NewNote.svelte"

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
	let bo = 0;

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

</script>

<main>
	<NewNote {allElements} {addNote}/>
	<div id="notecontainer">
		{#each columncollect as column}
			<div class="column">
				{#each column as postit}
					<div class="postit">
						<div class="textcontainer">
							<textarea readonly id="postittitle" class="title" bind:value={postit.title}></textarea>
						</div>
						<div id="bodycontainer" class="textcontainer">
							<textarea readonly id="postitbody" class="body" bind:value={postit.body}></textarea>
						</div>
					</div>
				{/each}
			</div>
		{/each}
	</div>
</main>

<style>
	
	:global(body){
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
		height: fit-content;
	}

	.postit{
		display: flex;
		flex-direction: column;
		position: relative;
		margin-bottom: 5px;
		width:240px;
		height: fit-content;
		border: solid 1px transparent;
		border-radius: 8px;
		border-color: #e0e0e0;
	}
	
	.textcontainer{
		height: 48px;
		padding-top: 12px;
		padding-left: 16px;
		padding-right: 16px;
		padding-bottom: 0px;
	}

	.title{
		color: #202124;
		font-size: 15px;
		color: black;
		letter-spacing: .00625em;
		line-height: 1.5rem;
		font-weight: 500;
		resize: none;
		overflow: hidden;
		width: 100%;
		height: 100%;
		padding: 0px;
		outline: 0px;
		margin: 0px;
		border: none;
		white-space: pre-wrap;
	}

	#bodycontainer{
		padding-top: 0px;
	}

	.body{
		font-size: 14px;
		resize: none;
		overflow: clip;
		vertical-align: text-top;
		width: 100%;
		height: auto;
		overflow: auto;
		border: none;
		outline: none;
		margin: 0px;
		margin-bottom: 1.5vh;
		padding: 4px;
		border-radius: 15px;
		white-space: pre-wrap;
	}

	.body:focus{
		overflow: auto;
	}

	#postitbody{
		user-select: none;
	}
</style>