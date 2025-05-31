<script setup>
import PianoKeyboard from './components/PianoKeyboard.vue'
</script>

<template>
  <div class="app">
    <header>
      <h1>在线钢琴</h1>
      <p>使用键盘或 MIDI 设备来演奏</p>
    </header>
    
    <main>
      <PianoKeyboard />
    </main>

    <footer>
      <p>支持 MIDI 设备连接 | 多种乐器音色</p>
    </footer>
  </div>
</template>

<style>
.app {
  font-family: Arial, sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

header {
  margin-bottom: 40px;
}

h1 {
  color: #2c3e50;
  margin-bottom: 10px;
}

footer {
  margin-top: 40px;
  color: #666;
  font-size: 14px;
}
</style>
