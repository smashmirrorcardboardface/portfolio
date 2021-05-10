<script>
    export let todos = [];

    import FilterButton from "./FilterButton.svelte";
    import Todo from "./Todo.svelte";

    $: totalTodos = todos.length;
    $: completedTodos = todos.filter((todo) => todo.completed).length;

    function removeTodo(todo) {
        todos = todos.filter((t) => t.id !== todo.id);
    }

    let newToDoName = "";

    function addToDo() {
        //push breaks the function of this because it mutates the array and svelte doesn't know
        //its changed.  So we need to use the spread operator as per below
        todos = [
            ...todos,
            { id: newToDoId, name: newToDoName, completed: false },
        ];
        newToDoName = "";
    }

    let newToDoId;
    $: {
        newToDoId =
            totalTodos === 0 ? 1 : Math.max(...todos.map((t) => t.id)) + 1;
    }

    let filter = "all";
    const filterToDos = (filter, todos) =>
        filter === "active"
            ? todos.filter((t) => !t.completed)
            : filter === "completed"
            ? todos.filter((t) => t.completed)
            : todos;

    function updateTodo(todo) {
        const i = todos.findIndex((t) => t.id === todo.id);
        todos[i] = { ...todos[i], ...todo };
    }
</script>

<!-- Todos.svelte -->
<div class="todoapp stack-large">
    <!-- NewTodo -->
    <form on:submit|preventDefault={addToDo}>
        <h2 class="label-wrapper">
            <label for="todo-0" class="label__lg">
                What needs to be done?
            </label>
        </h2>
        <input
            bind:value={newToDoName}
            type="text"
            id="todo-0"
            autocomplete="off"
            class="input input__lg"
        />
        <button type="submit" disabled="" class="btn btn__primary btn__lg">
            Add
        </button>
    </form>

    <!-- Filter -->
    <FilterButton bind:filter />

    <!-- TodosStatus -->
    <h2 id="list-heading">
        {completedTodos} out of {totalTodos} items completed
    </h2>

    <!-- Todos -->
    <!-- Todos -->
    <ul
        role="list"
        class="todo-list stack-large"
        aria-labelledby="list-heading"
    >
        {#each filterToDos(filter, todos) as todo (todo.id)}
            <li class="todo">
                <Todo {todo}
                on:remove={(e) => removeTodo(e.detail)}
                on:update={(e) => updateTodo(e.detail)}
                />
            </li>
        {:else}
            <li>Nothing to do here!</li>
        {/each}
    </ul>

    <hr />

    <!-- MoreActions -->
    <div class="btn-group">
        <button type="button" class="btn btn__primary">Check all</button>
        <button type="button" class="btn btn__primary">Remove completed</button>
    </div>
</div>
