<template>
  <div id="app">
    <div class="cols">
      <div class="col">
        <div class="field shadow-lg">
          <div class="bone"
               v-for="bone in bones"
               :data-id="bone.id"
               @click="move"
               :key="bones.id"
               :class="{empty : bone.title === ''}"
          >{{bone.title}}</div>
        </div>
      </div>
      <div class="col">
        <h2 class="steps">Steps: {{clicks}}</h2>
        <b-button @click="mix()">new game</b-button>
        <ul class="log-list">
          <li v-for="item in log.slice().reverse()">{{item}}</li>
        </ul>

      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      clicks: 0,
      bones: [
        {id: 0, title: 1, x: 1, y: 1},
        {id: 1, title: 2, x: 2, y: 1},
        {id: 2, title: 3, x: 3, y: 1},
        {id: 3, title: 4, x: 4, y: 1},
        {id: 4, title: 5, x: 1, y: 2},
        {id: 5, title: 6, x: 2, y: 2},
        {id: 6, title: 7, x: 3, y: 2},
        {id: 7, title: 8, x: 4, y: 2},
        {id: 8, title: 9, x: 1, y: 3},
        {id: 9, title: 10, x: 2, y: 3},
        {id: 10, title: 11, x: 3, y: 3},
        {id: 11, title: 12, x: 4, y: 3},
        {id: 12, title: 13, x: 1, y: 4},
        {id: 13, title: 14, x: 2, y: 4},
        {id: 14, title: 15, x: 3, y: 4},
        {id: 15, title: '', x: 4, y: 4},
      ],
      completeBones: [
        {title: 1},
        {title: 2},
        {title: 3},
        {title: 4},
        {title: 5},
        {title: 6},
        {title: 7},
        {title: 8},
        {title: 9},
        {title: 10},
        {title: 11},
        {title: 12},
        {title: 13},
        {title: 14},
        {title: 15},
        {title: ''},

      ],
      log: []

    }
  },
  methods:{
    victory() {
      let res = true
      this.bones.forEach((i, idx) => {
        if (!(i.title === this.completeBones[idx].title)) {
          res = false
        }
      })
      if(res) {
        this.showMsgBoxOne()
        this.clicks = 0
        this.log = []
      }
    },
    getRandomBool() {
      return Math.floor(Math.random() * 2) === 0
    },
    mix() {
      for (let i = 0; i < 300; i++) {
        let emptyBone = this.getEmptyBone
        let clickedId = null
        let candidate = null
        if (this.getRandomBool()) {
          if (this.getRandomBool()) {
            candidate = this.bones.find(i => i.x === emptyBone.x && (i.y + 1 === emptyBone.y))
          } else {
            candidate = this.bones.find(i => i.x === emptyBone.x && (i.y - 1 === emptyBone.y))
          }
        } else {
          if (this.getRandomBool()) {
            candidate = this.bones.find(i => i.y === emptyBone.y && (i.x + 1 === emptyBone.x))
          } else {
            candidate = this.bones.find(i => i.y === emptyBone.y && (i.x - 1 === emptyBone.x))
          }
        }

        if (candidate) {
          clickedId = candidate.id
          this.move(clickedId)
        }
      }
      this.clicks = 0
      this.log = []
    },
    getClickedBone(clickedId) {
      let clicked = {
        id: null,
        title: null,
        x: null,
        y: null
      }
      this.bones.forEach(i => {
        if (i.id == clickedId) {
          clicked.id = i.id
          clicked.title = i.title
          clicked.x = i.x
          clicked.y = i.y
        }
      })
      return clicked
    },
    move(e) {
      let clickedId = null
      if (e.target) {
        clickedId = e.target.dataset.id
      } else {
        clickedId = e
      }
      let clicked = this.getClickedBone(clickedId)
      let emptyBone = this.getEmptyBone
      if ((clicked.x === emptyBone.x && (clicked.y + 1 === emptyBone.y || clicked.y - 1 === emptyBone.y)) || (clicked.y === emptyBone.y && (clicked.x + 1 === emptyBone.x || clicked.x - 1 === emptyBone.x))) {
        this.bones.map(i => {
          if (i.id === clicked.id) {
            i.title = emptyBone.title
            return i
          } else {
            return i
          }
        })
        this.bones.map(i => {
          if (i.id === emptyBone.id ) {
            i.title = clicked.title
            return i
          } else {
            return i
          }
        })
        this.clicks++
        if (e.target) {
          this.log.push(`bone ${clicked.title} from x:${clicked.x}, y:${clicked.y} to x:${emptyBone.x}, y:${emptyBone.y}`)
          this.victory()
        }
      }
    },
    showMsgBoxOne() {
      this.$bvModal.msgBoxConfirm(`You win in ${this.clicks} steps! Mix bones for new game?`)
              .then(value => {
                value ? this.mix() : false
              })
              .catch(err => {
                // An error occurred
                console.log(err)
              })
    }
  },
  computed: {
    getEmptyBone() {
      let emptyBone = {
        id: null,
        title: null,
        x: null,
        y: null
      }
      this.bones.forEach(i => {
        if (i.title === '') {
          emptyBone.id = i.id
          emptyBone.title = i.title
          emptyBone.x = i.x
          emptyBone.y = i.y
        }
      })
      return emptyBone
    },
  }

}
</script>

<style lang="sass">
  @import '../node_modules/bootstrap/dist/css/bootstrap.css'
  @import '../node_modules/bootstrap-vue/dist/bootstrap-vue.css'
  *
    box-sizing: border-box
  .cols
    display: flex
    padding: 50px
    width: 800px
    margin: 0 auto
  .col
    width: 400px
    display: flex
    flex-direction: column
    align-items: flex-start
    &+.col
      margin-left: 30px
  .field
    width: 400px
    height: 400px
    background-color: black
    border: 2px solid black
    border-radius: 5px
    display: flex
    flex-wrap: wrap
  .bone
    width: 25%
    height: 25%
    background-color: #ffa255
    border: 2px solid black
    display: flex
    align-items: center
    justify-content: center
    font-size: 40px
    border-radius: 5px
    cursor: pointer
    &.empty
      background-color: black
      cursor: unset
  .log-list
    width: 400px
    list-style: none
    padding: 0
    margin-top: auto
    margin-bottom: 0
    height: 300px
    overflow: auto
</style>
