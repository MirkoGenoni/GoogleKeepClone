<script>
	import { tick } from "svelte";
	import Toolbar from "./Toolbar.svelte";

	let bodyAdd =
		""; /*bind to textarea value for newbody than inserted into body in postit dictionary inside allElements*/
	let titleAdd =
		""; /*bind to textarea value for newtitle than inserted into body in postit dictionary inside allElements*/

	export let allElements = []; /*array of all postit*/
	export let addNote; /*parent method to update the view on postit insertion*/

	let clicked = false; /*variable true on click on form for new postit insertion*/
	let dimensionintBody; /*variable that temporally stores the current size of the div, hidden part also*/
	let dimensionintTitle; /*variable that temporally stores the current size of the div, hidden part also*/
	let styleBody =
		"--height: 20px;"; /*variable passed to the element that contains its style*/
	let styleTitle =
		"--height: 24px;"; /*variable passed to the element that contains its style*/
	let newpostTitle; /*bind to textarea element necessary to modify style and value when opened or closed*/
	let newpostBody; /*bind to textarea element necessary to modify style and value when opened or closed*/

	let isPalette = false; /*variable true if opened the menu for changing postit background*/
	let currentBackground =
		"#ffffff"; /*current background color then added to postit dictionary inside allElements*/

	let image = ""; /*current image inserted into post*/
	let isImage = false; /*true if there is a image inside current new postit*/

	/*HANDLE IMMAGINE ADDITION*/
	/*function that gets current image inserted into input file*/
	function setImage(e) {
		isImage = true;
		/*gets only first file inserted*/
		let curr = e.target.files[0];
		let reader = new FileReader();
		reader.readAsDataURL(curr);
		reader.onload = (e) => {
			image = e.target.result;
		};
		reader.onabort = (e) => {
			image = "";
		};

		reader.onerror = (e) => {
			image = "";
		};

		clicked = true;
	}

	/*function that deletes image if click on button*/
	function deleteimage() {
		isImage = false;
		image = "";
	}

	/*POSTIT CLOSURE HANDLE*/
	/*function that opens the full newpostit with title and body textarea*/
	function setClick() {
		clicked = true;
	}

	/*function that delets new postit content if there's a click on close button*/
	function closeNote() {
		clicked = false;
		newpostTitle.value = "";
		newpostBody.valute = "";
		titleAdd = "";
		bodyAdd = "";
		styleBody = "--height: 20px;";
		styleTitle = "--height: 24px;";
		currentBackground = "#ffffff";
		isPalette = false;
		isImage = false;
		image = "";
	}

	/*function that inserts the new post in allElements when click outside new post forum and not on a postit*/
	function outsideClick(node) {
		const handleClick = (event) => {
			/*this condition controls that the click is not on toolbar or its child elements or on any postit*/
			if (
				clicked === true &&
				!event.target.closest("#toolbarcontainer") &&
				!event.target.closest(".newpostit") &&
				!event.target.closest(".postit") &&
				!event.target.closest("#background")
			) {
				node.dispatchEvent(new CustomEvent("outclick"));
			}
		};

		document.addEventListener("click", handleClick, true);

		return {
			destroy() {
				document.removeEventListener("click", handleClick, true);
			},
		};
	}

	/*function that actively insert postit and information inside allElements*/
	function propagateContent() {
		if (bodyAdd != "" || titleAdd != "" || image != "") {
			allElements.unshift({
				id: makeid(8),
				title: titleAdd,
				body: bodyAdd,
				colorbkg: currentBackground,
				image: image,
				isPalette: false,
			});
		}
		closeNote();
		addNote();
	}

	function makeid(length) {
		var result = "";
		var characters =
			"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
		var charactersLength = characters.length;
		for (var i = 0; i < length; i++) {
			result += characters.charAt(
				Math.floor(Math.random() * charactersLength)
			);
		}
		return result;
	}

	/*function that saves current selected background color*/
	const setBackground = (color) => {
		currentBackground = color;
	};

	/*Function that assign the right height of the textarea on input*/
	async function dynamicResizeBody(props) {
		if (props == "body") {
			styleBody = "--height: 0;";
			await tick();
			dimensionintBody = newpostBody.scrollHeight;
			styleBody = "--height: " + dimensionintBody.toString() + "px;";
		} else {
			styleTitle = "--height: 0;";
			await tick();
			dimensionintTitle = newpostTitle.scrollHeight;
			styleTitle = "--height: " + dimensionintTitle.toString() + "px;";
		}
	}
</script>

