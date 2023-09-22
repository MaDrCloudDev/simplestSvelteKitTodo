<script lang="ts">
	import { onMount } from 'svelte';

	let newTodo: string = '';
	let todos: { text: string; status: boolean }[] = [];

	onMount(() => {
		const savedTodosString = localStorage.getItem('todos');
		if (savedTodosString) {
			const savedTodos = JSON.parse(savedTodosString) as { text: string; status: boolean }[];
			if (savedTodos) {
				todos = savedTodos;
			}
		}
	});

	function addToList() {
		if (newTodo.trim() !== '') {
			todos = [...todos, { text: newTodo, status: false }];
			newTodo = '';
			saveTodos();
		}
	}

	function removeFromList(index: number) {
		todos.splice(index, 1);
		todos = [...todos];
		saveTodos();
	}

	function saveTodos() {
		localStorage.setItem('todos', JSON.stringify(todos));
	}
</script>

<div class="container">
	<h1>simplestSvelteTodo</h1>
	<div>
		<input bind:value={newTodo} type="text" placeholder="Add todo..." />
		<button on:click={addToList}>Add</button>
	</div>
	<div>
		{#each todos as item, index}
			<div>
				<input bind:checked={item.status} type="checkbox" />
				<span class:checked={item.status}>{item.text}</span>
				<button
					class="remove_button"
					on:click={() => removeFromList(index)}
					aria-label="Remove todo">❌</button
				>
			</div>
			<br />
		{/each}
	</div>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
		height: 90vh;
		justify-content: center;
	}
	input,
	button {
		cursor: pointer;
	}
	input {
		margin-bottom: 8px;
	}
	.remove_button {
		background-color: transparent;
		border: none;
	}
	.checked {
		text-decoration: line-through;
	}
</style>
