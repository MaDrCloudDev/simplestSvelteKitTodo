<script lang="ts">
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';

	// Define a todo type
	interface Todo {
		text: string;
		completed: boolean;
	}

	// Initialize a writable store for todos
	const todos = writable<Todo[]>([]);

	// Initialize a writable store for newTodo
	let newTodo = '';

	// Load todos from localStorage on mount
	onMount(() => {
		if (typeof window !== 'undefined') {
			const storedTodos = localStorage.getItem('todos');
			todos.set(storedTodos ? JSON.parse(storedTodos) : []);
		}
	});

	// Function to update todos and save to localStorage
	function updateTodos(callback: (currentTodos: Todo[]) => Todo[]) {
		todos.update((currentTodos) => {
			const updatedTodos = callback(currentTodos);
			localStorage.setItem('todos', JSON.stringify(updatedTodos));
			return updatedTodos;
		});
	}

	// Add todo function
	function addTodo() {
		if (newTodo) {
			updateTodos((currentTodos) => [...currentTodos, { text: newTodo, completed: false }]);
			newTodo = ''; // Clear the input after adding a todo
		}
	}

	// Toggle completed status function
	function toggleCompleted(index: number) {
		updateTodos((currentTodos) =>
			currentTodos.map((todo, i) => (i === index ? { ...todo, completed: !todo.completed } : todo))
		);
	}

	// Delete todo function
	function deleteTodo(index: number) {
		updateTodos((currentTodos) => currentTodos.filter((_, i) => i !== index));
	}

	// Handle key down event
	function handleKeyDown(event: KeyboardEvent, index: number) {
		if (event.key === 'Enter') {
			toggleCompleted(index);
		}
	}
</script>

<div class="container">
	<div class="todo-app">
		<h1 class="title">simplestSvelteKitTodo</h1>

		<div>
			<input type="text" bind:value={newTodo} placeholder="New Todo" />
			<button on:click={addTodo}>Add Todo</button>
		</div>

		{#if $todos.length > 0}
			<ul>
				{#each $todos as todo, index (todo.text)}
					<li>
						<label>
							<input
								type="checkbox"
								checked={todo.completed}
								on:change={() => toggleCompleted(index)}
							/>
							<span class={todo.completed ? 'completed' : ''}>{todo.text}</span>
						</label>
						<button class="delete" on:click={() => deleteTodo(index)}>Delete</button>
					</li>
				{/each}
			</ul>
		{/if}
	</div>
</div>

<style>
	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 100vh;
	}

	.todo-app {
		max-width: 400px;
		width: 100%;
		text-align: center;
		background-color: #333;
		color: #fff;
		padding: 20px;
		border-radius: 10px;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
	}

	.todo-app input[type='text'],
	.todo-app button {
		font-size: 16px;
		margin: 5px;
		padding: 10px;
		border: none;
		border-radius: 5px;
		outline: none;
	}

	.todo-app button {
		background-color: #4caf50;
		color: #fff;
		cursor: pointer;
	}

	.todo-app ul {
		list-style-type: none;
		padding: 0;
	}

	.todo-app li {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 5px 0;
		border-bottom: 1px solid #555;
	}

	.todo-app label input[type='checkbox'] {
		float: left;
		margin: 3px 10px 0 0;
	}

	.todo-app label {
		width: 100%;
		cursor: pointer;
	}

	.todo-app span.completed {
		text-decoration: line-through;
	}

	.todo-app button.delete {
		background-color: #ff5722;
		margin-left: 10px;
	}
</style>
