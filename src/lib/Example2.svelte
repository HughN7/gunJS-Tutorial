<script lang="ts">
  import Gun from "gun";
  import { onMount } from "svelte";

  const db_id: string = "t4";
  export const db = Gun().get(db_id);

  //DB Key
  const state: string = "state";

  //Local cache storage
  type AppState = {
    val: number | null;
    on: boolean | null;
  };

  let store: AppState;
  $: store = { val: null, on: null };

  //Call it once on load
  onMount(() => {
    db.map().once(function (data: AppState, key: string) {
      console.log(data);
      console.log(key);
      if (data) {
        store = data;
      }
    });
  });

  //Create a listener
  db.map().on(function (data: AppState) {
    if (data) {
      store = data;
    }
  });

  //Crud
  function create() {
    console.log("first run?");
    db.get(state).put({ val: -1, on: false });
  }

  function updateON(value: boolean) {
    db.get(state).get("on").put(value);
  }

  function valPlus() {
    if (store.val != null) {
      db.get(state)
        .get("val")
        .put(store.val + 1);
    }
  }

  function valMinus() {
    if (store.val != null) {
      db.get(state)
        .get("val")
        .put(store.val - 1);
    }
  }
</script>

<div>
  <button on:click={()=> create()}>Create</button>
  <h1>{store.on == (undefined || null) ? "Not loaded" : store.on}</h1>
  <br />
  <h1>{store.val == (undefined || null) ? "Not loaded" : store.val}</h1>
  <button
    on:click={() => {
      updateON(!store.on);
    }}>Change</button
  >
</div>

<div>
  <button
    on:click={() => {
      valPlus();
    }}>+1</button
  >
  <button
    on:click={() => {
      valMinus();
    }}>-1</button
  >
</div>
