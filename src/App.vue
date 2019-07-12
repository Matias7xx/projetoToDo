<template>
	<div id="app">
		<h1>Tarefas</h1>
		<TasksProgress :progress="progress" />
		<NewTask @taskAdded="addTask"/>
		<TaskGrid :tasks="tasks" 
			@taskDeleted="deleteTask"
			@taskStateChanged="toggleTaskState" />
	</div>
</template>

<script>
import TaskGrid from './components/TaskGrid'
import NewTask from './components/NewTask'
import TasksProgress from './components/TasksProgress'

export default {
	components: { TasksProgress, TaskGrid, NewTask },
	data() {
		return {
				tasks: []
		}
	},
	computed: {
		progress() {
			const total = this.tasks.length
			const done = this.tasks.filter(t => !t.pending).length
			return Math.round(done / total * 100) || 0
		}
	},
	watch: {
		tasks: {
			deep: true, //Faz um watch profundo, não só no que foi mexido mas também olha os objetos dentro te um array
			handler() { //O deep monitora o estado interno do elemento
				localStorage.setItem('tasks', JSON.stringify(this.tasks))
			}
		}
	},
	methods: {
		addTask(task) {
			const sameName = t => t.name === task.name //Verifica se já tem tarefas com mesmo nome/repetidas
			const reallyNew = this.tasks.filter(sameName).length == 0 //Filter que verifica se já tem tarefas com mesmo nome/repetidas
			if(reallyNew) {
				this.tasks.push({
					name: task.name,
					pending: task.pending || true
				})
			}
		},
		deleteTask(i) {
			this.tasks.splice(i, 1) //Indice, quantos quero excluir a partir desse índice
		},
		toggleTaskState(i) {
			this.tasks[i].pending = !this.tasks[i].pending
		}
	},
	created() {
		const json = localStorage.getItem('tasks')
		const array = JSON.parse(json)
		this.tasks = Array.isArray(array) ? array : []
	}
}
</script>

<style>
	body {
		font-family: 'Lato', sans-serif;
		background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
		color: #FFF;
	}

	#app {
		display: flex;
		flex: 1;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}

	#app h1 {
		margin-bottom: 5px;
		font-weight: 300;
		font-size: 3rem;
	}
</style>
