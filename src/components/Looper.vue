// TODO: Tone.Transport.bpm.value = val
// Give notes a changeable duration
// Fix autoplay bug
<template>
  <!-- eslint-disable vue/require-v-for-key -->
  <div class='looper'>
    <h1 id='title'>{{ msg }}</h1>
    <button v-if='!playing' @click='play'>START</button>
    <button v-else @click='stop'>STOP</button>
    <br>
    <div id='loop-container'>
        <div id='instrument-tabs-container'>
            <div class='instrument-tab' v-for='instrument in instruments' @click='selectInstrument(instrument)' :class='{ "active-instrument-tab": instrument.isActive }'>
                {{instrument.name}}
            </div>
        </div>
        <div id='grid-container'>
          <input id='slider' type='range' min='1' max='9' step='1'>
          <div id='grid' v-for='(instrument, instrumentIndex) in instruments' v-show='instrument.isActive'>
            <div class='measure' v-for='m in instruments[instrumentIndex].grid.measures'>
              <div class='col' v-for='col in m'>
                <!--<div class='pointer'></div>-->
                <Cell class='grid-cell' v-for='(cell, cellIndex) in col' @toggle-activated='toggleActivated(cell)' :key='cellIndex'></Cell>
                </div>
              </div>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-multi-spaces */
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
      grid: {},
      numMeasures: 6,
      numCols: 4,
      numRows: 5,
      instruments: [{name: 'Instrument 1', synthType: 'mono', part: {}, isActive: true, grid: {}}, {name: 'Instrument 2', synthType: 'membrane', part: {}, isActive: false, grid: {}}, {name: 'Instrument 3', synthType: 'membrane', part: {}, isActive: false, grid: {}}]
    }
  },
  created () {
    Tone.Transport.bpm.value = 300

    for (let i = 0; i < this.instruments.length; i++) {
      // Initialize grid
      this.instruments[i].grid.measures = []
      for (let m = 0; m < this.numMeasures; m++) {
        this.instruments[i].grid.measures.push([])
        for (let col = 0; col < this.numCols; col++) {
          this.instruments[i].grid.measures[m].push([])
          // Assign notes
          let time = m + ':' + col
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'B4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'A#4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'A4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'G#4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'G4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'F#4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'F4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'E4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'D#4', time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'D4',  time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'C#4', time: time, instrument: i})
          this.instruments[i].grid.measures[m][col].push({isActive: false, note: 'C4',  time: time, instrument: i})
        }
      }

      // Setup part
      let synth = this.getSynth(this.instruments[i].synthType)
      this.instruments[i].part = new Tone.Part((time, event) => {
        synth.triggerAttackRelease(event.note, event.dur, time)
        Tone.Draw.schedule(() => {

        }, time)
      }, []).start('0')
      this.instruments[i].part.loop = true
      let end = this.numMeasures + ':0:0'
      this.instruments[i].part.loopEnd = end
    }
  },
  methods: {
    getSynth (type) {
      switch (type) {
        case 'mono':
          return new Tone.MonoSynth().toMaster()
        case 'membrane':
          return new Tone.MembraneSynth().toMaster()
        default:
          console.log('Unknown synth name')
      }
    },
    selectInstrument (instrument) {
      for (let i of this.instruments) {
        i.isActive = false
      }
      instrument.isActive = true
    },
    toggleActivated (cell) {
      if (!cell.isActive) {
        cell.isActive = true
        this.instruments[cell.instrument].part.at(cell.time, {time: cell.time, note: cell.note, dur: '4n'})
      } else {
        cell.isActive = false
        this.instruments[cell.instrument].part.remove(cell.time)
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
* {
    margin: 0;
    padding: 0;
}
#grid-container {
    padding: .3vw;
    //width: 30vw;
    background: lightgreen;
    flex: 3;
}

#slider {
    width: 100%;
}

#grid {
  width: 100%;
  display: flex;
  flex-direction: row;
}
.cell.grid-cell {
    border: 1px solid black;
    border-radius: 1px;
    margin: .3vw;
    flex: 1;
}

.cell.grid-cell:before {
    content: '';
    display: block;
    padding-top: 100%;
    float: left;
}
.measure {
  display: flex;
  flex-direction: row;
  flex: 1;
}
.col {
  display: flex;
  flex-direction: column;
  flex: 1;
  //justify-content: space-around;
}

#instrument-tabs-container {
    flex: 1;
    background: orange;
    display: flex;
    flex-direction: column;
}

.instrument-tab {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
}

.active-instrument-tab {
    background: darkorange;
}

#loop-container {
    display: flex;
    flex-direction: row;
    width: 80vw;
}

#title {
  background:  yellow;
  text-align: center;
}
</style>
