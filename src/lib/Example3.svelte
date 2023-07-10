<script>
// @ts-nocheck

    import { messages } from "./stores"
</script>

<input placeholder="write message" on:change={e => messages.add(e.target.value) && (e.target.value = "")} />


{#each Object.entries($messages) as [key, {sender, text, icon}] (key)}
<div style="padding: .4rem">
    {icon} {sender}: {text}
    <a href="#" on:click|preventDefault={() => {messages.delete(key)}}>delete</a>
</div>
{/each}