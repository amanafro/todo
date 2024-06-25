<script>
  let newItem = '';
  let newCategory = "";
  let fromDate = "";
  let toDate = "";
  let todoList = [];
  let searchTerm = "";


  if (localStorage.getItem('todos')) {
    todoList = JSON.parse(localStorage.getItem('todos'));
  }

  function addToList() {
    if (newItem.trim()) { // Check if input is not empty
      todoList.push({
        text: newItem,
        status: false,
        category: newCategory,
        fromDate: fromDate,
        toDate: toDate,
      });
      newItem = '';
      newCategory = '';
      fromDate = null;
      toDate = null;
      saveToLocalStorage(); // Save updated list after adding
    }
  }


  function removeFromList(index) {
    todoList.splice(index, 1);
    saveToLocalStorage();
  }

  function saveToLocalStorage() {
    localStorage.setItem('todos', JSON.stringify(todoList));
  }

  let showInput = false;
</script>

<input bind:value={searchTerm} type="search" placeholder="Search...">
<button on:click={() => showInput = !showInput} type="button">Add New</button>
<br><br><br>
{#if showInput}
  <input bind:value={newItem} type="text" placeholder="new todo item..">
  <select bind:value={newCategory}>
    <option value="">Select Category</option>
    <option value="Blog">Blog</option>
    <option value="Social Media">Social Media</option>
  </select>
  <input type="date" bind:value={fromDate} />
  <input type="date" bind:value={toDate} />
  <button on:click={addToList}>Add</button>
{/if}



<br/>
{#each todoList as item, index}
  <div>
    <input bind:checked={item.status} type="checkbox">
    <span class:checked={item.status}>{item.text}</span>
    <span> - Category: {item.category}</span>
    <span> - From: {item.fromDate ? new Date(item.fromDate).toLocaleDateString() : 'No Due Date'}</span>
    <span> - To: {item.toDate ? new Date(item.toDate).toLocaleDateString() : 'No Due Date'}</span>
    <button on:click={() => removeFromList(index)}>Löschen</button>
    <br/>
  </div>
{/each}

<style>
  .checked {
    text-decoration: line-through;
  }
</style>
