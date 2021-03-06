<template>
  <div class="flex flex-col max-w-7xl mx-auto py-16 px-4 sm:py-24 sm:px-6 lg:px-8">
    <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
      <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
        <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">#</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Title</th>
                <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Assignee</th>
                <th scope="col" @click="sortStatus" class="inline-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Status
                  <ArrowSmDownIcon v-if="isAscending" class="ml-3 h-5 w-5 text-gray-400" />
                  <ArrowSmUpIcon v-else class="ml-3 h-5 w-5 text-gray-400" />
                </th>
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
    <Pagination :todos="todos" :pageNumber="pageNumber" @updatePageNumber="updatePageNumber" />
    <EditModal v-if="openedTodo && isClosed" :todo="openedTodo" @updateItem="updateItem" />
  </div>
</template>

<script>
import axios from 'axios';
import { ref } from 'vue';

import EditModal from '@/components/EditModal';
import Pagination from '@/components/Pagination';

import { ArrowSmDownIcon, ArrowSmUpIcon } from '@heroicons/vue/solid';

export default {
  async setup() {
    const openedTodo = ref(null);
    const isClosed = ref(false);
    const { data: todosResponse } = await axios.get('https://jsonplaceholder.typicode.com/todos');
    const { data: usersResponse } = await axios.get('https://jsonplaceholder.typicode.com/users');

    const pageNumber = ref(0);
    const todos = ref(todosResponse);
    const currentTodos = ref([]);
    const users = ref(usersResponse);

    const isAscending = ref(false);

    function updatePageNumber(newPageNumber) {
      pageNumber.value = newPageNumber;
      updateCurrentTodos();
    }

    function updateCurrentTodos() {
      let endIndex = pageNumber.value * 10 + 10;
      let startIndex = pageNumber.value * 10;
      if (endIndex > todos.value.length) {
        endIndex = todos.value.length;
      }
      currentTodos.value = todos.value.slice(startIndex, endIndex);
    }

    updateCurrentTodos();

    function openTodo(result) {
      openedTodo.value = result;
      isClosed.value = !isClosed.value;
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

    function sortStatus() {
      isAscending.value = !isAscending.value;
      console.log('sorted');
      if (isAscending.value === false) {
        todos.value.sort((a, b) => a.completed - b.completed);
      } else {
        todos.value.sort((a, b) => b.completed - a.completed);
      }
      console.log(todos.value);
      updateCurrentTodos();
    }

    return {
      todos,
      openTodo,
      openedTodo,
      deleteTodo,
      updateItem,
      updateCurrentTodos,
      currentTodos,
      users,
      updatePageNumber,
      pageNumber,
      isClosed,
      sortStatus,
      isAscending
    };
  },
  components: {
    EditModal,
    Pagination,
    ArrowSmDownIcon,
    ArrowSmUpIcon
  }
};
</script>
