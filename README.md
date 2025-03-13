# TO-DO-Tasts-Svelte
Este código implementa una lista de tareas con las siguientes funcionalidades:  Añadir, editar y marcar tareas como completadas.  Filtrar tareas por estado (all, active, completed).  Persistir las tareas en localStorage.  Mostrar el número de tareas pendientes.

1. Script (TypeScript)
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<script lang="ts">
El código está escrito en TypeScript (lang="ts"), lo que permite tipado estático y mejor autocompletado.

Definición de tipos
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
type Todo = {
	text: string
	done: boolean
}
type Filters = 'all' | 'active' | 'completed'
Todo: Define la estructura de una tarea, que tiene dos propiedades:

text: El texto de la tarea.

done: Un booleano que indica si la tarea está completada o no.

Filters: Define los posibles filtros para mostrar las tareas: 'all', 'active' o 'completed'.

Estado reactivo
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
let todos = $state<Todo[]>([])
let filter = $state<Filters>('all')
let filteredTodos = $derived(filterTodos())
todos: Un array reactivo que almacena todas las tareas. Se inicializa como un array vacío.

filter: Un estado reactivo que almacena el filtro actual. Se inicializa con 'all'.

filteredTodos: Una variable derivada ($derived) que se actualiza automáticamente cuando cambian todos o filter. Su valor se calcula llamando a la función filterTodos().

Efectos reactivos
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
$effect(() => {
	const savedTodos = localStorage.getItem('todos')
	savedTodos && (todos = JSON.parse(savedTodos))
})

$effect(() => {
	localStorage.setItem('todos', JSON.stringify(todos))
})
Primer $effect: Cuando el componente se monta, recupera las tareas guardadas en localStorage y las asigna a todos.

Segundo $effect: Cada vez que todos cambia, guarda el nuevo estado en localStorage. Esto permite persistir las tareas incluso si se recarga la página.

Funciones principales
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
function addTodo(event: KeyboardEvent) {
	if (event.key !== 'Enter') return

	const todoEl = event.target as HTMLInputElement
	const text = todoEl.value
	const done = false

	todos = [...todos, { text, done }]

	todoEl.value = ''
}
addTodo: Añade una nueva tarea cuando el usuario presiona la tecla Enter.

Obtiene el valor del input (todoEl.value).

Crea un nuevo objeto Todo con text y done = false.

Actualiza todos con la nueva tarea.

Limpia el input.
  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
function editTodo(event: Event) {
	const inputEl = event.target as HTMLInputElement
	const index = +inputEl.dataset.index!
	todos[index].text = inputEl.value
}
editTodo: Edita el texto de una tarea existente.

Obtiene el índice de la tarea desde data-index.

Actualiza el texto de la tarea correspondiente en todos.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
function toggleTodo(event: Event) {
	const inputEl = event.target as HTMLInputElement
	const index = +inputEl.dataset.index!
	todos[index].done = !todos[index].done
}
toggleTodo: Cambia el estado de una tarea entre completada y no completada.

Obtiene el índice de la tarea desde data-index.

Invierte el valor de done para la tarea correspondiente.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
function setFilter(newFilter: Filters) {
	filter = newFilter
}
setFilter: Cambia el filtro actual.

Actualiza el estado filter con el nuevo valor.

typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
function filterTodos() {
	switch (filter) {
		case 'all':
			return todos
		case 'active':
			return todos.filter((todo) => !todo.done)
		case 'completed':
			return todos.filter((todo) => todo.done)
	}
}
filterTodos: Filtra las tareas según el valor de filter.

'all': Devuelve todas las tareas.

'active': Devuelve solo las tareas no completadas.

'completed': Devuelve solo las tareas completadas.

typescript
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
function remaining() {
	return todos.filter((todo) => !todo.done).length
}
remaining: Devuelve el número de tareas no completadas.

2. HTML (Svelte)
html
Copy
<input onkeydown={addTodo} placeholder="Add todo" type="text" />
Run HTML
Un input que permite al usuario añadir nuevas tareas. Llama a addTodo cuando se presiona una tecla.

html
Copy
<div class="todos">
	{#each filteredTodos as todo, i}
		<div class:completed={todo.done} class="todo">
			<input oninput={editTodo} data-index={i} value={todo.text} type="text" />
			<input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox" />
		</div>
	{/each}
</div>
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#each: Itera sobre filteredTodos para mostrar cada tarea.

Cada tarea tiene un input de texto para editar y un checkbox para marcar como completada.

class:completed={todo.done}: Aplica la clase completed si la tarea está completada.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
html
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
<div class="filters">
	<button onclick={() => setFilter('all')}>All</button>
	<button onclick={() => setFilter('active')}>Active</button>
	<button onclick={() => setFilter('completed')}>Completed</button>
</div>
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
HTML
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Botones para cambiar el filtro actual. Cada botón llama a setFilter con el valor correspondiente.

html
Copy
<p>{remaining()} remaining</p>
HTML
Muestra el número de tareas no completadas.
