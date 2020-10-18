<template>
  <div>
    <div class="container">
      <b-button block variant="outline-primary" @click="handleCreate"
        >📝 <span class="font-weight-bold">Add To-Do</span></b-button
      >
      <div class="row">
        <div class="col-4 my-3" v-for="todo in todos" :key="todo.id">
          <b-card :title="todo.title">
            <b-card-text>
              {{ todo.description }}
            </b-card-text>
            <div class="row">
              <div class="col-6">
                <b-button variant="outline-success" @click="handleUpdate(todo)"
                  >Details</b-button
                >
              </div>
              <div class="col-6">
                <b-button variant="outline-danger" @click="deleteTodo(todo.id)"
                  >Delete</b-button
                >
              </div>
            </div>
          </b-card>
        </div>
      </div>
    </div>
    <!-- NOTE Modal -->
    <b-modal id="modal-todo" title="✔ Todo's">
      <b-form>
        <b-form-group label="✏ Title">
          <b-form-input v-model="form.title" placeholder="Todo's Title" />
        </b-form-group>

        <b-form-group label="📰 Description">
          <b-form-textarea
            id="textarea"
            v-model="form.description"
            placeholder="Todo's Description"
            rows="3"
            max-rows="3"
          ></b-form-textarea>
        </b-form-group>
      </b-form>
      <template v-slot:modal-footer>
        <b-button variant="primary" @click="updateTodo" v-if="form.id">
          Update
        </b-button>
        <b-button v-else @click="createTodo">Add Todo</b-button>
      </template>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      todos: [],
      form: {
        title: "",
        description: "",
      },
    };
  },
  mounted() {
    this.getTodo();
  },
  methods: {
    getTodo() {
      this.$http.get("/todo").then((res) => {
        this.todos = res.data;
      });
    },
    createTodo() {
      this.$http.post("/todo", this.form).then(() => {
        this.form = {
          title: "",
          description: "",
        };
        this.$bvModal.hide("modal-todo");
        this.getTodo();
      });
    },
    updateTodo() {
      this.$http.patch(`todo/${this.form.id}`, this.form).then(() => {
        this.form = {
          title: "",
          description: "",
        };
        this.$bvModal.hide("modal-todo");
        this.getTodo();
      });
    },
    deleteTodo(id) {
      this.$http.delete(`todo/${id}`).then(() => {
        this.form = {
          title: "",
          description: "",
        };
        this.$bvModal.hide("modal-todo");
        this.getTodo();
      });
    },
    handleCreate() {
      this.$bvModal.show("modal-todo");
    },
    handleUpdate(todo) {
      this.form = todo;
      this.$bvModal.show("modal-todo");
    },
  },
};
</script>
