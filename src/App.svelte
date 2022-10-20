<script>
	import NewNote from "./NewNote.svelte";
	import NoteOpened from "./NoteOpened.svelte";
	import Postit from "./postit.svelte";
	import persistent from "svelte-persistent-store";

	const allPostit = persistent.local.writable("allpostit", []);

	/*function that updates the visualization on added postit*/
	function addNote() {
		$allPostit = $allPostit;
	}

	let currentdragging; /*variable that holds the current position inside the wall div of the element being dragged*/
	let currentdragover =
		null; /*variable that hold current position inside wall div of the element on which there is a dragged item*/
	let dragfix =
		null; /*variable for correct styling of the dragged item on the wall div*/

	let currentopened; /*id of the current opened postit*/
	let isOpen = false; /*true if a postit has been opened*/

	/*function that sets the background of a specific postit*/
	const setBackground = (i, color) => {
		$allPostit[i].colorbkg = color;
	};

	/*function that deletes a specific note from $allPostit*/
	const deleteNote = (i) => {
		if (isOpen) {
			isOpen = false;
		}
		$allPostit.splice(i, 1);
		$allPostit = $allPostit;
	};

	/*function that adds the image to a specific post in $allPostit*/
	function setImage(e, i) {
		let curr = e.target.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(curr);
		reader.onload = (e) => {
			$allPostit[i].image = e.target.result;
		};
		reader.onabort = (e) => {
			$allPostit[i].image = "";
		};

		reader.onerror = (e) => {
			$allPostit[i].image = "";
		};
	}

	/*function called when a postit is dragged*/
	function postitdragstart(e, i) {
		if (currentdragging == null) {
			currentdragging = i;
			/*the postit on the wall becomes invisible leaving only the ghost under the cursor*/
			e.target.style.opacity = "0%";
			dragfix = i;
		}
	}

	/*function called when the postit is dropped anywhere on the wall*/
	function walldragend() {
		document.getElementById("postit" + currentdragging).style.opacity =
			"100%";
		currentdragover = null;
		currentdragging = null;
	}

	/*function called when the postit dragged is on a droppable place*/
	async function dragenter(e, i) {
		/*function that reorganizes allElement putting the element dragged on the position entered*/
		/*sliding all the other elements accordingly*/
		if (i != currentdragover && i != currentdragging) {
			/*console.log("Dragging "+ currentdragging + " on " + i);*/
			currentdragover = i;

			if (currentdragging > i) {
				$allPostit.splice(i, 0, $allPostit[currentdragging]);
				$allPostit.splice(currentdragging + 1, 1);
			} else {
				$allPostit.splice(i + 1, 0, $allPostit[currentdragging]);
				$allPostit.splice(currentdragging, 1);
			}

			currentdragging = i;
			currentdragover = null;
			$allPostit = $allPostit;
		}
	}

	/*function that opens the postit on click*/
	function openNote(event, i) {
		currentopened = i;
		isOpen = true;
	}
</script>

<svelte:head>
	<link
		href="//fonts.googleapis.com/css?family=Google+Sans:400,500&amp;subset=cyrillic,cyrillic-ext,greek,greek-ext,latin,latin-ext,vietnamese"
		rel="stylesheet"
		nonce=""
	/>
	<link
		rel="stylesheet"
		href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0"
	/>
	<link
		rel="stylesheet"
		href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0"
	/>
	<link
		rel="stylesheet"
		href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0"
	/>
</svelte:head>

<div
	id="page"
	style="overflow: {isOpen ? 'hidden' : 'auto'}"
	on:dragend={() => {
		walldragend();
	}}
	on:dragover|preventDefault
>
	{#if isOpen}
		<NoteOpened
			opened={$allPostit[currentopened]}
			on:newcolor={(e) => {
				setBackground(currentopened, e.detail.backgroundcolor);
			}}
			on:deletePostit={() => {
				deleteNote(currentopened);
			}}
			{addNote}
			bind:isOpen
		/>
	{/if}
	<NewNote allElements={$allPostit} {addNote} />

	<div id="notecontainer">
		{#each $allPostit as postit, i (postit.id)}
			<Postit
				{i}
				{postit}
				on:mouseleave={(e) => {
					$allPostit[i].isPalette = false;
				}}
				on:drag={(e) => {
					postitdragstart(e, i);
				}}
				on:dragenter={(e) => {
					dragenter(e, i);
				}}
				on:click={(e) => {
					openNote(e, i);
				}}
				on:deletePostit={() => {
					deleteNote(i);
				}}
				on:newcolor={(e) => {
					setBackground(i, e.detail.backgroundcolor);
				}}
				on:change={(e) => {
					setImage(e, i);
					e.target.value = "";
				}}
			/>
		{/each}
	</div>
</div>

<style>
	#page {
		box-sizing: border-box;
		width: 100vw;
		height: 100vh;
		padding-bottom: 32px;
	}
	/*container for all the the postit*/
	#notecontainer {
		display: grid;
		grid-template-columns: repeat(auto-fill, 242px);
		justify-content: center;
		column-gap: 10px;
		margin: 0px 120px;
	}
</style>
