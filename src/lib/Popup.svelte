<script>
    export let newItem = '';
    export let categories = [];
    export let addToList = () => {
    };
</script>

<style>
    .todo-form {
        display: flex; /* Arrange elements horizontally */
        align-items: center; /* Align vertically */
    }

    input[type="text"] {
        flex: 1; /* Take up remaining space */
        padding: 0.5rem;
    }

    select {
        padding: 0.5rem;
        margin-left: 0.5rem;
    }

    button {
        padding: 0.5rem 1rem;
        margin-left: 0.5rem;
        border: none;
        background-color: #007bff;
        color: white;
        cursor: pointer;
    }
</style>

<form class="todo-form" on:submit={addToList}>
    <input type="text" placeholder="New todo" bind:value={newItem}/>
    <select name="category">
        {#each categories as category}
            <option>{category}</option>
        {/each}
    </select>
    <button type="submit" value="Add">Add</button>
</form>
