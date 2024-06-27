<script>
  import {derived} from "svelte/store";

  let newTodo = '';
  let kategorie = "";
  let vonDatum = "";
  let bisDatum = "";
  let todoListe = [];
  let suche = "";
  let gewaehltekategorie = "All posts";

  if (localStorage.getItem('todos')) {
    todoListe = JSON.parse(localStorage.getItem('todos'));
  }

  function zuListeZufuegen() {
    if (newTodo.trim()) {
      todoListe.push({
        text: newTodo,
        status: false,
        category: kategorie,
        fromDate: vonDatum,
        toDate: bisDatum,
      });
      newTodo = '';
      kategorie = '';
      vonDatum = null;
      bisDatum = null;
      alert("Ihr Todo wurde erfolgreich hinzugefügt.");
      LocalStorage();
    }
  }

  function entfernenVonListe(index) {
    todoListe.splice(index, 1);
    LocalStorage();
  }

  let debounceTimeout;

  function LocalStorage() {
    clearTimeout(debounceTimeout);
    debounceTimeout = setTimeout(() => {
      localStorage.setItem('todos', JSON.stringify(todoListe));
    }, 100);
  }

  function filterTodoListe(searchTerm, selectedCategory) {
    return todoListe.filter((item) => {
      const textMatch = item.text.toLowerCase().includes(searchTerm.toLowerCase());
      const categoryMatch = selectedCategory === "All posts" || item.category === selectedCategory;
      return textMatch && categoryMatch;
    });
  }

  function suchEingabe(event) {
    suche = event.target.value;
  }

  let showInput = true;
</script>

<input bind:value={suche} type="search" placeholder="Search..." on:input={suchEingabe}>
<select id="todo-category" on:change={(event) => (gewaehltekategorie = event.target.value)}>
  <option value="All posts">All posts</option>
  <option value="Sport">Sport</option>
  <option value="Work/School">Work/School</option>
  <option value="Others">Others</option>
  <option value="Appointment">Appointment</option>
</select>
<button on:click={() => (showInput = !showInput)}>Add</button>
<br><br><br>

{status}


<div>
  {#if showInput}
    <div class="modal">
    <input bind:value={newTodo} type="text" placeholder="new todo item.." required>
    <select bind:value={kategorie} required>
      <option value="All posts">All posts</option>
      <option value="Sport">Sport</option>
      <option value="Work/School">Work/School</option>
      <option value="Others">Others</option>
      <option value="Appointment">Appointment</option>
    </select>
    <input type="date" bind:value={vonDatum} required/>
    <input type="date" bind:value={bisDatum} required/>
    <button on:click={zuListeZufuegen}>Add</button>
    </div>
  {/if}
</div>

<br/>
{#each filterTodoListe(suche ,gewaehltekategorie) as item, index}
  <div>
    <input bind:checked={item.status} type="checkbox">
    <span class:checked={item.status}>{item.text}</span>
    <span> - Category: {item.kategorie}</span>
    <span> - From: {item.vonDatum ? new Date(item.vonDatum).toLocaleDateString() : 'No Due Date'}</span>
    <span> - To: {item.bisDatum ? new Date(item.bisDatum).toLocaleDateString() : 'No Due Date'}</span>
    <button on:click={() => entfernenVonListe(index)}>Löschen</button>
    <br/>
  </div>
{/each}

<style>
  .checked {
    text-decoration: line-through;
  }
</style>
