<script>
	import Toolbar from "./Toolbar.svelte";
	import { tick } from "svelte";
	import { afterUpdate } from "svelte";

	export let opened; /*allElement's element with all its properties*/
	export let isOpen; /*true if the postit is open and opens the relative view*/
	export let currentopened; /*Index of current element of $allPostit opened*/
	export let addNote; /*parent method necessary to update the page render*/

	let submitimage; /*element bind necessary to upload image*/
	let title; /*element bind necessary to dinamically resize the textarea*/
	let body; /*element bind necessary to dinamically resize the textarea*/
	let dimensionintBody; /*variable that temporally stores the current size of the div, hidden part also*/
	let dimensionintTitle; /*variable that temporally stores the current size of the div, hidden part also*/

	let styleBody =
		"--height: 20px;"; /*variable passed to the element that contains its style*/
	let styleTitle =
		"--height: 24px;"; /*variable passed to the element that contains its style*/


	/*IMAGE HANDLING*/
	/*function that stores and display the photo added to the postit directly inside $allPostit*/
	function setImage(e) {
		let curr = e.target.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(curr);
		reader.onload = (e) => {
			opened.image = e.target.result;
		};
		reader.onabort = (e) => {
			opened.image = "";
		};

		reader.onerror = (e) => {
			opened.image = "";
		};
	}

	/*function that deletes the image inserted inside the postit*/
	function deleteimage() {
		opened.image = "";
		submitimage.value = "";
	}

	/*TETXTAREA HANDLING*/
	/*Function that assign the right height of the textarea on input*/
	async function dynamicResizeBody(props) {
		if (props == "body") {
			styleBody = "--height: 0;";
			await tick();
			dimensionintBody = body.scrollHeight;
			styleBody = "--height: " + dimensionintBody.toString() + "px;";
		} else {
			styleTitle = "--height: 0;";
			await tick();
			dimensionintTitle = title.scrollHeight;
			styleTitle = "--height: " + dimensionintTitle.toString() + "px;";
		}
	}

	/*function that run just after the render and it assign the right height to the textarea*/
	afterUpdate(async () => {
		title.style = "--height: " + title.scrollHeight + "px;";
		body.style = "--height: " + body.scrollHeight + "px;";
	});

	/*NOTE CLOSURE HANDLING*/
	/*function called by the button close that close the note visualization*/
	function closeNote() {
		isOpen = false;
		addNote();
	}
</script>

<div
	draggable="false"
	id="background"
	on:click|self={closeNote}
