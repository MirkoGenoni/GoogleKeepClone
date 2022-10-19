<script>
    import { createEventDispatcher } from "svelte";

    export let mini = false;

    /*variable that stores all the color in the change background color menu*/
    const colors = [
        { id: "1", name: "red", code: "#f28b82" },
        { id: "2", name: "arancione", code: "#fbbc04" },
        { id: "3", name: "giallo", code: "#fff475" },
        { id: "4", name: "verde", code: "#ccff90" },
        { id: "5", name: "verdeAcqu", code: "#a7ffeb" },
        { id: "6", name: "blu", code: "#cbf0f8" },
        { id: "7", name: "bluScuro", code: "#aecbfa" },
        { id: "8", name: "viola", code: "#d7aefb" },
        { id: "9", name: "rosa", code: "#fdcfe8" },
        { id: "10", name: "marrone", code: "#e6c9a8" },
        { id: "11", name: "grigio", code: "#e8eaed" },
    ];

    const dispatch = createEventDispatcher();

    function setbackground(color) {
        dispatch("newcolor", {
            backgroundcolor: color,
        });
    }
</script>

<svelte:head>
    <link
        rel="stylesheet"
        href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200"
    />
</svelte:head>

<div id="optionscontainer" on:click|stopPropagation style={mini ? "left: -130px" : "left:40px"}>
    <div id="nocolorcontainer" on:click|stopPropagation={() => setbackground("#ffffff")}>
        <span id="nocoloricon" class="material-symbols-outlined">
            format_color_reset</span
        >
    </div>
    {#each colors as color}
        <div
            id={color.name}
            class="color"
            style="background-color: {color.code}"
            on:click|stopPropagation={() => setbackground(color.code)}
        />
    {/each}
</div>

<style>
    /*container for all the change background color palette*/
    #optionscontainer {
        position: absolute;
        top: 33px;
        left: 40px;
        background-color: white;
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-around;
        height: 30px;
        width: 454px;
        padding: 8px 9px 9px 3px;
        border: 1px solid transparent;
        border-radius: 10px;
        box-shadow: 0 1px 2px 0 rgb(60 64 67 / 30%),
            0 2px 6px 2px rgb(60 64 67 / 15%);
        z-index: 2;
    }

    /*google icon for nocolor*/
    #nocolorcontainer {
        display: flex;
        justify-content: center;
        width: 24px;
        height: 24px;
        opacity: 80%;
        align-items: center;
        border: 2px solid #e0e0e0;
        margin: 4px;
        border-radius: 100%;
    }

    #nocolorcontainer:hover {
        border: 2px solid black;
    }

    /*alle the color in palette*/
    .color {
        width: 28px;
        height: 28px;
        margin: 4px;
        border-radius: 100%;
    }

    .color:hover {
        border: 2px solid #a142f4;
    }

    /*google icon style*/
    .material-symbols-outlined {
        cursor: default;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 18px;
    }
</style>
