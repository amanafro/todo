<script>
  import Popup from "./lib/Popup.svelte";
  let newItem = "";
  let searchTerm = "";

  export let categories = ["Sport", "Work/School", "Others", "Appointment"];

  let todoList = [
    { text: "Write my first post", status: true, category: "Work/School" },
    { text: "Upload the post to the blog", status: false, category: "Work/School" },
    { text: "Publish the post at Facebook", status: false, category: "Social Media" },
  ];

  function addToList() {
    todoList = [...todoList, { text: newItem, status: false, category: categories[0] }];
    newItem = "";
  }

  let showInput = false;

  function filterTodoList(selectedCategory) {
    if (selectedCategory === "All posts") {
      return todoList.filter((item) => item.text.toLowerCase().startsWith(searchTerm.toLowerCase()));
    }
    return todoList.filter(
            (item) => item.category === selectedCategory && item.text.toLowerCase().startsWith(searchTerm.toLowerCase())
    );
  }

  function removeFromList(index) {
    todoList.splice(index, 1);
  }

  let selectedCategory = "All posts";
</script>

<input id="search-input" type="text" placeholder="Search todos" on:input={(event) => (searchTerm = event.target.value)}/>
<select id="todo-category" on:change={(event) => (selectedCategory = event.target.value)}>
  <option value="All posts">All posts</option>
  <option value="Sport">Sport</option>
  <option value="Work/School">Work/School</option>
  <option value="Others">Others</option>
  <option value="Appointment">Appointment</option>
</select>

<button on:click={() => (showInput = !showInput)}>Add</button>

<br />

{#if showInput}
  <Popup/>
{/if}

<br />

{#each filterTodoList(selectedCategory) as item, index}
  <input bind:checked={item.status} type="checkbox" />
  <span class:checked={item.status}>{item.text}</span>
  <button on:click={() => removeFromList(index)}>Delete</button>
  <br />
{/each}

<style>
  .checked {
    text-decoration: line-through;
  }
</style>
