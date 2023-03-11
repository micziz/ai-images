<script lang="ts">
    let input;
    let submitted = false;
    let promsie;
    import { HfInference } from '@huggingface/inference'


    const hf = new HfInference(import.meta.env.VITE_HUGKEY)

    function convertToBase64(buffer){
        const uniChar = new Uint8Array(buffer);
        const strChar = String.fromCharCode.apply(null, uniChar);
        let base64String = btoa(strChar);
        return base64String;
    }
    async function createImage(prompt) {
        if (prompt === undefined){
            return null
        }
        const res = await hf.textToImage({
            inputs: prompt,
            negative_prompt: 'blurry',
            model: 'stabilityai/stable-diffusion-2',
        })
        const blob = new Blob([res])
        console.log(blob)
        const url = URL.createObjectURL(blob)
        return url
    }
    
    promsie = createImage(undefined)
</script>

<main>
    <h1 class="title is-1">Generate ai images, for FREE!</h1>
    {#if submitted}
        {#await promsie}
            <h1 class="title is-1">Loading</h1>
        {:then data} 
            <div id="form-div-2">
                <img src={data} width="500" height="500" alt="Generated from ai"/>
            </div>
        {/await}
    {/if}
    <form on:submit|preventDefault={() => {
        submitted = false
        promsie = createImage(input)
        submitted = true
    }}>
        <div id="form-div">
            <textarea id="form" bind:value={input}></textarea>
        </div>
        <div id="form-div-2">
            <input type="submit" class="button is-info is-outlined" value="Submit">
        </div>
    </form>
</main>

<style>
    @import "https://cdn.jsdelivr.net/npm/bulma@0.9.4/css/bulma.min.css";
    h1 {
        text-align: center;
        margin-top: 2rem;
    }

    #form-div{
        display: flex;
        justify-content: center;
        align-items: center;
    }
    
    #form-div-2{
        display: flex;
        justify-content: center;
        align-items: center;
        margin-top: 2rem;
    }

    #form{
        width: 40rem;
        height: 10rem;
    }
</style>