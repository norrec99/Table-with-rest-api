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
              <tr v-for="(result, index) in results" :key="result.id" :class="index % 2 === 0 ? 'bg-white' : 'bg-gray-50'">
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                  {{ result.id }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                  {{ result.title }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                  {{ result.assignee }}
                </td>
                <td v-if="result.completed" class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Done</td>
                <td v-else class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">In Progress</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                  <button @click="openTodo(result)" class="text-indigo-600 hover:text-indigo-900">Edit</button>
                  <button @click="deleteTodo(result.id)" class="px-6 text-red-600 hover:text-red-900">Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
  <EditModal v-if="openedTodo" :todo="openedTodo" />
</template>

<script>
import axios from 'axios';
import { ref } from 'vue';

import EditModal from '@/components/EditModal';

export default {
  async setup() {
    let openedTodo = ref(null);
    const { data: todos } = await axios.get('https://jsonplaceholder.typicode.com/todos');
    const { data: users } = await axios.get('https://jsonplaceholder.typicode.com/users');
    console.log(todos);
    console.log(users);

    const results = ref([]);

    async function mappingIds(todos) {
      await todos.map(todo => {
        results.value.push({
          // userIds start from 1 but we need to start from 0
          assignee: users[todo.userId - 1].name,
          ...todo
        });
      });
    }
    mappingIds(todos);
    console.log(results.value);

    function openTodo(result) {
      console.log('clicked');
      openedTodo.value = result;
      console.log(openedTodo.value);
    }

    function deleteTodo(id) {
      axios
        .delete(`https://jsonplaceholder.typicode.com/todos/${id}`)
        .then(() => {
          let z = results.value.map(results => results.id).indexOf(id);
          results.value.splice(z, 1);
        })
        .catch(error => {
          console.log(error);
        });
      console.log(id);
      console.log(todos);
    }

    return {
      results,
      openTodo,
      openedTodo,
      deleteTodo
    };
  },
  components: {
    EditModal
  }
};
</script>
