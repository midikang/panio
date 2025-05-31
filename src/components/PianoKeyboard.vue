<!-- 钢琴键盘组件 -->
<template>
  <div class="piano-container">
    <div class="controls">
      <button @click="initMIDI">{{ midiEnabled ? '已连接 MIDI' : '连接 MIDI 设备' }}</button>
      <select v-model="currentInstrument">
        <option value="acoustic_grand_piano">钢琴</option>
        <option value="acoustic_guitar_nylon">吉他</option>
        <option value="orchestral_harp">竖琴</option>
      </select>
    </div>
    
    <div class="piano-keyboard">
      <!-- 通过循环渲染钢琴键 -->
      <!-- 在每个琴键上添加了 @mouseup 和 @mouseleave 事件，这样当鼠标松开或离开琴键时都会触发 stopNote-->
      <div v-for="note in notes" 
           :key="note.midi" 
           :class="['piano-key', note.type, { active: activeNotes.includes(note.midi) }]"
           @mousedown="playNote(note.midi)"
           @mouseup="stopNote(note.midi)"
           @mouseleave="stopNote(note.midi)"
           :style="{ left: `${note.position}px` }">
        <span class="note-name">{{ note.name }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import { WebMidi } from 'webmidi'
import Soundfont from 'soundfont-player'

export default {
  name: 'PianoKeyboard',  data() {
    return {
      midiEnabled: false,
      activeNotes: [],
      currentInstrument: 'acoustic_grand_piano',
      player: null,      pressedKeys: new Set(),
      keyboardMap: {
        // 低八度 (C3 - B3)
        'z': 48, // C3
        'x': 50, // D3
        'c': 52, // E3
        'v': 53, // F3
        'b': 55, // G3
        'n': 57, // A3
        'm': 59, // B3
        // 黑键 (低八度)
        'a': 49, // C#3
        's': 51, // D#3
        'f': 54, // F#3
        'g': 56, // G#3
        'h': 58, // A#3
        // 中央八度 (C4 - B4)
        'q': 60, // C4
        'w': 62, // D4
        'e': 64, // E4
        'r': 65, // F4
        't': 67, // G4
        'y': 69, // A4
        'u': 71, // B4
        // 黑键 (中央八度)
        '1': 61, // C#4
        '2': 63, // D#4
        '4': 66, // F#4
        '5': 68, // G#4
        '6': 70, // A#4
        // 高八度 (C5-E5)
        'i': 72, // C5
        'o': 74, // D5
        'p': 76, // E5
        // 黑键 (高八度)
        '8': 73, // C#5
        '9': 75, // D#5
      },// 定义钢琴键的音符信息
      notes: [
        // 低八度的音符 (C3 - B3)
        { midi: 48, name: 'C3', type: 'white', position: 0 },
        { midi: 49, name: 'C#3', type: 'black', position: 35 },
        { midi: 50, name: 'D3', type: 'white', position: 50 },
        { midi: 51, name: 'D#3', type: 'black', position: 85 },
        { midi: 52, name: 'E3', type: 'white', position: 100 },
        { midi: 53, name: 'F3', type: 'white', position: 150 },
        { midi: 54, name: 'F#3', type: 'black', position: 185 },
        { midi: 55, name: 'G3', type: 'white', position: 200 },
        { midi: 56, name: 'G#3', type: 'black', position: 235 },
        { midi: 57, name: 'A3', type: 'white', position: 250 },
        { midi: 58, name: 'A#3', type: 'black', position: 285 },        { midi: 59, name: 'B3', type: 'white', position: 300 },
        // 中央八度的音符 (C4 - B4)
        { midi: 60, name: 'C4', type: 'white', position: 350 },
        { midi: 61, name: 'C#4', type: 'black', position: 385 },
        { midi: 62, name: 'D4', type: 'white', position: 400 },
        { midi: 63, name: 'D#4', type: 'black', position: 435 },
        { midi: 64, name: 'E4', type: 'white', position: 450 },
        { midi: 65, name: 'F4', type: 'white', position: 500 },
        { midi: 66, name: 'F#4', type: 'black', position: 535 },
        { midi: 67, name: 'G4', type: 'white', position: 550 },
        { midi: 68, name: 'G#4', type: 'black', position: 585 },
        { midi: 69, name: 'A4', type: 'white', position: 600 },
        { midi: 70, name: 'A#4', type: 'black', position: 635 },
        { midi: 71, name: 'B4', type: 'white', position: 650 },        // 高八度的音符
        { midi: 72, name: 'C5', type: 'white', position: 700 },
        { midi: 73, name: 'C#5', type: 'black', position: 735 },
        { midi: 74, name: 'D5', type: 'white', position: 750 },
        { midi: 75, name: 'D#5', type: 'black', position: 785 },
        { midi: 76, name: 'E5', type: 'white', position: 800 },
        { midi: 77, name: 'F5', type: 'white', position: 850 },
        { midi: 78, name: 'F#5', type: 'black', position: 885 },
        { midi: 79, name: 'G5', type: 'white', position: 900 },
        { midi: 80, name: 'G#5', type: 'black', position: 935 },
        { midi: 81, name: 'A5', type: 'white', position: 950 },
        { midi: 82, name: 'A#5', type: 'black', position: 985 },
        { midi: 83, name: 'B5', type: 'white', position: 1000 }
      ]
    }
  },
  mounted() {
    // 初始化音频播放器
    this.initAudioPlayer()
    // 添加键盘事件监听
    window.addEventListener('keydown', this.handleKeyDown)
    window.addEventListener('keyup', this.handleKeyUp)
  },
  beforeDestroy() {
    // 移除键盘事件监听
    window.removeEventListener('keydown', this.handleKeyDown)
    window.removeEventListener('keyup', this.handleKeyUp)
  },
  methods: {
    async initAudioPlayer() {
      // 初始化 Soundfont 播放器
      const audioContext = new (window.AudioContext || window.webkitAudioContext)()
      this.player = await Soundfont.instrument(audioContext, this.currentInstrument)
    },
    async initMIDI() {
      try {
        // 启用 Web MIDI
        await WebMidi.enable()
        this.midiEnabled = true
        
        // 监听所有 MIDI 输入设备
        WebMidi.inputs.forEach(input => {
          // 监听音符按下事件
          input.addListener('noteon', e => {
            this.playNote(e.note.number)
          })
          // 监听音符释放事件
          input.addListener('noteoff', e => {
            this.stopNote(e.note.number)
          })
        })
      } catch (err) {
        console.error('MIDI 设备连接失败:', err)
      }
    },
    playNote(midiNumber) {
      // 添加到活动音符列表
      if (!this.activeNotes.includes(midiNumber)) {
        this.activeNotes.push(midiNumber)
      }
      // 使用 Soundfont 播放音符
      if (this.player) {
        this.player.play(midiNumber)
      }
    },
    stopNote(midiNumber) {
      // 从活动音符列表中移除
      this.activeNotes = this.activeNotes.filter(n => n !== midiNumber)
      // 停止音符播放
      if (this.player) {
        this.player.stop(midiNumber)
      }
    },
    handleKeyDown(event) {
      const key = event.key.toLowerCase()
      // 如果按键已经被按下，或者不在映射中，则忽略
      if (this.pressedKeys.has(key) || !this.keyboardMap[key]) {
        return
      }
      
      this.pressedKeys.add(key)
      this.playNote(this.keyboardMap[key])
    },
    handleKeyUp(event) {
      const key = event.key.toLowerCase()
      if (!this.keyboardMap[key]) {
        return
      }
      
      this.pressedKeys.delete(key)
      this.stopNote(this.keyboardMap[key])
    }
  },
  watch: {
    // 监听乐器变化
    async currentInstrument(newInstrument) {
      await this.initAudioPlayer()
    }
  }
}
</script>

<style scoped>
.piano-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.controls {
  margin-bottom: 20px;
}

.piano-keyboard {
  position: relative;
  height: 200px;  width: 1400px;
  background: #f0f0f0;
  border-radius: 8px;
  overflow: hidden;
  user-select: none; /* 防止拖动时选中文字 */
}

.piano-key {
  position: absolute;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding-bottom: 10px;
  transition: background-color 0.1s;
  cursor: pointer;
}

.white {
  width: 50px;
  height: 200px;
  background: white;
  border: 1px solid #ccc;
  z-index: 1;
}

.black {
  width: 30px;
  height: 120px;
  background: black;
  z-index: 2;
  color: white;
}

.piano-key.active {
  background-color: #e0e0e0;
}

.black.active {
  background-color: #333;
}

.note-name {
  font-size: 12px;
  color: #666;
}

.black .note-name {
  color: #fff;
}

button, select {
  margin: 5px;
  padding: 8px 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background: white;
  cursor: pointer;
}

button:hover {
  background: #f0f0f0;
}
</style>