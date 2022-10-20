<script>
    import Palette from "./BackgroundColor.svelte"
    import {createEventDispatcher} from "svelte";
	
    export let isPalette; /*variable true if the change background color menu is opened*/
    export let setBackground; /*parent function that changes the postit background color*/
	export let mini; /*true if the toolbar is in a postit and not in new postit, adds the delete icon*/
	
	/*function that opens and closes the change background color menu is opened*/
    function setPalette(){
		if(isPalette){
			isPalette = false;
		} else {
			isPalette = true;
		}

	}

	const dispatch = createEventDispatcher();


	function deletenote(){
			dispatch('deletePostit');
	}
	
</script>

<div id="tools" on:click|stopPropagation>
	<label>
		<input
        draggable="false"
        style="display:none"
        type="file"
        accept=".jpg, .jpeg, .png"
        on:change
    />
		<span id="toolbaropenimagenote" class="material-symbols-outlined" style={mini===true ? "font-size: 16px" : "font-size: 24px"}>image</span>
	</label>
    <span id="toolbaropenpalettenote" class="material-symbols-outlined" on:click|stopPropagation={setPalette} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>palette</span>
	{#if mini}
		<span id="deletenote" class="material-symbols-outlined" on:click|stopPropagation={deletenote} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>delete</span>
	{/if}
    {#if isPalette}
        <Palette setBackground={setBackground} {mini} on:newcolor/>
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
		cursor: default;
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