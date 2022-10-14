<script>
    import Palette from "./BackgroundColor.svelte"
	
    export let isPalette; /*variable true if the change background color menu is opened*/
    export let setBackground; /*parent function that changes the postit background color*/
    export let submitimage; /*parent input file element*/
	export let deleteNote; /*parent function that deletes the postit*/
	export let mini; /*true if the toolbar is in a postit and not in new postit, adds the delete icon*/
	export let i; /*0 if the parent is a single postit, position in allElements otherwise*/
	export let setNotePalette; /*parent function that opens and closes the change background color menu*/
	
	/*function that opens and closes the change background color menu is opened*/
    function setPalette(){
		if(isPalette){
			isPalette = false;
		} else {
			isPalette = true;
		}

		if(mini){
			setNotePalette(i);
		}
	}
</script>

<div id="tools">
    <span id="toolbaropenimagenote" class="material-symbols-outlined" on:click={()=>{submitimage.click()}} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>image</span>
    <span id="toolbaropenpalettenote" class="material-symbols-outlined" on:click={setPalette} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>palette</span>
	{#if mini}
		<span id="deletenote" class="material-symbols-outlined" on:click={()=>{deleteNote(i);}} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>delete</span>
	{/if}
    {#if isPalette}
        <Palette setBackground={setBackground} {mini} {i}/>
    {/if}
</div>

<style>
	/*icon container*/
    #tools{
		display: flex;
		flex-direction: row;
	}

	/*image icon*/
	#toolbaropenimagenote{
		margin-left: 8px;
		margin-right: 8px;
		width: 34px;
		height: 34px;
	}

	/*palette icon*/
	#toolbaropenpalettenote{
		margin-right: 8px;
		margin-left: 8px;
		width: 34px;
		height: 34px;
	}

	/*bin icon*/
	#deletenote{
		margin-right: 8px;
		margin-left: 8px;
		width: 34px;
		height: 34px;
	}

	/*google icon style*/
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
</style>