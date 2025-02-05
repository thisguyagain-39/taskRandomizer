<script>
    import { json } from "@sveltejs/kit";

   /*  import { transformWithEsbuild } from "vite"; */ // wait i didnt. put this here? vite??? hello?????? who put this here 



    let { currentComponent = $bindable()} = $props()

    class Task {

        constructor(name, description, isRepeatable){

            this.name = name;
            this.description = description;
            this.isRepeatable = isRepeatable;
            this.queuedForRemoval = false;
        }
    }

    let addName = $state('');
    let addDesc = $state('');
    let addIsRepeatable = $state(false)

    function submitTask() {

        let thisTask = new Task(addName,addDesc,addIsRepeatable)

        let tasklist = JSON.parse(localStorage.getItem("tasklist"));

/*         thisTask = JSON.stringify(thisTask) */
    
        console.log(tasklist)


        if (tasklist == null) {

            tasklist = JSON.stringify([thisTask, (new Task("demotask","demodesc",false))]) // brackets hell

            localStorage.setItem("tasklist",tasklist)

            console.log(tasklist)

            return;
            
        }

        // console.log(tasklist)

        tasklist.push(thisTask)

        // console.log(tasklist)

        tasklist = JSON.stringify(tasklist)

        // console.log(tasklist)

        localStorage.setItem("tasklist",tasklist)

        addName = ''
        addDesc = ''
        addIsRepeatable = false


    }

</script>

<div class="buttonBarContainer">

    <button onmouseup={() => currentComponent = "addtask"}> Add New Tasks </button>

    <button onmouseup={() => currentComponent = "settings"}> Settings </button>

    <button onmouseup ={() => currentComponent = "randomizer"}> Return to Randomizer </button>

</div>

<form class="addForm">

    <label for="taskName"> Task Name </label>
    <input type="text" name="taskName" bind:value={addName}/>  

    <label for="taskDesc"> Task Description </label>
    <input type="text" name="taskDesc" bind:value={addDesc}/>  

    <abbr title="This setting determines if your task will be cleared after being completed. If checked, your task will remain in rotation after completion."><label for="isRepeatable"> Is This Task Repeatable? </label>
    <input type="checkbox" name="isRepeatable" bind:checked={addIsRepeatable}>  </abbr>

    <input type="submit" onmouseup={submitTask}>


</form>


<style lang="scss"> 

    @use "_main.scss";

</style>