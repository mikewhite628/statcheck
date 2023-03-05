<script>
  import Nav from "$lib/Nav.svelte";
  import { apiData } from "../store.js";
  import { onMount, afterUpdate, onDestroy } from "svelte";
  import { browser } from "$app/environment";
  import "../app.css";
  import "../circle.css";

  let savestore = false;

  $: if (browser && savestore && $apiData) {
    window.sessionStorage.setItem("store", JSON.stringify($apiData));
  }

  onMount(async () => {
    if (browser) {
      let ses = window.sessionStorage.getItem("store");
      if (ses) {
        console.log("sob-- ~ loading ses", ses);
        $apiData = JSON.parse(ses);
      }
      savestore = true;
    }
  });

  onDestroy(async () => {
    if (browser) {
      window.sessionStorage.removeItem("store");
    }
  });
</script>

<Nav />
<slot />
