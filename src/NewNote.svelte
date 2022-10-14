<script>
    import { tick } from "svelte";
	import Toolbar from "./Toolbar.svelte"

	const i = 0;
    export let postitid;
    let bodyAdd = "";
    let titleAdd= "";

    export let allElements;
    export let addNote;

	let clicked=false;
	let dimensionintBody;
	let dimensionintTitle;
	let styleBody = "--height: 20px;";
	let styleTitle = "--height: 24px;";
	let newpostTitle;
	let newpostBody;

	let isPalette = false;
	let currentBackground ="--background-color: #ffffff;";

	let image = "";
	let submitimage;
	let isImage = false;

	function setImage(e){
		isImage=true;
		let curr = e.target.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(curr);
		reader.onload = e => {
			image = e.target.result;
		}
		reader.onabort = e => {
			image = "";
		}

		reader.onerror = e => {
			image = "";
		}

		clicked= true;
	}

    function closeNote(){
		clicked=false;
		newpostTitle.value="";
		newpostBody.valute="";
		titleAdd="";
		bodyAdd="";
		styleBody = "--height: 20px;";
		styleTitle = "--height: 24px;";
		currentBackground ="--background-color: #ffffff;";
		isPalette=false;
		isImage=false;
		image=""; 
		submitimage.value="";
	}

	const setBackground = (i, color) => {
		currentBackground = "--background-color: "+ color +";";
	}

	function setClick(){
		clicked= true;
	}

	function handleout(event){
		let nodes;
		let array = [];
		if(isPalette===true){
			nodes = document.getElementById("optionscontainer").childNodes;
			nodes.forEach((value)=>{array.push(value.id)})
			array.push("nocoloricon");
		}
		if(event.target.id!=="toolbaropenpalettenote" && event.target.id!=="optionscontainer" && !array.includes(event.target.id) && isPalette===true){
			isPalette=false;
		}
	}

	async function dynamicResizeBody(props){
		if(props=="body"){
			styleBody = "--height: 0;";
			await tick();
			dimensionintBody = newpostBody.scrollHeight;
			styleBody= "--height: " + dimensionintBody.toString() + "px;";
		} else {
			styleTitle = "--height: 0;";
			await tick();
			dimensionintTitle = newpostTitle.scrollHeight;
			styleTitle= "--height: " + dimensionintTitle.toString() + "px;";
		}
	}

    function deleteimage(){
		isImage=false;
		image="";
		submitimage.value="";
	}

    function propagateContent(){
        if(bodyAdd!="" || titleAdd!="" || image!=""){
            allElements.unshift({id: postitid, title: titleAdd, body: bodyAdd, colorbkg: currentBackground, image: image, isPalette: false});
            postitid++;
        }
        closeNote();
        addNote();
    }

	function outsideClick(node){
		const handleClick = (event) => {
			if (!node.contains(event.target) && clicked===true && event.target.id!="toolbaropenpalette" && event.target.closest(".postit")==null && event.target.closest("#background")==null) {
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

	function deleteNote(){}
	function setNotePalette(){}
</script>

    <div id="newNote">
		<div id="start" class="newpostit" use:outsideClick on:outclick={propagateContent} on:click={(e)=> handleout(e)} on:mouseleave={(e)=> handleout(e)} style={currentBackground}>
			<div id="titlebodyimage">
			{#if isImage}
				<div id="imagecontainer">
					<img id="uploadedimage" alt ="immagine caricata" src={image}/>
					<div id="deleteimagecontainer">
						<span id="deleteimage" class="material-symbols-outlined" on:click={deleteimage}>delete</span>
					</div>
				</div>
			{/if}
			<div id="titlewithimage">
				<input style="display:none" type="file" accept=".jpg, .jpeg, .png" bind:this={submitimage} on:change={(e)=>{setImage(e);}}/>
				<div id="newtitlecontainer" class="textcontainer" style={"--width: " + (clicked===false ? "80%" : "100%")} on:click={setClick}>
					<textarea placeholder={clicked===false ? "Scrivi una nota..." : "Titolo..."} id="newtitle" class="title" bind:this={newpostTitle} bind:value={titleAdd} on:input={()=>dynamicResizeBody("title")} style={styleTitle+currentBackground + (isImage === true ? "--max-height: none" : "--max-height: 340px")}></textarea>
				</div>
				{#if !clicked}
				<div id="iconcontainer" on:click={()=>{submitimage.click()}}>
					<span id="toolbarclosedimage" class="material-symbols-outlined">image</span>
				</div>
				{/if}
			</div>
			{#if clicked}
				<div id="newbodycontainer" class="textcontainer">
					<textarea placeholder="Scrivi una nota..." id="newbody" class="body" bind:this={newpostBody} bind:value={bodyAdd} on:input={()=>dynamicResizeBody("body")} style={styleBody+currentBackground + (isImage === true ? "--max-height: none" : "--max-height: 340px")}></textarea>
				</div>
			{/if}
			</div>
			{#if clicked}
				<div id="toolbarcontainer">
					<Toolbar bind:isPalette={isPalette} setNotePalette={setNotePalette} {setBackground} {deleteNote} {submitimage} mini={false} {i}/>
					<button id="closenote" on:click={closeNote} style={currentBackground}>Chiudi</button>
				</div>
			{/if}
		</div>
	</div>

<style>
    /*FORM NUOVA NOTA*/
	/*Container div per centrare form newpostit*/
	#newNote{
		display: flex;
		justify-content: center;
		width: 100%;
		height: fit-content;
		max-height: 800px;
		padding-top: 32px;
		margin-bottom: 16px;
	}

	/**/
	#titlebodyimage{
		height: fit-content;
		max-height: 724px;
		overflow: auto;
        border-top-right-radius: 8px;
        border-top-left-radius: 8px;
    }

	#imagecontainer{
		position: relative;
		height: fit-content;
		width: fit-content;
	}

	#imagecontainer:hover > #deleteimagecontainer{
		opacity:40%;
	}
	#imagecontainer:hover > #deleteimagecontainer:hover{
		opacity:100%;
	}
	/*Nuova immagine nella nota*/
	#uploadedimage{
		width: 100%;
		height: 100%;
	}

	#deleteimage{
		color:white;
	}
	#deleteimagecontainer{
		display: flex;
		justify-content: center;
		align-items: center;
		position: absolute;
		background-color: black;
		width:34px;
		height: 34px;
		right: 15px;
		bottom: 15px;
		z-index: 2;
		opacity: 0%;
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
		border-color: var(--background-color);
		background-color: var(--background-color);
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
		width: var(--width);
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
		background-color: var(--background-color);
		font-size: 16px;
		color: rgba(0,0,0,0.702);
		letter-spacing: 0.00625em;
		font-weight: 500;
		line-height: 1.5rem;
		max-height: var(--max-height);
		overflow: auto;
        resize: none;
        border: none;
        width: 100%;
		padding: 0px;
		outline: 0px;
		margin: 0px;
		border: none;
		white-space: pre-wrap;
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
		position: relative;
		margin-left: 15px;
		margin-right: 15px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
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
		background-color: var(--background-color);
		max-height: var(--max-height);
		overflow: auto;
		color: #202124;
		font-size: 14px;
		letter-spacing: .01428571em;
		font-weight: 400;
		line-height: 1.25rem;
		padding: 0px;
		border-radius: 0px;
		margin: 0px;
       
        resize: none;
		width: 100%;
		border: none;
		outline: none;
		white-space: pre-wrap;
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
		background-color: var(--background-color);
	}
	#closenote:hover{
		background-color: rgba(95,99,104,0.039);
	}
	#closenote:active{
		background-color: rgba(95,99,104,0.161)!important;
	}
</style>