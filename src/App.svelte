<svelte:head>
	<link href="//fonts.googleapis.com/css?family=Google+Sans:400,500&amp;subset=cyrillic,cyrillic-ext,greek,greek-ext,latin,latin-ext,vietnamese" rel="stylesheet" nonce="">
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
</svelte:head>
<script>
    import { tick } from "svelte";
	
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
	let clicked = false;
	let dimensionintBody;
	let dimensionintTitle;

	let styleBody = "--height: 20px";
	let styleTitle = "--height: 24px";
	let newpostTitle;
	let newpostBody;

	function setClick(){
		clicked= true;
	}

	async function dynamicResizeBody(props){
		if(props=="body"){
			styleBody = "--height: 0";
			await tick();
			dimensionintBody = newpostBody.scrollHeight;
			styleBody= "--height: " + dimensionintBody.toString() + "px";
		} else {
			styleTitle = "--height: 0";
			await tick();
			dimensionintTitle = newpostTitle.scrollHeight;
			styleTitle= "--height: " + dimensionintTitle.toString() + "px";
		}
	}

	function clearColumnState(currentValue){
		currentValue.length=0;
	}

	function populateColumns(currentValue, index){
		columncollect[index%6].push(currentValue);
	}

	function closeNote(){
		clicked=false;
		newpostTitle.value="";
		newpostBody.valute="";
		titleAdd="";
		bodyAdd="";
	}

	function addNote(){
		clicked=false;
		columncollect.forEach(clearColumnState);
		allElements.unshift({id: postitid, title: titleAdd, body: bodyAdd});
		allElements.forEach(populateColumns);
		columncollect=columncollect;
		postitid++;
		newpostTitle.value="";
		newpostBody.valute="";
		titleAdd="";
		bodyAdd="";
		console.log(columncollect)
	}

	function outsideClick(node){
		const handleClick = (event) => {
			if (!node.contains(event.target) && clicked===true) {
				node.dispatchEvent(new CustomEvent("outclick"));
			}
		};
		
		document.addEventListener("click", handleClick, true);
		return {
		destroy() {
			document.removeEventListener("click", handleClick, true);
			}
		};
	}
</script>

