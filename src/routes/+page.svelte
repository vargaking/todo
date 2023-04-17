<script lang="ts" context="module">
    interface Task {
        id: number;
        name: string;
        highlight?: boolean;
    }
</script>

<script lang="ts">
    import { PUBLIC_CONFIGCAT_SDK_KEY } from "$env/static/public";
    import * as configcat from "configcat-js";
    import { onMount } from "svelte";

    let tasks: Task[] = [];
    let taskName: string;

    function addTask() {
        tasks = [
            ...tasks,
            {
                id: tasks.length + 1,
                name: taskName,
            },
        ];
        taskName = '';
    }

    function deleteTask(id: number) {
        tasks = tasks.filter((task) => task.id !== id);
    }

    function highlightTask(id: number) {
        tasks = tasks.map((task) => {
            if (task.id === id) {
                return {
                    ...task,
                    highlight: true,
                };
            }
            return task;
        });
    }
    
    let highlightTaskFlag: boolean;

    onMount(async () => {
        // create a ConfigCat client instance
        const client = configcat.createClient(PUBLIC_CONFIGCAT_SDK_KEY);

        // get the value of highlightTask feature flag
        highlightTaskFlag = await client.getValueAsync<boolean>("highlightTask", false);

        console.log("highlightTaskFlag", highlightTaskFlag);
    });
</script>

<div class="bg-blue-500 w-screen h-screen p-4">
    <h1 class="text-center text-white text-4xl font-bold">ToDo App</h1>

    <!-- create a thin column with white background, centered, rounded corners, and a shadow -->
    <div class="w-1/3 mx-auto bg-white rounded-lg shadow-lg p-4">
        <h2 class="text-center text-2xl font-bold">Add a new task</h2>

        <!-- create a form with a text input and a submit button -->
        <form class="flex flex-col">
            <input type="text" class="border border-gray-300 p-2 rounded-lg" placeholder="Enter a task" bind:value={taskName} />
            <button type="submit" class="bg-blue-500 text-white p-2 rounded-lg mt-2" on:click={addTask}>Add</button>
        </form>

        <!-- create a list of tasks -->
        <ul class="mt-4">
            {#each tasks as task}
                <li class="flex justify-between items-center p-2 border-b {task.highlight ? "bg-red-300": ""} border-gray-300">
                    <span>{task.name}</span>
                    {#if highlightTaskFlag}
                        <button class="bg-green-500 text-white p-2 rounded-lg" on:click={() => highlightTask(task.id)}>Highlight</button>
                    {/if}
                    <button class="bg-red-500 text-white p-2 rounded-lg" on:click={() => deleteTask(task.id)}>Delete</button>
                </li>
            {/each}
        </ul>        
    </div>
</div>