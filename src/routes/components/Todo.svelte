<script>
	import { onMount, afterUpdate, onDestroy } from 'svelte';

	let todos = [];
	let newTodo = '';
	let category = '';
	let dueDate = '';
	let completedPercentage = 0;

	function addTodo() {
		if (newTodo.trim() !== '') {
			todos = [...todos, { id: Date.now(), text: newTodo, category, dueDate, completed: false }];
			newTodo = '';
			category = '';
			dueDate = '';
		}
	}

	function removeTodo(id) {
		todos = todos.filter((todo) => todo.id !== id);
	}

	function completeTodo(id) {
		todos = todos.map((todo) => (todo.id === id ? { ...todo, completed: true } : todo));
	}

	function editTodo(id, newText) {
		todos = todos.map((todo) => (todo.id === id ? { ...todo, text: newText } : todo));
	}

	$: {
		const completedCount = todos.filter((todo) => todo.completed).length;
		const totalCount = todos.length;
		completedPercentage = totalCount > 0 ? (completedCount / totalCount) * 100 : 0;
	}

	onMount(() => {
		// Sample data for initial todos
		todos = [
			{ id: 1, text: 'Complete Svelte tutorial', completed: false, dueDate: '2023-11-30' },
			{ id: 2, text: 'Add Bootstrap to Todo List', completed: true, dueDate: '2023-12-05' }
		];
	});

	const updateHandler = () => {
		// Log completed percentage after each update (for demonstration)
		console.log('Completed Percentage:', completedPercentage);
	};

	afterUpdate(updateHandler);

	onDestroy(() => {
		// Clean up when the component is destroyed
		console.log('Component destroyed');
	});

	// Linear completed percentage section
	const linearCompletedPercentage = {
		get() {
			return completedPercentage + '%';
		}
	};
</script>

<main class="container mt-5 page-container">
	<div class="card">
		<div class="card-body">
			<h1 class="card-title mb-4">To-Do List</h1>

			<div class="input-group mb-3">
				<input
					type="text"
					bind:value={newTodo}
					placeholder="Enter a new task"
					class="form-control"
				/>
				<select bind:value={category} class="form-control">
					<option value="">Select Category</option>
					<option value="personal">Personal</option>
					<option value="work">Work</option>
					<!-- Add more categories as needed -->
				</select>
				<input type="date" bind:value={dueDate} placeholder="Due Date" class="form-control" />
				<button on:click={addTodo} class="btn btn-primary">Add Task</button>
			</div>

			<div class="task-container warning-bg">
				<h2>Pending Tasks</h2>
				<ul class="list-group">
					{#each todos as { id, text, category, dueDate, completed }}
						{#if !completed}
							<li class="list-group-item d-flex justify-content-between align-items-center">
								<div>
									<input
										type="text"
										bind:value={text}
										readonly={!completed}
										on:input={() => editTodo(id, text)}
										style="cursor: pointer; border: none; outline: none;"
									/>
									{#if category}<small class="text-muted">Category: {category}</small>{/if}
									{#if dueDate}<small class="text-muted">Due Date: {dueDate}</small>{/if}
								</div>
								<div>
									<button on:click={() => completeTodo(id)} class="btn btn-success btn-sm"
										>Complete</button
									>
									<button on:click={() => removeTodo(id)} class="btn btn-danger btn-sm"
										>Remove</button
									>
								</div>
							</li>
						{/if}
					{/each}
				</ul>
			</div>

			<div class="task-container success-bg">
				<h2>Completed Tasks</h2>
				<ul class="list-group">
					{#each todos as { id, text, category, dueDate, completed }}
						{#if completed}
							<li class="list-group-item d-flex justify-content-between align-items-center">
								<div>
									<input
										type="text"
										bind:value={text}
										readonly={!completed}
										on:input={() => editTodo(id, text)}
										style="cursor: pointer; border: none; outline: none;"
									/>
									{#if category}<small class="text-muted">Category: {category}</small>{/if}
									{#if dueDate}<small class="text-muted">Due Date: {dueDate}</small>{/if}
								</div>
								<div>
									<button on:click={() => removeTodo(id)} class="btn btn-danger btn-sm"
										>Remove</button
									>
								</div>
							</li>
						{/if}
					{/each}
				</ul>
			</div>
		</div>
	</div>
</main>

<!-- Linear completed percentage section -->
<section class="completed-percentage-container">
	<div class="card">
		<div class="card-body">
			<h2 class="card-title mb-4">Completed Percentage</h2>
			<div class="linear-progress-container">
				<div class="linear-progress" style="--percentage: {linearCompletedPercentage}" />
			</div>
			<div class="progress-label">{Math.round(completedPercentage)}%</div>
		</div>
	</div>
</section>

<style>
	main {
		max-width: 800px;
		margin: auto;
		background-color: #f7f7f7;
		padding: 20px;
		border-radius: 10px;
	}

	.card {
		border: 1px solid #ccc;
		border-radius: 10px;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	}

	.card-body {
		padding: 20px;
	}

	.input-group {
		margin-bottom: 20px;
	}

	.task-container {
		margin-bottom: 20px;
		padding: 15px;
		border-radius: 5px;
	}

	.warning-bg {
		background-color: #f8d7da; /* Bootstrap's warning color */
	}

	.success-bg {
		background-color: #d4edda; /* Bootstrap's success color */
	}

	/* Styles for the circular completed percentage section */
	.completed-percentage-container {
		margin-top: 20px;
	}

	.progress-label {
		position: absolute;
		width: 100%;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 14px;
	}

	/* Styles for the enhanced linear completed percentage section */
	.linear-progress-container {
		position: relative;
		width: 100%;
		height: 20px;
		background-color: #e0e0e0; /* Light gray color for the progress background */
		border-radius: 10px;
	}

	.linear-progress {
		position: absolute;
		height: 100%;
		width: var(--percentage);
		background-color: #4caf50; /* Green color for the progress fill */
		border-radius: 8px;
		transition: width 0.5s ease;
	}

	.progress-label {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		font-size: 14px;
		color: #333; /* Text color for the progress label */
	}
</style>
