<script>
import VueFeather from "vue-feather";
export default {
  components: {
    VueFeather,
  },
  data() {
    return {
      isCreating: false,
      vendoItensFeitos: false,
      todos: [],
      novoValor: "",
      textoTodoBkp: "",
      todosFeitos: [],
    };
  },
  mounted() {
    this.getTodosLocalStorage();
  },
  watch: {
    todos: {
      deep: true,
      handler() {
        this.salvarLocalStorage();
      },
    },
  },
  methods: {
    salvarNovoItem() {
      if (!!this.novoValor) {
        const obj = { isDone: false, text: this.novoValor, isEditing: false };
        this.todos.splice(0, 0, obj);
      }
    },
    filtraItems() {
      const itemsFiltrados = this.todos.filter((todo) => todo.isDone === true);
      this.todosFeitos = itemsFiltrados;
    },
    excluirItem(index) {
      this.todos.splice(index, 1);
    },
    editarItem(index) {
      this.textoTodoBkp = this.todos[index].text;
      this.todos.forEach((item, pos) => {
        item.isEditing = pos === index;
      });
    },
    getTodosLocalStorage() {
      let todos = localStorage.getItem("todos");
      if (todos) {
        this.todos = JSON.parse(todos);
      }
    },
    salvarLocalStorage() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },
  },
};
</script>

<template>
  <div class="wrapTodo">
    <h3 class="tituloApp"> To-do List</h3>
    <div class="headerWrap">
      <span :class="{ textItens: true, seeFilter: vendoItensFeitos }" @click="vendoItensFeitos = !vendoItensFeitos">
        Itens realizados: <strong>{{ todosFeitos.length }}</strong>
      </span>
      <button :style="{
        background: isCreating ? '#E57373' : '#5212aa',
      }" class="buttonStyle" @click="isCreating = !isCreating">
        {{ isCreating ? "Cancelar" : "Criar novo item" }}
      </button>
    </div>

    <div v-if="isCreating" class="todoItem">
      <input v-model="novoValor" type="text" class="inputStyle" placeholder="Insira seu novo item"
        @keypress.enter="salvarNovoItem" />
      <button class="buttonStyle" @click="salvarNovoItem">Salvar</button>
    </div>
    <div v-for="(todo, index) in vendoItensFeitos ? todosFeitos : todos" :key="index">
      <div v-if="!todo.isDone || vendoItensFeitos" class="todoItem">
        <div class="infoTodo">
          <input v-model="todo.isDone" type="checkbox" class="checkboxStyle" @change="filtraItems" />
        </div>
        <span v-if="!todo.isEditing" :class="{
            textStyle: true,
            markedDone: todo.isDone,
          }">
          {{ todo.text }}
        </span>
        <input v-else v-model="todo.text" type="text" class="inputStyle" :style="{ marginLeft: '12px' }"
          placeholder="Editando item" />
        <div v-if="!vendoItensFeitos">
          <div v-if="!todo.isEditing">
            <button class="buttonIcon" @click="editarItem(index)">
              <VueFeather type="edit" />
            </button>
            <button class="buttonIcon" @click="excluirItem(index)">
              <VueFeather type="trash" />
            </button>
          </div>
          <div v-else>
            <button class="buttonIcon" :style="{ background: '#4BB543' }" @click="todo.isEditing = false">
              <VueFeather type="check" />
            </button>
            <button class="buttonIcon" :style="{ background: '#DC3545' }"
              @click="(todo.text = textoTodoBkp), (todo.isEditing = false)">
              <VueFeather type="x" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
html {
  overflow-x: hidden;
}

.wrapTodo {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.tituloApp {
  color: white;
}

.todoItem {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #1b1b1b;
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 8px;
  width: 400px;
  gap: 10px;
}

.checkboxStyle {
  width: 30px;
  height: 30px;
}

.textStyle {
  color: white;
  font-size: 28px;
  margin-left: 16px;
  white-space: break-spaces;
  word-break: break-all;
}

.buttonStyle {
  padding: 12px;
  color: white;
  font-size: 18px;
  font-weight: bold;
  background: #5212aa;
  border-radius: 10px;
  border: none;
  cursor: pointer;
}

.buttonStyle:hover {
  background: #4b0ba4;
}

.inputStyle {
  padding: 12px;
  outline: none;
  border-radius: 8px;
  border: none;
  font-size: 16px;
  width: 100%;
}

.markedDone {
  text-decoration: line-through;
  color: #777;
}

.headerWrap {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
  width: 430px;
}

.textItens {
  padding: 8px 12px;
  background: transparent;
  border-radius: 20px;
  color: white;
  border: 2px solid #1dad06;
  cursor: pointer;
  user-select: none;
}

.seeFilter {
  background: #1dad06;
}

.textItens:hover {
  background: #499b3c;
}

.infoTodo {
  display: flex;
  align-items: center;
}

.buttonIcon {
  color: white;
  background: #222;
  border: none;
  margin: 4px;
  border-radius: 8px;
  padding: 8px;
  cursor: pointer;
}

.buttonIcon:hover {
  background: #2b2b2b;
}

@media screen and (max-width: 450px) {
  .todoItem {
    width: 300px;
  }

  .headerWrap {
    width: 300px;
  }
}

@media screen and (max-width: 321px) {
  .todoItem {
    width: 260px;
  }

  .headerWrap {
    width: 260px;
  }
}
</style>