<template>
  <div id="app">
    <div class="flex flex-row gap-2 m-3 p-4">
      <div class="block w-1/3">
        <label>Add a New Item.</label>
        <input
          type="text"
          placeholder="Make Breakfast"
          class="block px-3 py-2 rounded-sm shadow-sm w-full focus:outline-none border border-grey-500 focus:ring-blue-600 my-2"
          v-model="newItem.name"
          v-on:keyup.enter="submitForm()"
        />
        <p class="mt-2 text-sm text-gray-500">
          Type the ToDo item and press Enter to add to the list.
        </p>
      </div>
      <div class="block w-2/3">
        <div class="flex flex-col">
          <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
            <div
              class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8"
            >
              <div
                class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg"
              >
                <table class="min-w-full divide-y divide-gray-200">
                  <thead class="bg-gray-50">
                    <tr>
                      <th
                        scope="col"
                        class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                      ></th>
                      <th
                        scope="col"
                        class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                      >
                        Name
                      </th>
                      <th scope="col" class="relative px-6 py-3">
                        <span class="sr-only">Action</span>
                      </th>
                    </tr>
                  </thead>
                  <tbody class="bg-white divide-y divide-gray-200">
                    <tr
                      v-for="(item, index) in items"
                      :key="index"
                      :class="{ completed: item.completed }"
                    >
                      <td class="px-6 py-4 whitespace-nowrap">
                        <input v-if="!item.completed"
                          type="checkbox"
                          name="completed"
                          id="completed"
                          @click="markAsCompleted(item.id)"
                        />
                      </td>
                      <td class="px-6 py-4 whitespace-nowrap">
                        <div class="text-sm text-gray-900">
                          {{ item.name }}
                        </div>
                        <div class="text-sm text-gray-500">{{ item.date }}</div>
                      </td>
                      <td class="px-6 py-4 whitespace-nowrap text-red-600">
                        <div class="flex justify-around">
                          <button @click="deleteItem(item.id)">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              class="h-6 w-6"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke="currentColor"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
                              />
                            </svg>
                          </button>
                        </div>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
export default {
  name: "App",
  data() {
    return {
      items: [],
      newItem: {
        name: "",
        completed: false,
      },
    };
  },
  methods: {
    clearForm() {
      (this.newItem.name = ""), (this.newItem.completed = false);
    },
    submitForm() {
      axios.post("https://todo.test/api/items", this.newItem)
      .then(() => {
        this.fetchData()
      })
      this.clearForm();
    },
    markAsCompleted(id) {
      let a = this.items.filter(x => x.id = id)[0]
      a.completed = true,
      axios.put("https://todo.test/api/items/" + id, {
        name: a.name,
        completed: true
      }).then(() => {
        this.fetchData()
      })
    },
    deleteItem(id) {
      axios.delete("https://todo.test/api/items/" + id).then(() => {
        this.fetchData()
      })
    },
    fetchData() {
      axios.get("https://todo.test/api/items").then((res) => {
        const temp = res.data
        temp.map(x => {
          x.date = moment(x.created_at).fromNow();
        })
        this.items = temp
      });
    },
  },
  mounted() {
    this.fetchData()
    this.moment = moment()
  },
};
</script>

<style>
.completed {
  text-decoration: line-through;
  color: slategray;
}
</style>
