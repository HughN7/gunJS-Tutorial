<script lang="ts">
  import Gun from "gun/gun";

  const gun_id: string = "todos";
  const gun = Gun().get(gun_id);

  //Local storage
  type Todo = {
    title: string;
    done: boolean;
  };

  let store: Record<string, Todo> = {};

  //make a listener that goes through all keys in to `gun_id` node in the db
  //THE .map() IS NOT JS/TS's .map() -> it's from GunJS
  gun.map().on((todo: Todo, key: string) => {
    if (todo) {
      //update the local store with the new value
      store[key] = todo;
    } else {
      //Remove from local store if inside of gun, it's null b/c it's been removed
      delete store[key];
      store = store;
    }
  });

  //Listen for updates in local store
  $: todos = Object.entries(store);
  $: done = todos.filter(([key, todo]) => {
    todo.done;
  }).length;

  //Write data to gun
  let input: string = "";
  function create() {
    if (input == "") return;
    gun.get(input as any).put({ title: input, done: false }); //if you use gun.set here with TS, it gives linter error
    input = "";
  }

  function update(key: string, value: boolean) {
    gun.get(key).get("done").put(value);
  }

  function remove(key: string) {
    gun.get(key).put(null);
  }
</script>

<input placeholder="add todo" bind:value={input} />
<button
  on:click={() => {
    create();
  }}>Add</button
>
completed {done}/{todos.length}

{#each todos as [key, todo]}
  <div id={key}>
    <input
      type="checkbox"
      checked={todo.done}
      on:change={() => update(key, !todo.done)}
    />
    {todo.title} <a href="/" on:click={() => remove(key)}>remove</a>
    {todo.done ? "😺" : "😾"}
  </div>
{/each}
