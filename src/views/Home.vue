<template>
	<div>
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
import Tasks from "../components/Tasks.vue";
import AddTask from "../components/AddTask.vue";
export default {
	name: "Home",
	props: {
		showAddTask: Boolean,
	},
	components: {
		Tasks,
		AddTask,
	},
	data() {
		return {
			tasks: [],
		};
	},
	methods: {
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
