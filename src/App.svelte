<script>
  import { onMount } from 'svelte';
  
  let count = 0;
  let todos = [
    { id: 1, text: 'Integrate Whatfix SDK', completed: false },
    { id: 2, text: 'Test Whatfix features', completed: false }
  ];
  let newTodo = '';
  let message = '';

  onMount(() => {
    console.log('🚀 Component mounted');
    
    // Still check for Whatfix in console for your debugging
    const checkWhatfix = setInterval(() => {
      if (window._wfx_settings || window.whatfix) {
        console.log('✅ Whatfix SDK detected!');
        clearInterval(checkWhatfix);
      }
    }, 500);

    setTimeout(() => clearInterval(checkWhatfix), 5000);
    
    return () => clearInterval(checkWhatfix);
  });

  $: console.log('📊 Count changed to:', count);

  function increment() {
    count++;
    console.log('🔼 Counter incremented');
    showMessage('Counter incremented!');
  }

  function decrement() {
    count--;
    console.log('🔽 Counter decremented');
    showMessage('Counter decremented!');
  }

  function reset() {
    count = 0;
    console.log('🔄 Counter reset');
    showMessage('Counter reset!');
  }

  function addTodo() {
    if (newTodo.trim()) {
      todos = [...todos, {
        id: Date.now(),
        text: newTodo,
        completed: false
      }];
      console.log('➕ Todo added:', newTodo);
      newTodo = '';
      showMessage('Todo added!');
    }
  }

  function handleKeyPress(e) {
    if (e.key === 'Enter') addTodo();
  }

  function toggleTodo(id) {
    todos = todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    );
    console.log('✔️ Todo toggled:', id);
  }

  function deleteTodo(id) {
    todos = todos.filter(todo => todo.id !== id);
    console.log('🗑️ Todo deleted:', id);
    showMessage('Todo deleted!');
  }

  function showMessage(msg) {
    message = msg;
    setTimeout(() => message = '', 2000);
  }
</script>

<div class="min-h-screen bg-gradient-to-br from-purple-50 to-blue-50 p-8">
  <div class="max-w-4xl mx-auto">
    <header class="text-center mb-8">
      <h1 class="text-4xl font-bold text-gray-800 mb-2">Whatfix Integration Test App</h1>
      <p class="text-gray-600">A Svelte app for testing Whatfix Web SDK</p>
    </header>

    {#if message}
      <div class="mb-6 p-4 bg-blue-100 border border-blue-300 text-blue-800 rounded-lg text-center">
        {message}
      </div>
    {/if}

    <div class="grid md:grid-cols-2 gap-6 mb-6">
      <!-- Counter Section -->
      <div class="bg-white rounded-xl shadow-lg p-8" data-whatfix-element="counter-section">
        <h2 class="text-2xl font-semibold text-gray-800 mb-4">Counter</h2>
        <div class="text-center mb-6">
          <div class="text-6xl font-bold text-purple-600 mb-4" data-whatfix-element="counter-value">
            {count}
          </div>
          <div class="flex gap-3 justify-center flex-wrap">
            <button on:click={decrement} data-whatfix-element="decrease-button" 
              class="px-6 py-3 bg-red-500 text-white rounded-lg hover:bg-red-600 transition">
              Decrease
            </button>
            <button on:click={reset} data-whatfix-element="reset-button"
              class="px-6 py-3 bg-gray-500 text-white rounded-lg hover:bg-gray-600 transition">
              Reset
            </button>
            <button on:click={increment} data-whatfix-element="increase-button"
              class="px-6 py-3 bg-green-500 text-white rounded-lg hover:bg-green-600 transition">
              Increase
            </button>
          </div>
        </div>
      </div>

      <!-- Todo List Section -->
      <div class="bg-white rounded-xl shadow-lg p-8" data-whatfix-element="todo-section">
        <h2 class="text-2xl font-semibold text-gray-800 mb-4">Todo List</h2>
        <div class="mb-6">
          <div class="flex gap-2">
            <input type="text" bind:value={newTodo} on:keypress={handleKeyPress}
              placeholder="Add a new todo..." data-whatfix-element="todo-input"
              class="flex-1 px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-500" />
            <button on:click={addTodo} data-whatfix-element="add-todo-button"
              class="px-6 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 transition">
              Add
            </button>
          </div>
        </div>

        <div class="space-y-2" data-whatfix-element="todo-list">
          {#each todos as todo (todo.id)}
            <div class="flex items-center gap-3 p-3 bg-gray-50 rounded-lg hover:bg-gray-100 transition" data-whatfix-element="todo-item">
              <button on:click={() => toggleTodo(todo.id)} data-whatfix-element="todo-toggle" class="flex-shrink-0">
                <span class="text-2xl">{todo.completed ? '✅' : '⭕'}</span>
              </button>
              <span class="flex-1 {todo.completed ? 'line-through text-gray-500' : 'text-gray-800'}">
                {todo.text}
              </span>
              <button on:click={() => deleteTodo(todo.id)} data-whatfix-element="delete-todo-button"
                class="px-3 py-1 text-sm bg-red-500 text-white rounded hover:bg-red-600 transition">
                Delete
              </button>
            </div>
          {/each}
        </div>

        {#if todos.length === 0}
          <p class="text-center text-gray-500 py-8">No todos yet. Add one above!</p>
        {/if}
      </div>
    </div>

    <!-- Element Reference -->
    <div class="p-4 bg-blue-50 border border-blue-200 rounded-lg">
      <h4 class="font-semibold text-blue-900 mb-2">📍 Elements Tagged for Whatfix:</h4>
      <div class="grid md:grid-cols-2 gap-4 text-sm text-blue-800">
        <div>
          <p class="font-medium mb-1">Counter Section:</p>
          <ul class="list-disc list-inside ml-2 space-y-1">
            <li>counter-section</li>
            <li>counter-value</li>
            <li>increase-button</li>
            <li>decrease-button</li>
            <li>reset-button</li>
          </ul>
        </div>
        <div>
          <p class="font-medium mb-1">Todo Section:</p>
          <ul class="list-disc list-inside ml-2 space-y-1">
            <li>todo-section</li>
            <li>todo-input</li>
            <li>add-todo-button</li>
            <li>todo-list</li>
            <li>todo-item</li>
            <li>todo-toggle</li>
            <li>delete-todo-button</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  :global(body) {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
      'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
  }
</style>
