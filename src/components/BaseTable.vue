<template>
  <div class="flex flex-col">
    <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
      <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
        <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">#</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Title</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Assignee</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(todo, index) in currentTodos" :key="todo.id" :class="index % 2 === 0 ? 'bg-white' : 'bg-gray-50'">
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                  {{ todo.id }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                  {{ todo.title }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                  {{ users.find(user => user.id === todo.userId).name }}
                </td>
                <td v-if="todo.completed" class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Done</td>
                <td v-else class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">In Progress</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                  <button @click="openTodo(todo)" class="text-indigo-600 hover:text-indigo-900">Edit</button>
                  <button @click="deleteTodo(todo.id)" class="px-6 text-red-600 hover:text-red-900">Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
  <Pagination :todos="todos" @updateCurrentTodos="updateCurrentTodos" @previousTodos="previousTodos" />
  <EditModal v-if="openedTodo" :todo="openedTodo" @updateItem="updateItem" />
</template>

<script>
import axios from 'axios';
import { ref } from 'vue';

import EditModal from '@/components/EditModal';
import Pagination from '@/components/Pagination';

export default {
  async setup() {
    let openedTodo = ref(null);
    const { data: todosResponse } = await axios.get('https://jsonplaceholder.typicode.com/todos');
    const { data: usersResponse } = await axios.get('https://jsonplaceholder.typicode.com/users');

    const pageNumber = ref(0);
    const todos = ref(todosResponse);
    const currentTodos = ref([]);
    const users = ref(usersResponse);

    // async function mappingIds(todosResponse) {
    //   await todosResponse.map(todo => {
    //     todos.value.push({
    //       // userIds start from 1 but we need to start from 0
    //       assignee: usersResponse[todo.userId - 1].name,
    //       ...todo
    //     });
    //   });
    // }
    // mappingIds(todosResponse);
    // console.log(todos.value);

    function updateCurrentTodos() {
      let endIndex = pageNumber.value * 10 + 10;
      let startIndex = pageNumber.value * 10;
      if (endIndex > todos.value.length) {
        endIndex = todos.value.length;
      }
      currentTodos.value = todos.value.slice(startIndex, endIndex);
      pageNumber.value++;
    }
    function previousTodos() {
      if (pageNumber.value > 0) {
        pageNumber.value--;
      }
      let endIndex = pageNumber.value * 10 + 10;
      let startIndex = pageNumber.value * 10;
      if (endIndex > todos.value.length) {
        endIndex = todos.value.length;
      }
      currentTodos.value = todos.value.slice(startIndex, endIndex);
    }

    updateCurrentTodos();

    function openTodo(result) {
      console.log('clicked');
      openedTodo.value = result;
      console.log(openedTodo.value);
    }

    function deleteTodo(id) {
      axios
        .delete(`https://jsonplaceholder.typicode.com/todos/${id}`)
        .then(() => {
          todos.value = todos.value.filter(item => item.id !== id);
          updateCurrentTodos();
        })
        .catch(error => {
          console.log(error);
        });
    }

    function updateItem(item) {
      let index = todos.value.findIndex(todoItem => todoItem.id === item.id);
      todos.value[index] = item;
      updateCurrentTodos();
    }

    return {
      todos,
      openTodo,
      openedTodo,
      deleteTodo,
      updateItem,
      updateCurrentTodos,
      previousTodos,
      currentTodos,
      users
    };
  },
  components: {
    EditModal,
    Pagination
  }
};
</script>
