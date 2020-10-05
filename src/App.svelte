<script>
  import { onMount } from "svelte";
  import { text_area_resize } from "./auto_resize_height.js";

  let dataAdded = false;
  let input = "";

  let extraFiltered = [];
  let cleaned = [];
  onMount(async () => {
    // This is cool, but not sure I want to use it, keep it simple
    document.addEventListener("mouseup", event => {
      if (window.getSelection().toString().length) {
        const selected = window.getSelection().toString();
        extraFiltered.push({
          word: selected,
          interest: 1
        });
        extraFiltered = extraFiltered;
      }
    });
  });

  function nextStep() {
    dataAdded = true;
  }

  function reset() {
    dataAdded = false;
    input = "";
    words = words.map(e => {
      e.interest = 0;
      return e;
    });
    extraFiltered = [];
    cleaned = [];
  }

  function toggle(e, index) {
    e.interest = e.interest == 1 ? 0 : 1;
    words[index] = e;
  }

  function remove(e) {
    e.interest = 0;
    words[e.originalIndex] = e;
  }

  $: words = input.split(" ").map((e, index) => {
    return { word: e, interest: 0, originalIndex: index, cleaned: "" };
  });

  $: filtered = words.filter(e => e.interest == 1);

  $: cleaned = filtered.map(e => {
    var punctuationless = e.word.replace(/[.,\/#!$%\^&\*;:{}=\-_`~()]/g, "");
    var finalString = punctuationless.replace(/\s{2,}/g, " ");
    e.cleaned = finalString;
    return e;
  });
</script>

<style>
  @import "./all.css";
</style>

<div class="flex flex-column">
  {#if !dataAdded}
    <div class="outline w-50 pa3 mr2">
      <h1>Add Text</h1>
      <textarea
        class="db border-box hover-black w-100 ba b--black-20 pa2 br2 mb2"
        aria-describedby="comment-desc"
        bind:value={input}
        use:text_area_resize />
      <button class="br3" on:click={nextStep}>Next</button>
    </div>
  {/if}

  {#if dataAdded}
    <div class="outline w-50 pa3 mr2">

      <button class="br3" on:click={reset}>Reset</button>

      <h1>Select Text</h1>
      <p>
        {#each words as w, i}
          <span class="pa0" on:click={() => toggle(w, i)}>
            {#if w.interest == 0}
              {w.word}
            {:else}
              <mark>{w.word}</mark>
            {/if}
          </span>
        {/each}
      </p>
    </div>

    {#if cleaned.length > 0}
      <div class="outline w-50 pa3 mr2">
        <h1>View Words</h1>
        <ul>
          {#each cleaned as w, i}
            <li on:click={() => remove(w)}>
              <span>{w.cleaned}</span>
            </li>
          {/each}
        </ul>
      </div>
    {/if}
  {/if}
</div>
