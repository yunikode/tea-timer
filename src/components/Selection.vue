<template lang="html">



  <div class="container has-text-centered" id="selection" v-show="displayList">
    <h1 class="title">Selection</h1>
    <div class="card " v-for="type in types">
      <div class="columns is-gapless is-mobile">
        <div class="column is-two-thirds-mobile">
          <div class="card-content">
            <div class="content" @click="setTime(type)">
              <p class="title is-4 has-text-dark">{{type.name}}</p>
              <p class="subtitle is-4 has-text-dark">{{type.time}} minutes</p>
            </div>
          </div>
        </div>
        <div class="column is-one-third-mobile is-one-quarter-tablet">
          <div class="card-content">
            <div class="content has-text-centered">
              <div class="buttons">
                <button class="button is-primary" @click="startEditing(type)">
                  <span>Edit</span>
                  <span class="icon is-small">
                    <i class="fas fa-edit"></i>
                  </span>
                </button>
                <button class="button is-danger is-outlined" @click="deleteType(type)">
                  <span>Delete</span>
                  <span class="icon is-small">
                    <i class="fas fa-times"></i>
                  </span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div>
    <div class="card">
      <div class="card-content" @click="addNewTea">
      <p class="title is-2 has-text-dark">add new type</p>
    </div>
    </div>
    <div class="modal is-active" v-if="showModal" @keyup.esc="cancelEditing">
      <div class="modal-background"></div>
      <div class="modal-card">
        <header>
          <div class="modal-card-head">
            <p class="modal-card-title">{{editName}}</p>
          </div>
        </header>
        <section class="modal-card-body">
          <div class="field is-horizontal">
            <div class="field-label is-normal">
              <label class="label">Name of the tea</label>
            </div>
            <div class="field-body">
              <div class="field">
                <p class="control">
                  <input
                  type="text"
                  class="input is-medium"
                  name="name"
                  v-model="editName"
                  @keyup.esc="cancelEditing"
                >
                </p>
              </div>
            </div>
          </div>
          <div class="field is-horizontal">
            <div class="field-label is-normal">
              <label class="label">Steeping time</label>
            </div>
            <div class="field-body">
              <div class="field">
                <p class="control">
                  <input
                  type="text"
                  class="input is-medium"
                  name="time"
                  v-model="editTime"
                  @keyup.esc="cancelEditing"
                  >
                </p>
              </div>
            </div>
          </div>

<button class="button is-large" aria-label="close" @click="finishEditing">Save</button>
        </section>
      </div>
      <button class="modal-close is-large" aria-label="close" @click="cancelEditing"></button>
    </div>
  </div>

</template>

<script>
import {
  eventBus
} from '../main'
export default {
  name: 'Selection',
  created() {
    eventBus.$on('showList', () => {
      this.displayList = true
      document.getElementById('selection').classList.toggle('visible')
    })
  },
  mounted() {
    if (localStorage.getItem('types')) this.types = JSON.parse(localStorage.getItem('types'))
  },
  data() {
    return {
      editing: null,
      showModal: false,
      types: [{
          name: 'green',
          time: 3
        },
        {
          name: 'black',
          time: 5
        },
        {
          name: 'black, second flush',
          time: 7
        },
        {
          name: 'gunpowder',
          time: 5
        }
      ],
      time: null,
      teatime: null,
      displayList: false,
      editName: null,
      editTime: null,
      editType: null
    }
  },
  methods: {
    setTime: function(type) {
      document.getElementById('selection').classList.toggle('visible')
      const tea = type
      eventBus.$emit('showTimer', tea)
      this.displayList = false
    },
    addNewTea: function() {
      this.types.push({
        name: 'new',
        time: 42
      })
    },
    deleteType: function(type) {
      const index = this.types.indexOf(type)
      this.types.splice(index, 1)
    },
    startEditing: function(type) {
      this.editName = type.name
      this.editTime = type.time
      this.editType = type
      this.showModal = true
    },
    cancelEditing: function() {
      this.showModal = false
      this.cleanUp()
    },
    finishEditing: function(event) {
      this.showModal = false

      const index = this.types.indexOf(this.editType)
      this.types[index].name = this.editName
      this.types[index].time = this.editTime

      this.cleanUp()
      localStorage.setItem('types', JSON.stringify(this.types))
    },
    cleanUp: function() {
      this.editName = null
      this.editTime = null
      this.editType = null
    }
  }
}
</script>

<style lang="css">
@-webkit-keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
@-moz-keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
@keyframes fadeIn { from { opacity:0; } to { opacity:1; } }

.visible {
  opacity:0;  /* make things invisible upon start */
  -webkit-animation:fadeIn ease-in 1;  /* call our keyframe named fadeIn, use animattion ease-in and repeat it only 1 time */
  -moz-animation:fadeIn ease-in 1;
  animation:fadeIn ease-in 1;

  -webkit-animation-fill-mode:forwards;  /* this makes sure that after animation is done we remain at the last keyframe value (opacity: 1)*/
  -moz-animation-fill-mode:forwards;
  animation-fill-mode:forwards;

  -webkit-animation-duration:0.5s;
  -moz-animation-duration:0.5s;
  animation-duration:0.5s;
}
</style>
