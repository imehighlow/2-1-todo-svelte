<script>
  import '../app.css';

  let newTask = $state("");
  let tasks = $state([]);

  let hasCompleted = $derived.by(() => {
    return tasks.some((t) => t.isDone);
  });
  let completedCount = $derived(tasks.filter((t) => t.isDone).length);

  const addTaskHandler = () => {
    const newTaskObj = {
      text: newTask,
      isDone: false,
    };

    if (newTask.length !== 0) tasks.push(newTaskObj);
    newTask = "";
  };

  const enterEventHandler = (event) => {
    if (event.key === "Enter") {
      addTaskHandler();
    }
  };

  const removeHandler = (i) => {
    if (tasks.length > 0) tasks.splice(i, 1);
  };

  const removeCompleted = () => {
    const result = tasks.filter((t) => !t.isDone);
    tasks = result;
  };
</script>

<div class="app-container">
  <header class="app-header">
    <h1>Todo App</h1>
  </header>

  <div class="input-form">
    <input 
      type="text" 
      name="task" 
      bind:value={newTask} 
      placeholder="What needs to be done?"
      class="task-input"
      onkeydown={enterEventHandler}
    />
    <button type="button" onclick={addTaskHandler} class="add-btn">
      <span>Add Task</span>
    </button>
  </div>

  <div class="tasks-container">
    {#if tasks.length !== 0}
      <ul class="task-list">
        {#each tasks as task, i}
          <li class="task-item" class:completed={task.isDone}>
            <label class="checkbox-container">
              <input type="checkbox" name="isCompleted" bind:checked={task.isDone} />
              <span class="checkmark"></span>
            </label>
            <button
              type="button"
              class="row-toggle-btn"
              aria-label={task.isDone ? "Mark as incomplete" : "Mark as complete"}
              onclick={() => task.isDone = !task.isDone}
            >
              <span class="task-text">{task.text}</span>
            </button>
            <button type="button" onclick={() => removeHandler(i)} class="remove-btn">
              <span>×</span>
            </button>
          </li>
        {/each}
      </ul>

      <div class="task-summary">
        <span class="task-count">
          {tasks.filter(t => !t.isDone).length} of {tasks.length} tasks remaining
        </span>
        <button
          type="button"
          onclick={removeCompleted}
          disabled={!hasCompleted}
          class="clear-completed-btn"
        >
          Clear Completed ({completedCount})
        </button>
      </div>
    {:else}
      <div class="empty-state">
        <p>No tasks yet</p>
      </div>
    {/if}
  </div>
</div>