<div id="newNote">
	<div
		id="start"
		class="newpostit"
		use:outsideClick
		on:outclick={propagateContent}
		on:mouseleave={() => {
			isPalette = false;
		}}
		style="background-color: {currentBackground}"
	>
		<div id="titlebodyimage">
			{#if isImage}
				<div id="imagecontainer">
					<img
						id="uploadedimage"
						alt="immagine caricata"
						src={image}
					/>
					<div id="deleteimagecontainer">
						<span
							id="deleteimage"
							class="material-symbols-outlined"
							on:click={deleteimage}>delete</span
						>
					</div>
				</div>
			{/if}
			<div id="titlewithimage">
				<div
					id="newtitlecontainer"
					class="textcontainer"
					style={"--width: " + (clicked === false ? "80%" : "100%")}
					on:click={setClick}
				>
					<textarea
						placeholder={clicked === false
							? "Scrivi una nota..."
							: "Titolo..."}
						id="newtitle"
						class="title"
						bind:this={newpostTitle}
						bind:value={titleAdd}
						on:input={() => dynamicResizeBody("title")}
						on:click={()=>{isPalette=false}}
						style={styleTitle +
							(isImage === true
								? "--max-height: none"
								: "--max-height: 340px")}
					/>
				</div>
				{#if !clicked}
					<label>
						<input
							draggable="false"
							style="display:none"
							type="file"
							accept=".jpg, .jpeg, .png"
							on:change={(e) => {
								setImage(e);
								e.target.value = "";
							}}
						/>

						<div id="iconcontainer">
							<span
								id="toolbarclosedimage"
								class="material-symbols-outlined">image</span
							>
						</div>
					</label>
				{/if}
			</div>
			{#if clicked}
				<div id="newbodycontainer" class="textcontainer">
					<textarea
						placeholder="Scrivi una nota..."
						id="newbody"
						class="body"
						bind:this={newpostBody}
						bind:value={bodyAdd}
						on:input={() => dynamicResizeBody("body")}
						on:click={()=>{isPalette=false}}
						style={styleBody +
							(isImage === true
								? "--max-height: none"
								: "--max-height: 340px")}
					/>
				</div>
			{/if}
		</div>
		{#if clicked}
			<div id="toolbarcontainer">
				<Toolbar
					bind:isPalette
					on:newcolor={(e) => {
						setBackground(e.detail.backgroundcolor);
					}}
					on:change={(e) => {
						setImage(e);
						e.target.value = "";
					}}
					mini={false}
				/>
				<button
					id="closenote"
					on:click={closeNote}
					style={currentBackground}>Chiudi</button
				>
			</div>
		{/if}
	</div>
</div>

<style>
	/*NEW POSTIT INSERTION STYLE*/
	/*div container that contains all the postit information image, title and body*/
	#newNote {
		display: flex;
		justify-content: center;
		width: 100%;
		height: fit-content;
		max-height: 800px;
		padding-top: 32px;
		margin-bottom: 16px;
	}

	/*container of title, body and image that ensure scrolling on overflow*/
	#titlebodyimage {
		height: fit-content;
		max-height: 724px;
		overflow: auto;
		border-top-right-radius: 8px;
		border-top-left-radius: 8px;
	}

	/*container for added image*/
	#imagecontainer {
		position: relative;
		width: 100%;
	}

	/*style for image deletion icon*/
	#imagecontainer:hover > #deleteimagecontainer {
		opacity: 40%;
	}
	#imagecontainer:hover > #deleteimagecontainer:hover {
		opacity: 100%;
	}

	/*new image inserted*/
	#uploadedimage {
		width: 100%;
		height: 100%;
	}

	/*container for bin icon*/
	#deleteimagecontainer {
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

	/*bin icon for image deletion*/
	#deleteimage {
		color: white;
	}

	/*container for all new post elements, toolbar included*/
	.newpostit {
		box-sizing: border-box;
		width: 598px;
		white-space: 0px;
		height: fit-content;
		box-shadow: 0 1px 2px 0 rgb(60 64 67 / 30%),
			0 2px 6px 2px rgb(60 64 67 / 15%);
		border: solid 1px transparent;
		border-radius: 8px;
	}

	/*TITLE TEXTAREA*/
	/*Title textarea with icon addition icon*/
	#titlewithimage {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
	}

	/*Container title textarea*/
	#newtitlecontainer {
		width: var(--width);
		display: flex;
		height: auto;
		padding-top: 10px;
		padding-bottom: 10px;
		padding-left: 15px;
		padding-right: 15px;
	}

	/*new title textarea*/
	#newtitle {
		height: var(--height);
		font-size: 16px;
		color: rgba(0, 0, 0, 0.702);
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
		background-color: transparent;
	}

	/*PROPERTIES FOR IMPORTED GOOGLE ICONS*/
	.material-symbols-outlined {
		user-select: none;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 100%;
		opacity: 80%;
	}
	.material-symbols-outlined:hover {
		background-color: rgba(95, 99, 104, 0.157);
	}

	/*NEWPOSTIT CLOSED*/
	/*container for title textarea and image addition icon*/
	#iconcontainer {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 20%;
		height: 44px;
	}
	/*image addition icon*/
	#toolbarclosedimage {
		width: 44px;
		height: 44px;
	}

	/*NEWPOSTIT OPENED*/
	/*container for toolbar*/
	#toolbarcontainer {
		position: relative;
		margin-left: 15px;
		margin-right: 15px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}

	/*TEXTAREA BODY*/
	/*container for body textarea*/
	#newbodycontainer {
		display: flex;
		height: fit-content;
		padding-top: 12px;
		padding-bottom: 12px;
		padding-left: 15px;
		padding-right: 15px;
	}
	/*poperty for body placeholder text*/
	#newbody::placeholder {
		font-size: 14px;
		color: rgba(0, 0, 0, 0.702);
		font-weight: 500;
		letter-spacing: 0.01785714em;
		line-height: 1.25rem;
	}
	/*property for body textarea*/
	#newbody {
		height: var(--height);
		max-height: var(--max-height);
		overflow: auto;
		color: #202124;
		font-size: 14px;
		letter-spacing: 0.01428571em;
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
		background-color: transparent;
	}

	/*CLOSE POSTIT BUTTON*/
	/*close postit button style*/
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
</style>
