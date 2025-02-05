<script>

    import Randomizer from "./Randomizer.svelte";

    import AddTask from "./AddTask.svelte";

    import Settings from "./Settings.svelte";

    let { currentComponent = $bindable("randomizer") } = $props();

</script>

<div class="appcontainer">

<h1> <em class="qtext"> "I Want To Do Something, but Don't Know What!," </em> a Task Randomizer App. </h1>

{#if currentComponent == "randomizer"}
    <Randomizer bind:currentComponent = {currentComponent}/>
{:else if currentComponent == "addtask"}
    <AddTask bind:currentComponent = {currentComponent}/>
{:else if currentComponent == "settings"} 
    <Settings bind:currentComponent = {currentComponent}/>
{/if}

</div>

<style lang="scss">
    
    @use "_main.scss";

</style>