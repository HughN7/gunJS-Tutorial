<script lang="ts">
  import Gun from "gun";
  import { onMount } from "svelte";

  const db_id: string = "t1";
  const db = Gun().get(db_id);

  //DB Key
  const state: string = "state"

  //Local cache storage
  type AppState = {
    val: number | null;
    on: boolean | null;
  };

  let store: AppState; 
  $: store = {val: null, on: null}

  //Call it once on load
  onMount(()=>{
    db.map().once(function(data: AppState){
      if(data){
        store = data
      }
    })
  })

  //Create a listener
  db.map().on(function(data: AppState){
    if(data){
      store = data
    }
  })

  //Crud functions
  function create(){
    db.get(state).put({val: -1, on: false})
  }

  function updateON(value: boolean) {
    db.get(state).get("on").put(value);
  }


</script>



<div>
  <button on:click={()=>{create()}}>Create</button>
  <h1>{store.on == (undefined || null) ? "Not loaded" : store.on}</h1>
  <br>
  <h1>{store.val == (undefined || null) ? "Not loaded" : store.val}</h1>
  <button on:click={()=>{updateON(!store.on)}}>Change</button>
</div>