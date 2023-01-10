<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_categories = ref(null);

const todos_asc = computed(() =>
	todos.value.sort((a, b) => b.createdAt - a.createdAt)
);

const addTodo = () => {
	if (input_content.value.trim() === "" || input_categories.value === null) {
		return;
	}
	todos.value.push({
		content: input_content.value,
		category: input_categories.value,
		isFinished: false,
		createdAt: new Date().getTime(),
	});
	input_categories.value = null;
	input_content.value = "";
};

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo);
};

watch(name, (newvalue) => {
	console.log(newvalue);
	if (newvalue === "") {
		localStorage.removeItem("name");
	} else {
		localStorage.setItem("name", newvalue);
	}
});

watch(
	todos,
	(newvalue) => {
		localStorage.setItem("todos", JSON.stringify(newvalue));
	},
	{ deep: true }
);

onMounted(() => {
	name.value = localStorage.getItem("name") || "";
	todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				What's up
				<input type="text" placeholder="Name Here" v-model="name" />
			</h2>
		</section>
		<section class="create-todo">
			<h3>CREATE A TODO</h3>
			<form @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input
					type="text"
					placeholder="e.g Make a video"
					v-model="input_content"
				/>
				<h4>Pick a Category</h4>
				<div class="options">
					<label>
						<input
							type="radio"
							name="category"
							value="business"
							v-model="input_categories"
						/>
						<span class="bubble business"></span>
						<h4>Business</h4>
					</label>
					<label>
						<input
							type="radio"
							name="category"
							value="personal"
							v-model="input_categories"
						/>
						<span class="bubble personal"></span>
						<h4>Personal</h4>
					</label>
				</div>
				<input type="submit" value="Add Todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>Todo List</h3>
			<div class="list">
				<div
					v-for="todo in todos_asc"
					:class="`todo-item ${todo.done && 'done'}`"
				>
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${todo.category}`"></span>
					</label>
					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>
					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>
			</div>
		</section>
	</main>
</template>
