# 在线钢琴（Online Piano）

一个基于 Vue 3 + Vite 的在线钢琴应用，支持键盘演奏、MIDI 设备连接和多种乐器音色。

## 功能特点

- 三个八度的钢琴键盘（C3-B5）
- 支持键盘按键演奏
- 支持鼠标点击演奏
- 支持 MIDI 设备连接
- 多种乐器音色（钢琴、吉他、竖琴）
- 实时视觉反馈

## 键盘映射

### 低八度 (C3-B3)：
- 白键：
  - Z -> C3
  - X -> D3
  - C -> E3
  - V -> F3
  - B -> G3
  - N -> A3
  - M -> B3
- 黑键：
  - A -> C#3
  - S -> D#3
  - F -> F#3
  - G -> G#3
  - H -> A#3

### 中央八度 (C4-B4)：
- 白键：
  - Q -> C4
  - W -> D4
  - E -> E4
  - R -> F4
  - T -> G4
  - Y -> A4
  - U -> B4
- 黑键：
  - 1 -> C#4
  - 2 -> D#4
  - 4 -> F#4
  - 5 -> G#4
  - 6 -> A#4

### 高八度 (C5-E5)：
- 白键：
  - I -> C5
  - O -> D5
  - P -> E5
- 黑键：
  - 8 -> C#5
  - 9 -> D#5

## 在线演示

访问 [https://midikang.github.io/panio/](https://midikang.github.io/panio/) 体验在线演示。

## 本地开发

1. 克隆仓库：
   ```bash
   git clone https://github.com/midikang/panio.git
   cd panio
   ```

2. 安装依赖：
   ```bash
   npm install
   ```

3. 启动开发服务器：
   ```bash
   npm run dev
   ```

4. 构建生产版本：
   ```bash
   npm run build
   ```

## 技术栈

- Vue 3
- Vite
- WebMIDI API
- Soundfont Player

## 许可证

MIT