>
	<div
		draggable="false"
		id="openednote"
		style="background-color: {opened.colorbkg}; border-color: {opened.colorbkg ===
		'#ffffff'
			? '#e0e0e0'
			: opened.colorbkg}"
	>
		<div draggable="false" id="titleimagebody">
			{#if opened.image !== ""}
				<div draggable="false" id="openedimagecontainer">
					<img
						draggable="false"
						id="openeduploadedimage"
						alt="immagine caricata"
						src={opened.image}
					/>
					<div draggable="false" id="openeddeleteimagecontainer">
						<span
							draggable="false"
							id="openeddeleteimage"
							class="material-symbols-outlined"
							on:click={deleteimage}>delete</span
						>
					</div>
				</div>
			{/if}
			<div draggable="false" id="titlewithimageform">
				<input
					draggable="false"
					style="display:none"
					type="file"
					accept=".jpg, .jpeg, .png"
					bind:this={submitimage}
					on:change={(e) => {
						setImage(e);
					}}
				/>
				<div
					draggable="false"
					id="openedtitlecontainer"
					class="textcontainer"
					style={"--width: 100%"}
				>
					<textarea
						draggable="false"
						placeholder="Titolo"
						id="openedtitle"
						class="title"
						bind:this={title}
						bind:value={opened.title}
						on:input={() => dynamicResizeBody("title")}
						style={styleTitle}
					/>
				</div>
			</div>
			<div
				draggable="false"
				id="openedbodycontainer"
				class="textcontainer"
			>
				<textarea
					draggable="false"
					placeholder="Nota"
					id="openedbody"
					class="body"
					bind:this={body}
					bind:value={opened.body}
					on:input={() => dynamicResizeBody("body")}
					style={styleBody}
				/>
			</div>
		</div>
		<div draggable="false" id="toolbarcontainer">
			<Toolbar
				i={currentopened}
				on:newcolor
				on:deletePostit
				on:submitimage={() => submitimage.click()}
				mini={true}
			/>
			<button
				draggable="false"
				id="closenote"
				on:click={closeNote}
				style={opened.colorbkg}>Chiudi</button
			>
		</div>
	</div>
</div>

<style>
	/*background of the opened note that covers all the window*/
	#background {
		display: flex;
		justify-content: center;
		box-sizing: border-box;
		padding-top: 20vh;
		padding-bottom: 20vh;
		width: 100vw;
		height: 100vh;
		background-color: rgba(32, 33, 36, 0.6);
		z-index: 2;
		position: absolute;
		top: 0;
		left: 0;
	}
	/*container of all the element and containers inside the post, image, body, title*/
	#openednote {
		position: absolute;
		width: 600px;
		height: fit-content;
		border: solid 1px transparent;
		border-radius: 8px;
		opacity: 1;
		z-index: 3;
	}

	/*container for image, title and body*/
	/*limits the post dimension and make element scrollable if oversize (except for toolbar)*/
	#titleimagebody {
		width: 100%;
		max-height: calc(60vh - 40px);
		overflow: auto;
		border-top-left-radius: 8px;
		border-top-right-radius: 8px;
	}

	/*IMAGE CONTAINER*/
	#openedimagecontainer {
		position: relative;
		height: 100%;
		width: 100%;
	}

	/*makes half visible the delete button on image hover*/
	#openedimagecontainer:hover > #openeddeleteimagecontainer {
		opacity: 40%;
	}

	/*makes full visible the delete button on image hover*/
	#openedimagecontainer:hover > #openeddeleteimagecontainer:hover {
		opacity: 100%;
	}

	/*New image inside note*/
	#openeduploadedimage {
		width: 100%;
		height: 100%;
	}

	/*Container for delete image symbol*/
	#openeddeleteimagecontainer {
		display: flex;
		justify-content: center;
		align-items: center;
		position: absolute;
		background-color: black;
		width: 34px;
		height: 34px;
		right: 15px;
		bottom: 15px;
		z-index: 2;
		opacity: 0%;
	}

	/*bin symbol for image deletion*/
	#openeddeleteimage {
		color: white;
	}

	/*Container for invisible input file and image visualization*/
	#titlewithimageform {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
	}

	/*TITLE STYLE*/
	/*title container*/
	#openedtitlecontainer {
		width: var(--width);
		display: flex;
		height: fit-content;
		padding-top: 16px;
		padding-bottom: 10px;
		padding-left: 15px;
		padding-right: 15px;
	}
	/*title textarea*/
	#openedtitle {
		height: var(--height);
		font-size: 22px;
		color: rgba(0, 0, 0, 0.702);
		letter-spacing: 0;
		font-weight: 400;
		line-height: 1.75rem;
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

	/*BODY STYLE*/
	/*body container*/
	#openedbodycontainer {
		display: flex;
		height: fit-content;
		padding-top: 12px;
		padding-bottom: 12px;
		padding-left: 16px;
		padding-right: 16px;
	}
	/*placeholder font properties for body textarea*/
	#openedbody::placeholder {
		font-size: 14px;
		color: rgba(0, 0, 0, 0.702);
		font-weight: 500;
		letter-spacing: 0.01785714em;
		line-height: 1.25rem;
	}
	/*body textarea*/
	#openedbody {
		height: var(--height);
		max-height: var(--max-height);
		overflow: auto;
		color: #202124;
		font-size: 16px;
		letter-spacing: 0.00625em;
		font-weight: 400;
		line-height: 1.5rem;
		padding: 0px;
		border-radius: 0px;
		margin: 0px;

		resize: none;
		width: 100%;
		border: none;
		outline: none;
		white-space: pre-wrap;
	}

	/*TOOLBAR HANDLING*/
	/*container for all the tool icon and closure button*/
	#toolbarcontainer {
		position: relative;
		margin-left: 15px;
		margin-right: 15px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	/*close button style*/
	#closenote {
		background-color: transparent;
		border: transparent;
		height: 36px;
		padding: 8px 24px;
		border-radius: 4px;
		margin-top: 2px;
		margin-bottom: 2px;
		font-size: 14px;
		color: rgba(0, 0, 0, 0.87);
		font-weight: 500;
		line-height: 1.25rem;
	}
	#closenote:hover {
		background-color: rgba(95, 99, 104, 0.039);
	}
	#closenote:active {
		background-color: rgba(95, 99, 104, 0.161) !important;
	}

	/*style for imported google symbols*/
	.material-symbols-outlined {
		user-select: none;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 100%;
		opacity: 80%;
	}
</style>
