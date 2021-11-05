<template>
	<div class="container">
		<Header
			@toggle-add-task="toggleAddTask"
			title="Task Tracker"
			:showAddTask="showAddTask"
		/>
		<div v-show="showAddTask">
			<AddTask @add-task="addTask" />
		</div>
		<Tasks
			@toggle-reminder="toggleReminder"
			@delete-task="deleteTask"
			:tasks="tasks"
		/>
	</div>
</template>

<script>
import axios from "axios";
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";
export default {
	name: "App",
	components: {
		Header,
		Tasks,
		AddTask,
	},
	data() {
		return {
			tasks: [],
			showAddTask: false,
		};
	},
	methods: {
		toggleAddTask() {
			this.showAddTask = !this.showAddTask;
		},
		addTask(task) {
			axios.post(`api/tasks`, task).then((response) => {
				console.log(response);
				this.tasks = [...this.tasks, response.data];
			});
		},
		deleteTask(id) {
			if (confirm("Are you sure?")) {
				// this.tasks = this.tasks.filter((task) => task.id !== id);
				axios.delete(`api/tasks/${id}`).then(() => {
					const taskId = this.tasks.indexOf(id);
					this.tasks.splice(taskId, 1);
				});
			}
		},
		toggleReminder(id) {
			this.tasks.map((task) =>
				task.id === id
					? axios
							.put(`api/tasks/${id}`, { ...task, reminder: !task.reminder })
							.then((response) => {
								this.tasks = this.tasks.map((task_update) =>
									task_update.id === id
										? { ...task, reminder: !task.reminder }
										: task_update
								);
							})
					: task
			);
		},
	},
	created() {
		// this.tasks = [
		// 	{
		// 		id: 1,
		// 		text: "Task 1",
		// 		day: "March 1",
		// 		reminder: true,
		// 	},
		// 	{
		// 		id: 2,
		// 		text: "Task 2",
		// 		day: "March 2",
		// 		reminder: false,
		// 	},
		// 	{
		// 		id: 3,
		// 		text: "Task 3",
		// 		day: "March 3",
		// 		reminder: true,
		// 	},
		// ];
		axios.get(`api/tasks`).then((response) => {
			this.tasks = response.data;
		});
	},
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");
* {
	box-sizing: border-box;
	margin: 0;
	padding: 0;
}
body {
	font-family: "Poppins", sans-serif;
}
.container {
	max-width: 500px;
	margin: 30px auto;
	overflow: auto;
	min-height: 300px;
	border: 1px solid steelblue;
	padding: 30px;
	border-radius: 5px;
}
.btn {
	display: inline-block;
	background: #000;
	color: #fff;
	border: none;
	padding: 10px 20px;
	margin: 5px;
	border-radius: 5px;
	cursor: pointer;
	text-decoration: none;
	font-size: 15px;
	font-family: inherit;
}
.btn:focus {
	outline: none;
}
.btn:active {
	transform: scale(0.98);
}
.btn-block {
	display: block;
	width: 100%;
}
</style>
