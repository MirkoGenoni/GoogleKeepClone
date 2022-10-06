<script>
    import Palette from "./BackgroundColor.svelte"
	
    export let isPalette;
    export let setBackground;
    export let submitimage;

	export let mini;

    function setPalette(){
		if(isPalette){
			isPalette = false;
		} else {
			isPalette = true;
		}
	}
</script>

<div id="tools">
    <span id="toolbaropenimagenote" class="material-symbols-outlined" on:click={()=>{submitimage.click()}} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>image</span>
    <span id="toolbaropenpalettenote" class="material-symbols-outlined" on:click={setPalette} style={mini===true ? "font-size: 16px" : "font-size: 24px"}>palette</span>
	{#if mini}
		<span id="toolbaropenpalettenote" class="material-symbols-outlined" style={mini===true ? "font-size: 16px" : "font-size: 24px"}>delete</span>
	{/if}
    {#if isPalette}
        <Palette setBackground={setBackground}/>
    {/if}
</div>

<style>
    #tools{
		display: flex;
		flex-direction: row;
	}

	/*Icona immagine*/
	#toolbaropenimagenote{
		margin-right: 8px;
		margin-right: 8px;
		width: 34px;
		height: 34px;
	}

	/*Icona palette*/
	#toolbaropenpalettenote{
		margin-right: 8px;
		margin-left: 8px;
		width: 34px;
		height: 34px;
	}

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