<script lang="ts">
  // import svelteLogo from './assets/svelte.svg'
  import GithubLogo from './assets/github.svg?component'
  import { dbcleanfood } from "./assets/db";
  import Result from "./lib/Result.svelte";
  import CopyBtn from "./lib/CopyBtn.svelte";
  import { onMount } from "svelte";


  interface ICleanfoods {
    code: string;
    name: string;
    check: boolean;
  }

  // init value
  export let cleanfoods:ICleanfoods[] = dbcleanfood.map((m) => {
    return {
      ...m,
      check: false,
    };
  });

  // compute item list that check === true only
  let cleanfoosChecked: ICleanfoods[]
  $: cleanfoosChecked = cleanfoods.filter((f) => f.check);

  // compute total
  let total: number
  $: total = cleanfoosChecked.length;

  // compute code that check === true only
  let cleanfoosCheckedCode: string[]
  $: cleanfoosCheckedCode = cleanfoods
    .filter((f) => f.check)
    .map((m) => m.code);

  // compute final script that check === true only
  let result: string
  $: result = cleanfoosChecked.map((m) => m.name).join("\r\n");


  function codeColor(code) {
    if (code.includes('A')) {
      return 'text-green-300'
    } else if(code.includes('B')) {
      return 'text-blue-300'
    } else if(code.includes('C')) {
      return 'text-yellow-300'
    } else {
      return 'text-white'
    }
  }

  let loading: boolean = false;
  async function copyText() {
    if(total === 0) return
    loading = true;

    if (window.navigator.clipboard) {
      await window.navigator.clipboard.writeText(result).then(
        () => {
          /* Resolved - text copied to clipboard */
          alert("copy success");
        },
        () => {
          /* Rejected - clipboard failed */
          alert("copy failed");
        }
      );
      loading = false;
    }
    loading = false;
  }

  function onReset() {
    if (confirm(`Are you sure to clear all order ?`)) {
      cleanfoods = cleanfoods.map((cf) => {
          return {
            ...cf,
            check: false
          }
        });
      const urlParams = new URLSearchParams(window.location.search);
       urlParams.delete("order");
       window.history.replaceState(null, "", '/');
    }
  }

  function addQueryToURL() {
    const urlParams = new URLSearchParams(window.location.search);
    urlParams.set("order", cleanfoosCheckedCode.join(","));
    window.history.pushState(null, "", "?" + urlParams.toString());
  }

  onMount(() => {
    const urlParams = new URLSearchParams(window.location.search);
    const orderUrl = urlParams.get("order");
    if (orderUrl) {
      const orders = orderUrl.split(",");
      cleanfoods = cleanfoods.map((cf) => {
        return {
          ...cf,
          check: orders.includes(cf.code)
        }
      });
    }
  });
</script>

<main>
  <div>
    <a href="https://github.com/mjkodkks/clean-food-order-list" class="w-7 h-7 float-right" target="_blank" rel="noreferrer">
      <div class="text-white w-7 h-7 cursor-pointer">
        <GithubLogo />
      </div>
    </a>
    <img src="/android-chrome-192x192.png" height="48" width="48" class="mx-auto" alt="cat">
    <div class="text-2xl mt-4">Clean Food Order List 📔 </div>
    <!-- {@debug cleanfoosChecked} -->
    <section class="mt-4">
      <CopyBtn on:onCopyText={copyText} on:onReset={onReset} orderTotal={total} />
    </section>
    <section>
      <Result {result} />
    </section>
    <section class="pb-[80px]">
      {#each cleanfoods as cleanfood, i}
        <div class="form-control">
          <label class="label cursor-pointer">
            <div class="flex gap-4">
              <span class="text-lg {codeColor(cleanfood.code)}" 
                >{cleanfood.code}</span>
              <span class="label-text text-lg text-left">{cleanfood.name}</span>
            </div>
            <input
              type="checkbox"
              bind:checked={cleanfood.check}
              on:change={addQueryToURL}
              class="checkbox checkbox-primary"
            />
          </label>
        </div>
      {/each}
    </section>
  </div>
</main>

<style>
</style>
