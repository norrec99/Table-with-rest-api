<template>
  <nav class="border-t border-gray-200 px-4 flex items-center justify-between sm:px-0">
    <div class="-mt-px w-0 flex-1 flex">
      <a @click="goPreviousPage" class="border-t-2 border-transparent pt-4 pr-1 inline-flex items-center text-sm font-medium text-gray-500 hover:text-gray-700 hover:border-gray-300 cursor-pointer">
        <ArrowNarrowLeftIcon class="mr-3 h-5 w-5 text-gray-400" aria-hidden="true" />
        Previous
      </a>
    </div>
    <div class="hidden md:-mt-px md:flex">
      <a v-for="pageNumber in pageNumbers" :key="pageNumber" class="border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium cursor-pointer"> {{ pageNumber }} </a>

      <!-- Current: "border-indigo-500 text-indigo-600", Default: "border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300" -->
      <!-- <a href="#" class="border-indigo-500 text-indigo-600 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium" aria-current="page"> 2 </a>
      <a href="#" class="border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium"> 3 </a>
      <span class="border-transparent text-gray-500 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium"> ... </span>
      <a href="#" class="border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium"> 8 </a>
      <a href="#" class="border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium"> 9 </a>
      <a href="#" class="border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300 border-t-2 pt-4 px-4 inline-flex items-center text-sm font-medium"> 10 </a> -->
    </div>
    <div class="-mt-px w-0 flex-1 flex justify-end">
      <a @click="goNextPage" class="border-t-2 border-transparent pt-4 pl-1 inline-flex items-center text-sm font-medium text-gray-500 hover:text-gray-700 hover:border-gray-300 cursor-pointer">
        Next
        <ArrowNarrowRightIcon class="ml-3 h-5 w-5 text-gray-400" aria-hidden="true" />
      </a>
    </div>
  </nav>
</template>

<script>
import { ref } from 'vue';
import { ArrowNarrowLeftIcon, ArrowNarrowRightIcon } from '@heroicons/vue/solid';

export default {
  props: {
    todos: {
      type: Object,
      required: true
    }
  },
  emits: ['updateCurrentTodos', 'previousTodos'],
  setup(props, { emit }) {
    const pageNumbers = ref([]);
    const totalItem = ref(null);
    const maxItemPerPage = 10;

    // eslint-disable-next-line vue/no-setup-props-destructure
    const todoList = props.todos;
    totalItem.value = todoList.length;
    console.log(todoList);
    console.log('total page number ', totalItem.value);

    for (let i = 1; i <= totalItem.value / maxItemPerPage; i++) {
      pageNumbers.value.push(i);
    }
    console.log('page number array ', pageNumbers.value);

    function goNextPage() {
      emit('updateCurrentTodos');
    }
    function goPreviousPage() {
      emit('previousTodos');
    }

    return { goNextPage, goPreviousPage, pageNumbers };
  },
  components: {
    ArrowNarrowLeftIcon,
    ArrowNarrowRightIcon
  }
};
</script>
