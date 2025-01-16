<script>
    import { json } from "@sveltejs/kit";

    import { onMount } from "svelte";

    let pickedTask = $state({})

    let tasklist = $state([]); // becomes an object in onmount, needs to be global so we can access it in the pickRandom function

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

        tasklist = localStorage.getItem("tasklist")

        if (tasklist) {

            tasklist = JSON.parse(tasklist)
            
        } else { 

           

            tasklist = ([(new Task("testTask1","A task for testing purposes", false)), (new Task("Do an art study","Take some time to do an art study", true)), (new Task("aaaaaaaaaaaaaaaaaaaaaaaaaaaa", "aaaaaaaaa aaaaaaaaa aaaaaaaaaaaaaaaaaaaaaa aaaaaaa aaa aaaaaaaaaaaaaaaa", false))])

            localStorage.setItem("tasklist", JSON.stringify(tasklist))
            
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

    function debugClearLocalStorage() {

        localStorage.clear;
    }

    function debugMakeLocalStorage() {

        localStorage.set([(new Task("testTask1","A task for testing purposes", false)), (new Task("Do an art study","Take some time to do an art study", true)), (new Task("aaaaaaaaaaaaaaaaaaaaaaaaaaaa", "aaaaaaaaa aaaaaaaaa aaaaaaaaaaaaaaaaaaaaaa aaaaaaa aaa aaaaaaaaaaaaaaaa", false))])

    }

    function updateTasklist() {

        tasklist.forEach(task => {

            if (task.queuedForRemoval == true) {

                tasklist = tasklist.toSpliced((tasklist.indexOf(task)),1)

                console.log(`removed task ${task.name}`)

            }
            
        });

        updateLocalStorage()

    }

    function updateLocalStorage() {

        localStorage.setItem("tasklist", JSON.stringify(tasklist))
    }
    

</script>

<button onclick={pickRandom}> Hit Me! </button>

<div class="outContainer">

{#if (pickedTask.name !== undefined )} <!-- this way of doing this kinda sucks but i couldnt figure out checking for empty object -->

    <h2> Your Task is: {pickedTask.name}! </h2>
    <p> {pickedTask.description} </p>

    <!-- {console.log("did if")} -->

    <button class="doneButton" onclick={() => queueForRemoval(pickedTask)}> Done! </button>

{/if}



</div>




<style>

</style>