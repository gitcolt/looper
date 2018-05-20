//TODO: Tone.Transport.bpm.value = val
<template>
  <div class='looper'>
    <h1 id='title'>{{ msg }}</h1>
    <button v-if='!playing' @click='play'>START</button>
    <button v-else @click='stop'>STOP</button>
    <div id='grid'>
      <!-- eslint-disable-next-line vue/no-unused-vars vue/require-v-for-key -->
      <div class='measure' v-for='m in grid.measures'>
        <!-- eslint-disable-next-line vue/no-unused-vars vue/require-v-for-key -->
        <div class='col' v-for='col in m'>
          <div class='pointer'></div>
          <Cell v-for='(cell, index) in col' @toggle-activated='toggleActivated(cell)' :key='index'></Cell>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Tone from 'tone'
import Cell from './Cell'

export default {
  name: 'Looper',
  components: {
    Cell
  },
  data () {
    return {
      playing: false,
      msg: 'Looper',
      notes: [0, 0, 0, 0],
      grid: {},
      numMeasures: 4,
      numCols: 3,
      numRows: 5
    }
  },
  created () {
    this.grid.measures = []
    for (let m = 0; m < this.numMeasures; m++) {
      this.grid.measures.push([])
      for (let col = 0; col < this.numCols; col++) {
        this.grid.measures[m].push([])
        for (let row = 0; row < this.numRows; row++) {
          let cell = {isActive: false}
          this.grid.measures[m][col].push(cell)
        }
      }
    }

    let synth = new Tone.MonoSynth().toMaster()

    let part = new Tone.Part((time, note) => {
      synth.triggerAttackRelease(note, '8n', time)
    }, [['0:0:0', 'C3'], ['0:0:4', 'D3'], ['0:0:8', 'E3'], ['0:0:12', 'G3']]).start('0')
    part.loop = true
    part.loopEnd = '1:0:0'
    /*
    let loop = new Tone.Loop((time) => {
      synth.triggerAttackRelease('C1', '8n', time)
    }, '16n')
    loop.start(0)
    */
  },
  methods: {
    toggleActivated (cell) {
      cell.isActive = !cell.isActive
    },
    play () {
      Tone.Transport.start()
      this.playing = true
      // Tone.Transport.toggle()
    },
    stop () {
      Tone.Transport.stop()
      this.playing = false
    },
    asdf () {
      console.log('asdffdfd')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#grid {
  width: 100%;
  display: flex;
  flex-direction: row;
}
.measure {
    display: flex;
    flex-direction: row;
}
.col {
  display: flex;
  flex-direction: column;
}
.pointer {
  width: 20px;
  height: 20px;
  background: blue;
  margin: 2px;
  border: 1px solid black;
}
#title {
  background:  yellow;
  text-align: center;
}
</style>
