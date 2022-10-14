<svelte:head>
	<link href="//fonts.googleapis.com/css?family=Google+Sans:400,500&amp;subset=cyrillic,cyrillic-ext,greek,greek-ext,latin,latin-ext,vietnamese" rel="stylesheet" nonce="">
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
</svelte:head>

<script>
    import NewNote from "./NewNote.svelte"
	import NoteOpened from "./NoteOpened.svelte"
	import { onMount } from 'svelte';
	import { afterUpdate } from 'svelte';
	import {tick} from 'svelte';
	import Toolbar from "./Toolbar.svelte"
	
	let allElements = []; /*array that holds all the postit dictionary that contains all the information of the postit*/

	/*function that updates the visualization on added postit*/
	function addNote(){
		allElements=allElements;
	}

	let postitid = 0; /*univoque id for every post inserted into allElements*/
	let fullpostit = []; /*bind for every postit div created*/

	/*HTML ELEMENT BINDERS FOR DYNAMIC STYLING*/
	let imagescontainer = []; /*bind for every image container div created*/
	let images = []; /*binder for every img element inside the page*/
	let titles = []; /*binder for every title div inside the page*/
	let bodies = []; /*binder for every body div inside the page*/
	let toolbars=[]; /*binder for every toolbar container div inside the page*/
	let submitimage = []; /*binder for every input file necessary to add image to a single postit*/
	let wall; /*binder fot the page that will hold every postit div*/
	let width; /*variable that contains current wall width, depending on window size*/
	let margins; /*variable that contains current wall margins, depending on window size*/
	let boxShadow = []; /*variable that hold every shadow for every postit*/

	let currentdragging; /*variable that holds the current position inside the wall div of the element being dragged*/
	let currentdragover=null; /*variable that hold current position inside wall div of the element on which there is a dragged item*/
	let dragfix = null; /*variable for correct styling of the dragged item on the wall div*/

	let currentopened;/*id of the current opened postit*/
	let isOpen = false; /*true if a postit has been opened*/
	let clicked = false; /*true if new postit is opened*/

	/*function that runs on app creation*/
	onMount(async () => {
		/*sets the dimension for the wall for postit depending on window size*/
		calculateDimension();

		/*gets some placeholder information from a site*/
		const source = "https://jsonplaceholder.typicode.com/photos";
		const response = await fetch(source);
		const datareceivedraw = await response.json();
		const string = JSON.stringify(datareceivedraw);
		const datareceived = JSON.parse(string);
		allElements.unshift({id: datareceived[0].id, title: datareceived[0].title, body: null, colorbkg: "--background-color: #aecbfa;", image: datareceived[0].url, isPalette: false});
		allElements.unshift({id: datareceived[1].id, title: null, body: datareceived[1].title, colorbkg: "--background-color: #ffffff;", image: datareceived[1].url, isPalette: false});
		allElements.unshift({id: datareceived[2].id, title: null, body: null, colorbkg: "--background-color: #f28b82;", image: datareceived[2].url, isPalette: false});
		allElements.unshift({id: datareceived[3].id, title: datareceived[3].title, body: datareceived[3].title, colorbkg: "--background-color: #ccff90;", image: datareceived[3].url, isPalette: false});
		postitid = 5;
		allElements=allElements;
	});

	/*function that calculates correct wall dimension and margins depending on window dimension*/
	function calculateDimension() {
		if(window.innerWidth>1738){
			width = 6*252-10;
			margins = (window.innerWidth - width) / 2;
			wall.style="--width: " + width + "px; --margin-left: " + margins + "px; --margin-right: " + margins + "px;";
		} else if(window.innerWidth>1486){
			width = 5*252-10;
			margins = (window.innerWidth - width) / 2;
			wall.style="--width: " + width + "px; --margin-left: " + margins + "px; --margin-right: " + margins + "px;";
		} else if(window.innerWidth>1234){
			width = 4*252-10;
			margins = (window.innerWidth - width) / 2;
			wall.style="--width: " + width + "px; --margin-left: " + margins + "px; --margin-right: " + margins + "px;";
		} else if(window.innerWidth>982){
			width = 3*252-10;
			margins = (window.innerWidth - width) / 2;
			wall.style="--width: " + width + "px; --margin-left: " + margins + "px; --margin-right: " + margins + "px;";
		} else if(window.innerWidth>730){
			width = 2*252-10;
			margins = (window.innerWidth - width) / 2;
			wall.style="--width: " + width + "px; --margin-left: " + margins + "px; --margin-right: " + margins + "px;";
		} else if(window.innerWidth>478){
			width = 1*252-10;
			margins = (window.innerWidth - width) / 2;
			wall.style="--width: " + width + "px; --margin-left: " + margins + "px; --margin-right: " + margins + "px;";
		}
	}

	/*function that runs every time a component view is updated and corrects the style of every post on every update*/
	afterUpdate(async () => {
		/*console.log(allElements)*/
		allElements.forEach((element, index) => {
			if(titles[index]!==null || images[index]!==null || bodies[index]!== null){
			/*fix to white note border color, the background is set to white but the border must be set to #e0e0e0*/
			let backgroundCol = getComputedStyle(fullpostit[index]).getPropertyValue("background-color");
			let tmppostitstyle="";
			if(backgroundCol!=="rgb(255, 255, 255)"){
				tmppostitstyle="border-color: " + backgroundCol + ";" + "--background-color: " + backgroundCol + ";"
			} else {
				tmppostitstyle="border-color: #e0e0e0;" + "--background-color: " + backgroundCol + ";"
			}
			/*function that adds the shadow when the pointer is over that postit*/
			if(boxShadow[index]!=null && index!=currentdragging){
				tmppostitstyle += boxShadow[index];
			}
			/*remove the shadow on the postit on the wall if the postit is dragged*/
			if(index==dragfix){
				tmppostitstyle += "box-shadow: none";
			}
			fullpostit[index].style = tmppostitstyle;

			/*this assign resets the textarea dimension that will be later updated*/
			titles[index].style = "height: 0px;"
			bodies[index].style = "height: 0px;"
			images[index].style = "height: 0px;"

			/*variable that holds the current image class inside the postit div*/
			/*in the html there will be a div with "noimage" class if there is no image in the postit*/
			let imageclass = new String(images[index].getAttribute("class"));

			/*STYLE FOR POSTIT WITH ONLY IMAGE*/
			if(titles[index].value==="" && bodies[index].value==="" && !imageclass.includes("noimage")){
				imagescontainer[index].style="height:100%;";
				images[index].style = "border-radius: 8px;"

				titles[index].parentElement.style = "padding: 0px; height: 0px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			}

			/*STYLE FOR POSTIT WITH ONLY TITLE AND IMAGE OR TITLE AND BODY AND IMAGE*/
			if(((titles[index].value!=="" && bodies[index].value==="") || ((titles[index].value!=="" && bodies[index].value!==""))) && !imageclass.includes("noimage")){
				imagescontainer[index].style="height:154px;";
				images[index].style = "border-top-left-radius: 8px; border-top-right-radius:8px;";

				titles[index].parentElement.style="height: fit-content;  padding: 12px 16px 12px 16px; max-height: 48px;"
				titles[index].style = "height: " + titles[index].scrollHeight + "px;  max-height: 48px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			}

			/*STYLE FOR POSTIT WITH ONLY BODY AND IMAGE */
			if((titles[index].value==="" && bodies[index].value!=="") && !imageclass.includes("noimage")){
				imagescontainer[index].style="height:154px;";
				images[index].style = "border-top-left-radius: 8px; border-top-right-radius:8px;";

				bodies[index].parentElement.style = "height: fit-content; padding: 12px 16px 12px 16px; max-height: 40px"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px; max-height: 40px;"

				titles[index].parentElement.style = "padding: 0px; height: 0px;"
			}

			/*STYLE FOR POSTIT WITH ONLY TITLE*/
			if(titles[index].value!=="" && bodies[index].value==="" && imageclass.includes("noimage")){
				titles[index].parentElement.style="height: fit-content;  padding: 12px 16px 12px 16px; max-height: 197px;"
				titles[index].style = "height: " + titles[index].scrollHeight + "px;"

				bodies[index].parentElement.style = "padding: 0px; margin: 0px; height: 0px;"
				bodies[index].style = "height: 0px; margin: 0px; padding: 0px;"
			} 

			/*STYLE FOR POSTIT WITH ONLY BODY*/
			if(bodies[index].value!=="" && titles[index].value==="" && imageclass.includes("noimage")) {
				bodies[index].parentElement.style = "height: fit-content; padding: 12px 16px 12px 16px; max-height: 199px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px;"

				titles[index].parentElement.style = "padding: 0px; height: 0px;"

			}
			
			/*STYLE FOR POSTIT WITH ONLY TITLE AND BODY AND THERE IS NO TEXT OVERFLOW*/
			if(titles[index].value!=="" && bodies[index].value!=="" && titles[index].scrollHeight+bodies[index].scrollHeight<193 && imageclass.includes("noimage")){
				titles[index].parentElement.style="height: fit-content;  padding: 12px 16px 5px 16px;"
				titles[index].style = "height: " + titles[index].scrollHeight + "px;"

				bodies[index].parentElement.style = "height: fit-content; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px;"
			}

			/*on the next two cases the length is calculated not on characters but on textarea scrollHeight*/
			/*STYLE FOR POSTIT WITH ONLY TITLE AND BODY, THERE IS OVERFLOW AND THE BODY IS EQUAL OR LONGER OF THE TITLE */
			if(titles[index].value!=="" && bodies[index].value!=="" && titles[index].scrollHeight+bodies[index].scrollHeight>193
			   && titles[index].scrollHeight<=bodies[index].scrollHeight && imageclass.includes("noimage")){
				titles[index].parentElement.style="height: 48px;  padding: 12px 16px 5px 16px;"
				titles[index].style = "height: 48px;"

				bodies[index].parentElement.style = "height: fit-content; max-height: 144px; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: " + bodies[index].scrollHeight + "px; max-height: 144px;"
			}
			
			/*STYLE FOR POSTIT WITH ONLY TITLE AND BODY, THERE IS OVERFLOW AND THE TITLE IS EQUAL OR LONGER OF THE BODY */
			if(titles[index].value!=="" && bodies[index].value!=="" && titles[index].scrollHeight+bodies[index].scrollHeight>193
			   && titles[index].scrollHeight>bodies[index].scrollHeight && imageclass.includes("noimage")){
				titles[index].parentElement.style="height: fit-content; max-height: 138px; padding: 12px 16px 5px 16px;"
				titles[index].style = "height: " + titles[index].scrollHeight + "px; max-height: 138px;"

				bodies[index].parentElement.style = "height: 54px; padding: 5px 16px 12px 16px;"
				bodies[index].style = "height: 60px;"
			}

			/*this cases render the current postit on the wall completely transparent and removes the toolbar*/
			if(currentdragging!=null){
				fullpostit[currentdragging].style.opacity = "0%";
				toolbars[dragfix].style.opacity="0%";
			} else if(dragfix!=null) {
				toolbars[dragfix].style.opacity="";
				dragfix=null;
			}

			/*this case render the current opened postit on the wall completely transparent*/
			if(isOpen==true){
				fullpostit[currentopened].style.opacity = "0%";
			}
		}
		});
	});

	/*FROM HERE THE VARIABLE I CORRESPONDS TO THE POSITION IN allElements*/
	/* i is assigned when the list of postit is rendered by scanning in order the array AllElements*/

	/*function that sets the background of a specific postit*/
	const setBackground = (i, color) => {
		allElements[i].colorbkg= "--background-color: "+ color +";";
	}

	/*function that deletes a specific note from allElements*/
	const deleteNote = (i) => {
		if(isOpen){ isOpen = false }
		allElements.splice(i, 1);
		allElements=allElements;
	}

	/*function that activates and deactivates the background color selection*/
	const setNotePalette = (i) => {
		allElements[i].isPalette===true ? allElements[i].isPalette=false : allElements[i].isPalette=true;
	}

	/*function that close the background selection when there is an outside click or the cursor exit the postit*/
	function handleout(event, i){
		let nodes;
		let array = [];
		if(allElements[i].isPalette===true){
			nodes = document.getElementById("optionscontainer").childNodes;
			nodes.forEach((value)=>{array.push(value.id)})
			array.push("nocoloricon");
		}
		if(event.target.id!=="toolbaropenpalettenote" && event.target.id!=="optionscontainer" && !array.includes(event.target.id) && allElements[i].isPalette===true){
			allElements[i].isPalette=false;
		} 
		/*this case remove the shadow from the postit when the cursoe leaves the postit*/
		if(event.type!="mousedown") {
			boxShadow[i]=null;
			/*change of a useless property that triggers a new render*/
			fullpostit[i].style.opacity=1;
		}
	}

	/*function that adds the image to a specific post in allElements*/
	function setImage(e, i){
		let curr = e.target.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(curr);
		reader.onload = e => {
			allElements[i].image = e.target.result;
		}
		reader.onabort = e => {
			allElements[i].image = "";
		}

		reader.onerror = e => {
			allElements[i].image = "";
		}

	}

	/*function called when a postit is dragged*/
	function postitdragstart(e, i){
		if(currentdragging==null){
			currentdragging = i;
			/*the postit on the wall becomes invisible leaving only the ghost under the cursor*/
			e.target.style.opacity="0%";
			dragfix = i;
		}
	}

	/*function called when the postit is dropped anywhere on the wall*/
	function walldragend(){
		fullpostit[currentdragging].style.opacity="100%";
		currentdragover = null;
		currentdragging = null;
	}

	/*function called when the postit dragged is on a droppable place*/
	async function dragenter(e, i){
		for(let j=0; j++; j<5){
			await tick();
		}
		/*function that reorganizes allElement putting the element dragged on the position entered*/
		/*sliding all the other elements accordingly*/
		if(i!=currentdragover && i!=currentdragging){
			/*console.log("Dragging "+ currentdragging + " on " + i);*/
			currentdragover=i;

			if(currentdragging>i){
				allElements.splice(i, 0, allElements[currentdragging]);
				allElements.splice(currentdragging+1, 1);
			} else {
				allElements.splice(i+1, 0, allElements[currentdragging]);
				allElements.splice(currentdragging, 1);
			}

			currentdragging = i;
			currentdragover = null;
			allElements = allElements;
		}
	}

	/*placeholder function that disables the return of the ghost image of the dragged element*/
	function dragover(e){}

	/*function that opens the postit on click*/
	function openNote(event, i){		
		let nodes;
		let array = [];

		/*array will contain all toolbar element*/
		let id = "tools";
		nodes=document.getElementById(id).childNodes
		nodes.forEach((value)=>{array.push(value.id)})
		array.push("tools");
		array.push("submitimage"+i);

		if(allElements[i].isPalette===true){
			nodes = document.getElementById("optionscontainer").childNodes;
			nodes.forEach((value)=>{array.push(value.id)})
			array.push("nocoloricon");
		}
		/*this condition ensures that the click is not on any element of the toolbar*/
		if(event.target.id!=="toolbaropenpalettenote" && event.target.id!=="optionscontainer" && !array.includes(event.target.id) && clicked!=true){
			/*console.log("note opened");*/
			currentopened = i;
			isOpen = true;
		}
	}

	/*function called on mouse enter on any postit, setting the shadow*/
	function boxshadow(e, i){
		/*console.log("focusing")*/
		boxShadow[i] = "box-shadow: 0 1px 2px 0 rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);";
		/*useless style set that trigger render*/
		fullpostit[i].style.opacity=1;
	}

</script>

<svelte:window on:resize={calculateDimension}></svelte:window>
<svelte:body  on:dragend={()=>{walldragend()}} on:dragover|preventDefault={(e)=>{dragover(e)}}></svelte:body>
<main>
	{#if isOpen}
		<NoteOpened opened={allElements[currentopened]} bind:isOpen={isOpen} {setBackground} {deleteNote} {currentopened} {addNote}/>
	{/if}
	<NewNote bind:postitid={postitid} {allElements} {addNote}/>
	<div id="notecontainer" bind:this={wall}>	
		{#each allElements as postit, i}
			<div draggable="true" class="postit" id={"postit"+i} bind:this={fullpostit[i]} style={postit.colorbkg} on:mouseenter={(e)=>{boxshadow(e,i)}} on:mouseleave={(e)=> {handleout(e, i)}} on:mousedown={(e)=> {handleout(e, i)}} on:drag={(e)=>{postitdragstart(e, i);}} on:dragenter={(e)=>{dragenter(e, i)}} on:dragover|preventDefault={(e)=>{dragover(e)}} on:click={(e)=>{openNote(e,i)}}>
				<input draggable="false" id={"submitimage" + i} style="display:none" type="file" accept=".jpg, .jpeg, .png" bind:this={submitimage[i]} on:change={(e)=>{setImage(e, i);}}/>
				{#if postit.image!=""}
					<div draggable="false" id={"image" + i} class="noteimagecontainer" bind:this={imagescontainer[i]}>
						<img draggable="false" class="noteimage" bind:this={images[i]} src={postit.image} alt="note"/>
					</div>
				{:else}
					<div draggable="false" class="noimage" bind:this={images[i]}></div>
				{/if}
				<div draggable="false" id={"titlecontainer" + i} class="textcontainer">
					<textarea disabled class="titlepostit" bind:this={titles[i]} bind:value={postit.title} style={postit.colorbkg}></textarea>
				</div>
				<div draggable="false" id={"bodycontainer" + i} class="textcontainer">
					<textarea disabled draggable="false" class="bodypostit" bind:this={bodies[i]} bind:value={postit.body} style={postit.colorbkg}></textarea>
				</div>
				<div draggable="false" class="toolbarcontainer" id={"toolbarcontainer" + i} bind:this={toolbars[i]} style={postit.colorbkg}>
					<Toolbar {i} isPalette={postit.isPalette} {deleteNote} {setBackground} submitimage={submitimage[i]} {setNotePalette} mini={true}/>
				</div>
			</div>
		{/each}
	</div>
</main>

<style>
	/*sets the font for all the elements*/
	:global(body){
		height: fit-content;
		margin: 0px;
		padding: 0px;
		font-family: "Google Sans", Roboto, Arial, sans-serif;
	}

	/*style of the element for the postit with no image*/
	.noimage{
		height: 0px;
	}

	/*container for all the the postit*/
	#notecontainer{
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: flex-start;
		width: var(--width);
		margin-left: var(--margin-right);
		margin-right: var(--margin-left);
		min-width: 478px;
		gap:10px;
		height: fit-content;
	}

	/*container for all the elements of the postit*/
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
	
	/*sets the visibility for the toolbar*/
	.postit:hover > .toolbarcontainer{
		opacity: 100%;
	}

	/*container for the title and body textarea*/
	.textcontainer{
		width: 208px;
		height: fit-content;
		padding-top: 12px;
		padding-left: 16px;
		padding-right: 16px;
		padding-bottom: 0px;
	}

	/*container for the postit image*/
	.noteimagecontainer{
		width: 240px;
		height: 154px;
		display: flex;
		justify-content: center;
		border-top: none;
		border-top-left-radius: 8px;
		border-top-right-radius: 8px;
	}

	/*postit image*/
	.noteimage{
		width: 100%;
		object-fit:cover;
		object-position: 0%;
		border-top-left-radius: 8px;
		border-top-right-radius: 8px;
	}

	/*title postit textarea*/
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
		pointer-events: none;
	}

	/*properties for body container*/
	#bodycontainer{
		padding-top: 0px;
	}

	/*body postit textarea*/
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
		pointer-events: none;
	}

	.bodypostit:focus{
		overflow: auto;
	}

	/*container for toolbar*/
	.toolbarcontainer{
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