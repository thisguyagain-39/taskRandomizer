<script>

    // todo: add settings page, add taskadd page

    import { json } from "@sveltejs/kit";

    import { onMount } from "svelte";

    let pickedTask = $state({})

    let tasklist = $state([]); // becomes an object in onmount, needs to be global so we can access it in the pickRandom function

    let { currentComponent = $bindable()} = $props()

    class Task {

        constructor(name, description, isRepeatable){

            this.name = name;
            this.description = description;
            this.isRepeatable = isRepeatable;
            this.queuedForRemoval = false;
        }
    }

    onMount(() => {

		/* console.log('mounted Randomizer'); */

        // on mount, check if local storage is present
        // if yes, load tasklist from storage
        // if no, load default tasklist

        tasklist = (localStorage.getItem("tasklist"))

        if (tasklist == false || tasklist == [] || tasklist === null)  {

            tasklist = ([(new Task("Nothing!", "The task list is empty. Mark this as done, then go add some tasks under 'Add New Tasks.'", false))])

            localStorage.setItem("tasklist", JSON.stringify(tasklist))
            
        } else { 

            tasklist = JSON.parse(tasklist)
            
        }

        $inspect(tasklist)

        $inspect(pickedTask)

	});

    function pickRandom(){

        updateTasklist() // update the task list before doing stuff

        if (tasklist !== undefined) {

            let oldTask

            if (pickedTask !== undefined) {

                // if pickedtask is not undefined, this function has been run before, and we can use the value of pickedtask before it's updated to prevent dupes from happening

                oldTask = pickedTask;
            }

            let thisRandomNumber = ((Math.floor(Math.random() * tasklist.length)));

            /* console.log(thisRandomNumber) */

            pickedTask = tasklist[thisRandomNumber]
        }

    }

    function queueForRemoval(thisItem) {

        if (thisItem.isRepeatable == true) {

            console.log(`task ${thisItem.name} is repeatable, skipping queueForRemoval`)

            return; // if task is repeatable, Just Don't clear the task

        } else {

            tasklist.forEach(task => {

                if (thisItem.name == task.name && thisItem.description == task.description)  // if tasks are the same...
                
                {

                    task.queuedForRemoval = true; // ...queue the task for removal
                    
                }
                
            });

        }

        console.log(`task ${thisItem.name} queued for removal`)

    }

    function updateTasklist() {
        

        tasklist.forEach(task => {

            if (task.queuedForRemoval == true) {

                tasklist = tasklist.toSpliced((tasklist.indexOf(task)),1)

                console.log(`removed task ${task.name}`)

            }
            
        });

        if (tasklist == false || tasklist == [] || tasklist === null)  {

            tasklist = ([(new Task("Nothing!", "The task list is empty. Mark this as done, then go add some tasks under 'Add New Tasks.'", false))])

            localStorage.setItem("tasklist", JSON.stringify(tasklist))

        } 


        updateLocalStorage()

    }

    function updateLocalStorage() {

        localStorage.setItem("tasklist", JSON.stringify(tasklist))
    }


    

</script>

<div class="buttonBarContainer">

    <button onmouseup={() => currentComponent = "addtask"}> Add New Tasks </button>

    <button onmouseup={() => currentComponent = "settings"}> Settings </button>

    <button onmouseup={pickRandom}> Hit Me! </button>

</div>

<div class="outContainer">

    {#if (pickedTask.name !== undefined )} <!-- this way of doing this kinda sucks but i couldnt figure out checking for empty objects -->

        <h2> Your Task is: {pickedTask.name}! </h2>
        <p> {pickedTask.description} </p>

        <!-- {console.log("did if")} -->

        <div class="buttonBarDiv"> 
    
            <button class="doneButton" onclick={() => queueForRemoval(pickedTask)}> Done! </button>

        </div>

    {/if}

</div>




<style lang="scss">

    @use "_main.scss";

</style>