<main>
	<div id="newNote">
		<div class="newpostit" use:outsideClick on:outclick={addNote}>
			<div id="titlewithimage">
				<div id="newtitlecontainer" class="textcontainer" on:click={setClick}>
					<textarea placeholder={clicked===false ? "Scrivi una nota..." : "Titolo..."} id="newtitle" class="title" bind:this={newpostTitle} bind:value={titleAdd} on:input={()=>dynamicResizeBody("title")} style={styleTitle}></textarea>
				</div>
				{#if !clicked}
				<div id="iconcontainer">
					<span id="toolbarclosedimage" class="material-symbols-outlined">image</span>
				</div>
				{/if}
			</div>
			{#if clicked}
				<div id="newbodycontainer" class="textcontainer">
					<textarea placeholder="Scrivi una nota..." id="newbody" class="body" bind:this={newpostBody} bind:value={bodyAdd} on:input={()=>dynamicResizeBody("body")} style={styleBody}></textarea>
				</div>
				<div id="toolbarcontainer">
					<div id="tools">
						<span id="toolbaropenimage" class="material-symbols-outlined">image</span>
						<span id="toolbaropenpalette" class="material-symbols-outlined">palette</span>
					</div>
					<button id="closenote" on:click={closeNote}>Chiudi</button>
			</div>
			{/if}
		</div>
	</div>
	<div id="notecontainer">
		{#each columncollect as column}
			<div class="column">
				{#each column as postit}
					<div class="postit">
						<div class="textcontainer">
							<textarea class="title" bind:value={postit.title}></textarea>
						</div>
						<textarea class="body" bind:value={postit.body}></textarea>
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

	/*container per tutta la pagina*/
	#page{
		width: 100%;
	}

	/*FORM NUOVA NOTA*/
	/*Container div per centrare form newpostit*/
	#newNote{
		display: flex;
		justify-content: center;
		width: 100%;
		height: fit-content;
		margin-top: 32px;
		margin-bottom: 16px;
	}

	/*Box nuova nota, contiene form per titolo e boty e tasto per confermare*/
	.newpostit{
		box-sizing: border-box;
		width: 598px;
		white-space: 0px;
		height: fit-content;
		box-shadow: 0 1px 2px 0 rgb(60 64 67 / 30%), 0 2px 6px 2px rgb(60 64 67 / 15%);
		border: solid 1px transparent;
		border-radius: 8px;
		border-color: #e0e0e0;
	}

	/*TEXTAREA TITOLO*/
	/*Barra titolo chiusa con icona imagine*/
	#titlewithimage{
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content:space-between;
	}

	/*Container per dare padding alla textarea del titolo*/
	#newtitlecontainer{
		width: 80%;
		display: flex;
		height: auto;
		padding-top: 10px;
		padding-bottom: 10px;
		padding-left: 15px;
		padding-right: 15px;
	}
	/*Proprietà aggiuntive per titolo nuovo postit*/
	#newtitle{
		height: var(--height);
		font-size: 16px;
		color: rgba(0,0,0,0.702);
		letter-spacing: 0.00625em;
		font-weight: 500;
		line-height: 1.5rem;
		max-height: 340px;
		overflow: auto;
	}

	/*PROPRIETÀ GENERALI ICONE GOOGLE*/
	.material-symbols-outlined{
		user-select: none;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 100%;
		opacity: 80%;
	}
	.material-symbols-outlined:hover{
		background-color: rgba(95,99,104,0.157);
	}

	/*NEWNOTECHIUSA*/
	/*Container icone barra titolo*/
	#iconcontainer{
		display: flex;
		justify-content: center;
		align-items: center;
		width: 20%;
		height: 44px;
	}
	/*Icona immagine*/
	#toolbarclosedimage{
		width: 44px;
		height: 44px;
	}

	/*NEWNOTE APERTA*/
	/*Container barra inferiore newnote*/
	#toolbarcontainer{
		margin-top: 32px;
		margin-left: 15px;
		margin-right: 15px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}

	/*Container strumenti*/
	#tools{
		display: flex;
		flex-direction: row;
	}

	/*Icona immagine*/
	#toolbaropenimage{
		margin-right: 8px;
		margin-right: 8px;
		width: 34px;
		height: 34px;
	}

	/*Icona palette*/
	#toolbaropenpalette{
		margin-right: 8px;
		margin-left: 8px;
		width: 34px;
		height: 34px;
	}

	/*TEXTAREA BODY*/
	/*Container per dare padding alla textarea del body*/
	#newbodycontainer{
		display: flex;
		height: fit-content;
		padding-top: 12px;
		padding-bottom: 12px;
		padding-left: 15px;
		padding-right: 15px;
	}
	/*Proprietà font placeholder body*/
	#newbody::placeholder{
		font-size: 14px;
		color: rgba(0,0,0,0.702);
		font-weight: 500;
		letter-spacing: 0.01785714em;
		line-height: 1.25rem;
	}
	/*Proprietà aggiuntive per body nuovo postit*/
	#newbody{
		height: var(--height);
		max-height: 340px;
		overflow: auto;
		color: #202124;
		font-size: 14px;
		letter-spacing: .01428571em;
		font-weight: 400;
		line-height: 1.25rem;
		padding: 0px;
		border-radius: 0px;
		margin: 0px;
	}
	
	/*BOTTONE CHIUDI NOTA*/
	/*Button chiudi nota*/
	#closenote{
		background-color: white;
		border: transparent;
		height: 36px;
		padding: 8px 24px;
		border-radius: 4px;
		margin-top: 2px;
		margin-bottom: 2px;
		font-size: 14px;
		color: rgba(0,0,0,.87);
		font-weight: 500;
		line-height: 1.25rem;
	}
	#closenote:hover{
		background-color: rgba(95,99,104,0.039);
	}
	#closenote:active{
		background-color: rgba(95,99,104,0.161)!important;
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

	.body{
		font-size: 14px;
		resize: none;
		overflow: clip;
		vertical-align: text-top;
		width: 100%;
		height: 165px;
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
</style>