<script lang="ts">
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
    export let loading = false;
    export let orderTotal = 0;

    function copyText(e) {
        dispatch("onCopyText", e);
    }
    function reset(e) {
        dispatch("onReset", e);
    }
</script>

<div
    class="md:relative fixed bottom-0 left-0 right-0 flex md:gap-4 md:justify-center"
>
    <button
        class="btn md:btn-wide w-[50%] btn-error md:rounded-2xl rounded-none text-white"
        on:click={reset}>Reset 💣</button
    >
    <button
        class="btn md:btn-wide w-[50%] btn-primary md:rounded-2xl rounded-none text-white"
        on:click={copyText}
    >
        {#if loading}
            <span>Loading...</span>
        {:else}
            Copy Order ({orderTotal}) <span class="animate-bounce ml-1">😋</span>
        {/if}
    </button>
</div>
