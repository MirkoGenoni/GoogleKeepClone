<script>
    import Toolbar from "./Toolbar.svelte"
    import { tick } from "svelte"
    import { afterUpdate } from "svelte";
    export let opened;
    export let isOpen;
    export let setBackground;
    export let deleteNote;
    export let currentopened;
    export let addNote;

	let isPalette=false;
    let submitimage;
    let title;
    let body;
    let dimensionintBody;
    let dimensionintTitle;

    let styleBody = "--height: 20px;";
    let styleTitle = "--height: 24px;";

    let i;

    function setImage(e){
		let curr = e.target.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(curr);
		reader.onload = e => {
			opened.image = e.target.result;
		}
		reader.onabort = e => {
			opened.image = "";
		}

		reader.onerror = e => {
			opened.image = "";
		}
	}

    function deleteimage(){
		opened.image="";
		submitimage.value="";
	}

    async function dynamicResizeBody(props){
		if(props=="body"){
			styleBody = "--height: 0;";
			await tick();
			dimensionintBody = body.scrollHeight;
			styleBody= "--height: " + dimensionintBody.toString() + "px;";
		} else {
			styleTitle = "--height: 0;";
			await tick();
			dimensionintTitle = title.scrollHeight;
			styleTitle= "--height: " + dimensionintTitle.toString() + "px;";
		}
	}

    function closeNote(){
        isOpen = false;
        addNote();
    }

    function setNotePalette(){

    }

	afterUpdate(async ()=>{
		title.style="--height: " + title.scrollHeight + "px;"
		body.style="--height: " + body.scrollHeight + "px;"
	})

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
</script>


	<div id="background">
		<div id="openednote" style={opened.colorbkg} on:click={(e)=> handleout(e)} on:mouseleave={(e)=> handleout(e)}>
			<div id="titleimagebody">
				{#if opened.image!==""}
					<div id="imagecontainer">
						<img id="uploadedimage" alt ="immagine caricata" src={opened.image}/>
						<div id="deleteimagecontainer">
							<span id="deleteimage" class="material-symbols-outlined" on:click={deleteimage}>delete</span>
						</div>
					</div>
				{/if}
				<div id="titlewithimage">
					<input style="display:none" type="file" accept=".jpg, .jpeg, .png" bind:this={submitimage} on:change={(e)=>{setImage(e);}}/>
					<div id="newtitlecontainer" class="textcontainer" style={"--width: 100%"}>
						<textarea placeholder="Titolo..." id="newtitle" class="title" bind:this={title} bind:value={opened.title} on:input={()=>dynamicResizeBody("title")} style={styleTitle+opened.colorbkg}></textarea>
					</div>
				</div>
				<div id="newbodycontainer" class="textcontainer">
					<textarea placeholder="Scrivi una nota..." id="newbody" class="body" bind:this={body} bind:value={opened.body} on:input={()=>dynamicResizeBody("body")} style={styleBody+opened.colorbkg}></textarea>
				</div>
			</div>
			<div id="toolbarcontainer">
				<Toolbar i={currentopened} bind:isPalette={isPalette} setNotePalette={setNotePalette} {setBackground} {deleteNote} {submitimage} mini={true}/>
				<button id="closenote" on:click={closeNote} style={opened.colorbkg}>Chiudi</button>
			</div>
		</div>
	</div>


<style>
	#background{
		display: flex;
		justify-content: center;
		padding-top: 20vh;
		padding-bottom: 20vh;
		width: 100vw;
		height: 60vh;
		background-color: rgba(32, 33, 36, 0.6);
		z-index: 2;
		position: absolute;
		top:0;
		left:0;
	}
    #openednote{
        position: absolute;
        width:600px;
        height: fit-content;
        background-color: var(--background-color);
		border: solid 1px transparent;
		border-radius: 8px;
		border-color: var(--background-color);
		opacity: 1;
		z-index: 3;
    }
	#titleimagebody{
		width: 100%;
		max-height: calc(60vh - 40px);
		overflow: auto;
		border-top-left-radius: 8px;
		border-top-right-radius: 8px;
	}
	#imagecontainer{
		position: relative;
		height: 100%;
		width: 100%;
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

    #titlewithimage{
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content:space-between;
	}

    #newtitlecontainer{
		width: var(--width);
		display: flex;
		height: fit-content;
		padding-top: 10px;
		padding-bottom: 10px;
		padding-left: 15px;
		padding-right: 15px;
	}

    #newtitle{
		height: var(--height);
		background-color: var(--background-color);
		font-size: 16px;
		color: rgba(0,0,0,0.702);
		letter-spacing: 0.00625em;
		font-weight: 500;
		line-height: 1.5rem;		
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

	.material-symbols-outlined{
		user-select: none;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 100%;
		opacity: 80%;
	}

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

    #toolbarcontainer{
		position: relative;
		margin-left: 15px;
		margin-right: 15px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}

</style>