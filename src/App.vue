<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                Olá, <input type="text" placeholder="Digite seu nome" v-model="name" />
            </h2>
        </section>

        <section class="create-todo">
            <h3>CRIAR TAREFA</h3>

            <form @submit.prevent="addTodo">
                <h4>O que quer adicionar em sua lista de tarefas?</h4>
                <input type="text" placeholder="ex: subir no github" v-model="input_content" />
                <h4>Escolha uma categoria</h4>
                <div class="options">
                    <label>
                        <input type="radio" name="category" value="business" v-model="input_category" />
                        <span class="bubble business"></span>
                        <div>Trabalho</div>
                    </label>
                    <label>
                        <input type="radio" name="category" value="personal" v-model="input_category" />
                        <span class="bubble personal"></span>
                        <div>Pessoal</div>
                    </label>
                </div>
                <input type="submit" value="Criar Tarefa" />
            </form>
        </section>

        <section class="todo-list">
            <h3>Tarefas Criadas</h3>
            <div class="list">
                <div :class="`todo-item ${todo.done && 'done'}`" v-for="todo in todos_asc" :key="todo">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${todo.category}`"></span>
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)">Remover</button>
                    </div>
                </div>
            </div>
        </section>
    </main>
</template>

<script setup>
    import { ref, onMounted, computed, watch } from 'vue'

    const todos = ref([])
    const name = ref('')

    const input_content = ref('')
    const input_category = ref(null)

    const todos_asc = computed(() => todos.value.sort((a, b) => {
        return b.created_at - a.created_at
    }))

    const addTodo = () => {
        if (input_content.value.trim() === '' || input_category.value === null) {
            return
        }
        
        todos.value.push({
            content: input_content.value,
            category: input_category.value,
            done: false,
            created_at: new Date().getTime()
        })

        input_content.value = ''
        input_category.value = null
    }

    const removeTodo = todo => {
        todos.value = todos.value.filter(t => t !== todo)
    }

    watch(todos, newVal => {
        localStorage.setItem('todos', JSON.stringify(newVal))
    }, { deep: true })

    watch(name, (newVal) => {
        localStorage.setItem('name', newVal)
    })

    onMounted(() => {
        name.value = localStorage.getItem('name') || ''
        todos.value = JSON.parse(localStorage.getItem('todos')) || []
    })
</script>