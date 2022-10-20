<script>
    import Toolbar from "./Toolbar.svelte";
    export let i;
    export let postit;
    export let isOpen;
    
    let title;
    let nlines = 1;

    const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

    const prova =
        async () => {
        if(!isOpen){
            if (title && postit.title) {
                await sleep(10);
                nlines = Math.floor(8 - title.offsetHeight / 24);
            } else if(!postit.image) {
                nlines = 8;
            } else if (postit.image){
                nlines = 2;
            }
        }
    }
    
    $: prova(title, postit.title, nlines)
</script>

<div
    draggable="true"
    class="postit"
    id={"postit" + i}
    style="background-color: {postit.colorbkg}; border-color: {postit.colorbkg ===
    '#ffffff'
        ? '#e0e0e0'
        : postit.colorbkg}"
    on:click
    on:drag
    on:dragenter
    on:dragover|preventDefault
    on:mouseleave
>
    {#if postit.image}
        <div
            draggable="false"
            id={"image" + i}
            class="noteimagecontainer"
            class:onlyimage={!postit.title && !postit.body}
        >
            <img
                draggable="false"
                class="noteimage"
                src={postit.image}
                alt="note"
            />
        </div>
    {:else}
        <div draggable="false" class="noimage" />
    {/if}
    {#if postit.title}
        <div
            class="titlepostit"
            class:clamp2={postit.image}
            class:clamp8={!postit.image && !postit.body}
            class:clamp7={!postit.image && postit.body}
            bind:this={title}
        >
            {postit.title}
        </div>
    {/if}
    {#if postit.body && !(postit.image && postit.title)}
        <div
            draggable="false"
            class="bodypostit"
            style="-webkit-line-clamp: {nlines};"
        >
            {postit.body}
        </div>
    {/if}
    <div
        draggable="false"
        class="toolbarcontainer"
        id={"toolbarcontainer" + i}
        style="background-color: {postit.colorbkg};"
    >
        <Toolbar
            isPalette={postit.isPalette}
            mini={true}
            on:deletePostit
            on:newcolor
            on:change
        />
    </div>
</div>

<style>
    /*container for all the elements of the postit*/
    .postit {
        position: relative;
        display: flex;
        flex-direction: column;
        position: relative;
        margin-bottom: 5px;
        width: 240px;
        height: 260px;
        border: solid 1px transparent;
        border-radius: 8px;
        border-color: #e0e0e0;
    }

    .postit:hover {
        box-shadow: 0 1px 2px 0 rgba(60, 64, 67, 0.3),
            0 1px 3px 1px rgba(60, 64, 67, 0.15);
    }

    /*sets the visibility for the toolbar*/
    .postit:hover > .toolbarcontainer {
        opacity: 100%;
    }

    /*container for the postit image*/
    .noteimagecontainer {
        width: 240px;
        height: 154px;
        display: flex;
        justify-content: center;
        border-top: none;
        border-top-left-radius: 8px;
        border-top-right-radius: 8px;
    }

    /*postit image*/
    .noteimage {
        width: 100%;
        object-fit: cover;
        border-top-left-radius: 8px;
        border-top-right-radius: 8px;
    }

    /*title postit textarea*/
    .titlepostit {
        color: #202124;
        font-size: 16px;
        letter-spacing: 0.00625em;
        line-height: 1.5rem;
        font-weight: 500;
        margin: 11px 16px;
        max-height: 197px;
        white-space: pre-wrap;
        word-break: break-word;
    }

    /*properties for body container*/
    #bodycontainer {
        padding-top: 0px;
    }

    /*body postit textarea*/
    .bodypostit {
        color: #202124;
        font-size: 14px;
        letter-spacing: 0.01428571em;
        line-height: 1.25rem;
        font-weight: 400;
        margin: 11px 16px;
        max-height: 199px;
        white-space: pre-wrap;
        display: -webkit-box;
        -webkit-box-orient: vertical;
        overflow: hidden;
        word-break: break-word;
    }

    .clamp7 {
        display: -webkit-box;
        -webkit-line-clamp: 7;
        -webkit-box-orient: vertical;
        overflow: hidden;
        margin-bottom: 0px;
    }
    .clamp8 {
        display: -webkit-box;
        -webkit-line-clamp: 8;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }

    .clamp2 {
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }

    .bodypostit:focus {
        overflow: auto;
    }

    /*container for toolbar*/
    .toolbarcontainer {
        position: absolute;
        margin-bottom: 5px;
        bottom: 0px;
        width: 210px;
        margin-left: 15px;
        margin-right: 15px;
        display: flex;
        flex-direction: row;
        justify-content: center;
        opacity: 0%;
        border-radius: 8px;
    }

    .onlyimage {
        overflow: hidden;
        height: 100%;
        border-radius: 8px;
    }
</style>
