//TODO: Tone.Transport.bpm.value = val
// give notes a changeable duration
<template>
  <!-- eslint-disable vue/require-v-for-key -->
  <div class='looper'>
    <h1 id='title'>{{ msg }}</h1>
    <button v-if='!playing' @click='play'>START</button>
    <button v-else @click='stop'>STOP</button>
    <br>
    <input type='range' min='1' max='8' value='1'>
    <div id='grid'>
      <div class='measure' v-for='m in grid.measures'>
        <div class='col' v-for='col in m'>
          <div class='pointer'></div>
          <Cell v-for='(cell, index) in col' @toggle-activated='toggleActivated(cell)' :key='index'></Cell>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-multi-spaces */
import Tone from 'tone'
import Cell from './Cell'
let part

export default {
  name: 'Looper',
  components: {
    Cell
  },
  data () {
    return {
      playing: false,
      msg: 'Looper',
      grid: {},
      numMeasures: 2,
      numCols: 4,
      numRows: 5
    }
  },
  created () {
    this.grid.measures = []
    for (let m = 0; m < this.numMeasures; m++) {
      this.grid.measures.push([])
      for (let col = 0; col < this.numCols; col++) {
        this.grid.measures[m].push([])
        // Assign notes
        let time = m + ':' + col
        this.grid.measures[m][col].push({isActive: false, note: 'E4',  time: time})
        this.grid.measures[m][col].push({isActive: false, note: 'D#4', time: time})
        this.grid.measures[m][col].push({isActive: false, note: 'D4',  time: time})
        this.grid.measures[m][col].push({isActive: false, note: 'C#4', time: time})
        this.grid.measures[m][col].push({isActive: false, note: 'C4',  time: time})
        /*
        for (let row = 0; row < this.numRows; row++) {
          let cell = {isActive: false}
          this.grid.measures[m][col].push(cell)
        }
        */
      }
    }

    let synth = new Tone.MonoSynth().toMaster()

    part = new Tone.Part((time, event) => {
      synth.triggerAttackRelease(event.note, event.dur, time)
      Tone.Draw.schedule(() => {

      }, time)
    }, [{time: '0:0:0', note: 'C3', dur: '4n'}, {time: '0:0:8', note: 'E3', dur: '4n'}, {time: '0:0:12', note: 'G3', dur: '4n'}]).start('0')
    part.loop = true
    let end = this.numMeasures + ':0:0'
    part.loopEnd = end
  },
  methods: {
    toggleActivated (cell) {
      if (!cell.isActive) {
        cell.isActive = true
        part.at(cell.time, {time: cell.time, note: cell.note, dur: '4n'})
      } else {
        cell.isActive = false
        part.remove(cell.time)
      }
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
  //background: blue;
  margin: 2px;
  //border: 1px solid black;
}
.pointer-active {
  background: blue;
  border: 1px solid black;
}
#title {
  background:  yellow;
  text-align: center;
}
</style>
