<script>

  // alle attribute oder felder die ich brauche werden hier aufgelistet
  let title = "";
  let category = "";
  let startDate = null;
  let endDate = null;
  let description = "";
  let todoList = [];
  let searchText = "";
  let selectedCategory = "All posts";
  let author = "";
  let isUrgent = null;
  let priority = null;

  // todos werden aus dem localstorage aufgelistet

  if (localStorage.getItem('todos')) {
    todoList = JSON.parse(localStorage.getItem('todos'));
  }

  // hier werden die attribute aufgelistet, validateInput() schaut ob krietrien eingehalten sind oder nicht
  function addToDoList() {
    const isValid = validateInput();

    if (isValid) {
      todoList = [...todoList, {
        title,
        description,
        status: false,
        category,
        startDate,
        endDate,
        author,
        isUrgent,
        priority,
      }];
      clearInputFields();
      alert("Es wurde erfolgreich hinzugefügt😃👍.");
      localStorageFn();
    } else {
      // Handle invalid input
      alert("Fehler beim Zufügen. Bitte alle Felder korrekt ausfüllen.😟");
    }
  }

  // todos werden entfernt
  function removeFromList(index) {
    todoList.splice(index, 1);
    todoList = [...todoList];
    localStorageFn();
  }

  let debounceTimeout;

  // todos werden in den localstorage gespeichert
  function localStorageFn() {
    clearTimeout(debounceTimeout);
    debounceTimeout = setTimeout(() => {
      localStorage.setItem('todos', JSON.stringify(todoList));
    }, 100);
  }

  // filter funktionen for die kategorie und die suche
  function filterTodoList(searchText, selectedCategory) {
    return todoList.filter((item) => {
      const textMatch = item.title ? item.title.toLowerCase().includes(searchText.toLowerCase()) : false; // Check if newTodoText exists
      const categoryMatch = selectedCategory === "All posts" || item.category === selectedCategory;
      return textMatch && categoryMatch;
    });
  }

  // diese funktion handelt die sucheeingabe

  function handleSearchInput(event) {
    searchText = event.target.value;
    // todoList = filterTodoList(searchText, selectedCategory);
  }

  // validateInput() schaut ob krietrien eingehalten sind oder nicht

  function validateInput() {
    const titleIsValid = title.trim() && title.length <= 255;
    if (!titleIsValid) {
      alert("Todo muss Titel haben und es darf nicht über 255 Zeichen sein.❗️");
      return false;
    }

    const categoryIsValid = category !== "";
    if (!categoryIsValid) {
      return false;
    }

    const authorIsValid = author.trim() && author.length <= 20;
    if (!authorIsValid) {
      alert("Todo muss Autor haben mit max. 20 Zeichen.❗️");
      return false;
    }

    const descriptionIsValid = description.trim().length > 0;
    if (!descriptionIsValid) {
      return false;
    }
    return true;
  }

  // nach dem man neue todos eingefügt hat, werden diese felder geleert, damit man nicht alles manuell löscht
  function clearInputFields() {
    title = "";
    description = "";
    author = "";
    category = "";
    startDate = null;
    endDate = null;
    isUrgent = null;
    priority = null;
  }

  let showInput = false;

  let editingTodoIndex = null;

  // das wird gebrauch, damit ein todo spezifisch bearbeitet wird
  function editTodo(index) {
    editingTodoIndex = index;
  }

  // hier wird ein todo verarbeitet, die alten werte werden dirket durch das neue ersetzt
  function updateTodo() {
    const editedTodo = { ...todoList[editingTodoIndex] }; // Create a copy
    editedTodo.title = document.getElementById("edit-title").value;
    editedTodo.description = document.getElementById("edit-description").value;
    editedTodo.author = document.getElementById("edit-author").value;
    editedTodo.category = document.getElementById("edit-category").value;
    editedTodo.startDate = new Date(document.getElementById("edit-start-date").value);
    editedTodo.endDate = new Date(document.getElementById("edit-end-date").value);

    editedTodo.isUrgent = document.getElementById("edit-isUrgent").checked;
    editedTodo.priority = document.getElementById("edit-priority").checked;

    todoList[editingTodoIndex] = editedTodo;
    editingTodoIndex = null;
    localStorageFn();
  }

</script>

<h1>Füge etwas hinzu</h1>

<div class="menu">
<input bind:value={searchText} type="search" placeholder="Nach Todos suchen" on:input={handleSearchInput}>
<select id="todo-category" on:change={(event) => (selectedCategory = event.target.value)}>
  <option value="All posts">All posts</option>
  <option value="Appointment">Termin</option>
  <option value="Work/School">Arbeit/Schule</option>
  <option value="Sport">Sport</option>
  <option value="Others">Etwas anders</option>
