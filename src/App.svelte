<script>
let newTodoText = '';
let kategorie = "";
let vonDatum = "";
let bisDatum = "";
let beschreibung = "";
let todoListe = [];
let suche = "";
let gewaehltekategorie = "All posts";

if (localStorage.getItem('todos')) {
        todoListe = JSON.parse(localStorage.getItem('todos'));
}

function zuListeZufuegen() {
        if (newTodoText.trim()) {
        todoListe = [
        ...todoListe,
        { newTodoText: newTodoText, beschreibung: beschreibung, status: false, kategorie: kategorie, vonDatum: vonDatum, bisDatum: bisDatum },
        ];
        newTodoText = '';
        beschreibung = "";
        kategorie = '';
        vonDatum = null;
        bisDatum = null;
        alert("Ihr Todo wurde erfolgreich hinzugef?gt.");
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
        const textMatch = item.newTodoText.toLowerCase().includes(searchTerm.toLowerCase());
        const categoryMatch = selectedCategory === "All posts" || item.category === selectedCategory;
        return textMatch && categoryMatch;
});
}

function suchEingabe(event) {
        suche = event.target.value;
}

let showInput = false;

</script>
<div class="menu">
<input bind:value={suche} type="search" placeholder="Search..." on:input={suchEingabe}>
<select id="todo-category" on:change={(event) => (gewaehltekategorie = event.target.value)}>
  <option value="All posts">All posts</option>
  <option value="Sport">Sport</option>
  <option value="Work/School">Work/School</option>
  <option value="Others">Others</option>
  <option value="Appointment">Appointment</option>
</select>
<button on:click={() => (showInput = !showInput)}>Add</button>
</div>

<div>
  {#if showInput}
    <div class="modal">
      <input bind:value={newTodoText} type="text" placeholder="new todo item.." required>
      <input bind:value={beschreibung} type="text" placeholder="kurze beschreibung." required>
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
  <div class="list">
  <div class="todo">
    <div>
    <input bind:checked={item.status} type="checkbox">
    <span class:checked={item.status}>Todo: {item.newTodoText}</span>
    </div>
    <span>Category: {item.kategorie}</span>
    <br>
    <span>From: {item.vonDatum ? new Date(item.vonDatum).toLocaleDateString() : 'No Due Date'}</span>
    <span> - To: {item.bisDatum ? new Date(item.bisDatum).toLocaleDateString() : 'No Due Date'}</span>
    <br/>
    <span>Beschreibung: <br>{item.beschreibung}</span>
    <br>
    <button on:click={() => entfernenVonListe(index)}>L?schen</button>
  </div>
  </div>
{/each}

<style>
  .checked {
    text-decoration: line-through;
  }
</style>
