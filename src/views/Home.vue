<template>
  <div class="home">
    <b-row>
      <!-- 左側ここから -->
      <b-col sm="4" xs="12">
        <!-- フォームここから -->
        <b-form class="mt-3 ml-2 mr-2">
          <b-form-group>
            <b-form-input v-model="textInput" id="task" type="text" placeholder="やることを入力">
            </b-form-input>
          </b-form-group>
          <!-- ボタンここから -->
          <div class="text-right">
            <b-button type="button" variant="outline-info" class="mr-2" @click="addButton">登録</b-button>
            <b-button type="reset" variant="outline-danger">クリア</b-button>
          </div>
          <!-- ボタンここまで -->
        </b-form>
        <!-- フォームここまで -->
      </b-col>
      <!-- 左側ここまで -->
      <!-- 右側ここから -->
      <b-col sm="8" xs="12">
        <b-list-group flush>
          <b-list-group-item v-for="task in tasks" :key="task.id">
            <div class="d-flex justify-content-between">
              <div class="text-left">
                <!-- チェックボックスここから -->
                <b-form-checkbox
                  :id="'checkbox'+ task.id"
                  :class="{ done: task.check }"
                  v-model="task.check"
                  name="checkbox"
                  class="position-static">
                  {{ task.title }}
                </b-form-checkbox>
                <!-- チェックボックスここまで -->
              </div>
              <div class="text-right">
                <b-badge pill v-if="task.check === false" class="task-label mr-2">
                  まだ
                </b-badge>
                <b-badge pill v-if="task.check=== true" variant="success" class="task-label mr-2">
                  完了
                </b-badge>
                <font-awesome-icon icon="minus-circle" @click="showModal(task)" class="text-danger delete-button" />
                <v-dialog />
              </div>
            </div>
          </b-list-group-item>
        </b-list-group>
      </b-col>
      <!-- 右側ここまで -->
    </b-row>
  </div>
</template>

<script>
export default {
  data () {
    return {
      textInput: '',
      tasks: []
    }
  },
  methods: {
    addButton () {
      localStorage.uid = this.maxId
      localStorage.uid++
      let newTask = {
        id: Number(localStorage.uid),
        title: this.textInput,
        check: false
      }
      this.tasks.push(newTask)
      this.textInput = ''
    },
    deleteTask (task) {
      let targetTaskIndex = this.tasks.indexOf(task)
      this.tasks.splice(targetTaskIndex, 1)
    },
    showModal (task) {
      this.$modal.show('dialog', {
        title: '削除してもよろしいですか？',
        text: 'タスク名：' + task.title,
        buttons: [
          { title: 'OK',
            handler: () => {
              this.deleteTask(task)
              this.$modal.hide('dialog')
          } },
          { title: 'やめる'}
        ]
      });
    }
  },
  computed: {
    maxId () {
      // タスクが1件もない場合、0を返す
      if (this.tasks.length === 0) return 0

      let allTasksId = this.tasks.map(task => {
        return task.id
      })
      return Math.max.apply(null, allTasksId)
    },
    STORAGE_KEY () {
      return 'yarukoto-list'
    }
  },
  watch: {
    tasks: {
      handler (tasks) {
        localStorage.setItem(this.STORAGE_KEY, JSON.stringify(tasks))
      },
      deep: true
    }
  },
  mounted () {
    this.tasks = JSON.parse(localStorage.getItem(this.STORAGE_KEY) || '[]')
  }
}
</script>

<style scoped>
.task-label {
  width: 50px
}

.done {
  color: #ccc;
}

.delete-button:hover {
  opacity: 0.6;
  transition-duration: 0.3s;
}
</style>