</select>
<button on:click={() => (showInput = !showInput)}>Neues Todo?</button>
</div>

  {#if showInput}
    <div class="modal">
      <label>Tdoo:</label>
      <input bind:value={title} type="text" placeholder="Neues Todo hinzufügen" required>
      <label>Beschreibung:</label>
      <textarea bind:value={description} rows="8" cols="24" required placeholder="Kurze Beschreibung des Todos"></textarea>
      <label>Autor:</label>
      <input bind:value={author} type="text" placeholder="Autor" required>
      <label>Kategorie:</label>
      <select bind:value={category} required>
        <option value="All posts">All posts</option>
        <option value="Appointment">Termin</option>
        <option value="Work/School">Arbeit/Schule</option>
        <option value="Sport">Sport</option>
        <option value="Others">Etwas anders</option>
      </select>
      <label>
        Startdatum:
      <input type="date" bind:value={startDate} required />
      </label>
      <label>
        Enddatum:
      <input type="date" bind:value={endDate} required/>
      </label>
      <label>Dringend?
      <input bind:checked={isUrgent} type="checkbox" name="Dringen?">
      </label>
      <label>Priorität?
      <input bind:checked={priority} type="checkbox" name="Priorität?SS">
      </label>
      <button on:click={addToDoList}>Hinzufügen</button>
    </div>
  {/if}

<br/>
{#each todoList as item, index}
  {#if item.category === selectedCategory && item.title.includes(searchText)}
  <div class="list">
  <div class="todo">
    <div>
    <input bind:checked={item.status} type="checkbox">
    <span class:checked={item.status}>Todo: {item.title}</span>
    </div>
    <span>Kategorie: {item.category}</span>
    <br>
    <span>Von: {item.startDate ? new Date(item.startDate).toLocaleDateString() : 'No Due Date'}</span>
    <span> - Bis: {item.endDate ? new Date(item.endDate).toLocaleDateString() : 'No Due Date'}</span>
    <br/>
    <span>Autor: {item.author}</span>
    <span>Beschreibung: <br>{item.description}</span>
    {#if item.priority && item.isUrgent}
      <span class="bold"> Sofort erledigen!</span>
    {/if}
    {#if item.priority && !item.isUrgent}
      <span class="bold">️ Einplanen und Wohlfühlen </span>
    {/if}
    {#if !item.priority && item.isUrgent}
      <span class="bold">❗️Gib es ab❗️️</span>
    {/if}
    {#if !item.priority && !item.isUrgent}
      <span class="bold"> Weg damit ‍♂️</span>
    {/if}
    <br>
    <button on:click={() => removeFromList(index)}>Löschen</button>
    <button on:click={() => editTodo(index)}>Bearbeiten</button>
  </div>
  </div>
  {/if}
{/each}

{#if editingTodoIndex !== null}
  <div class="modal">
    <label for="edit-title">Titel:</label>
    <input id="edit-title" bind:value={todoList[editingTodoIndex].title} type="text" placeholder="new todo item.." required>
    <label for="edit-description">Beschreibung:</label>
    <textarea id="edit-description" bind:value={todoList[editingTodoIndex].description} rows="8" cols="24" required></textarea>
    <label for="edit-author">Autor:</label>
    <input id="edit-author" bind:value={todoList[editingTodoIndex].author} type="text" placeholder="Autor?">

    <label for="edit-category">Kategorie:</label>
    <select id="edit-category" bind:value={todoList[editingTodoIndex].category} required>
      <option value="All posts">All posts</option>
      <option value="Appointment">Termin</option>
      <option value="Work/School">Arbeit/Schule</option>
      <option value="Sport">Sport</option>
      <option value="Others">Etwas anders</option>
    </select>

    <label for="edit-start-date">Startdatum:</label>
    <input type="date" id="edit-start-date" bind:value={todoList[editingTodoIndex].startDate} required min={new Date().toISOString().split('T')[0]}>
    <label for="edit-end-date">Enddatum:</label>
    <input type="date" id="edit-end-date" bind:value={todoList[editingTodoIndex].endDate} required>

    <label>Dringend?
      <input bind:checked={todoList[editingTodoIndex].isUrgent} type="checkbox" name="isUrgent" id="edit-isUrgent">
    </label>
    <label>Priorität?
      <input bind:checked={todoList[editingTodoIndex].priority} type="checkbox" name="priority" id="edit-priority">
    </label>

    <button on:click={updateTodo}>Speichern</button>
    <button on:click={() => editingTodoIndex = null}>Abbrechen</button>
  </div>
{/if}

<style>
  .checked {
    text-decoration: line-through;
    color: lime;
  }
</style>
