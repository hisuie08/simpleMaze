<template>
    <main id="maze"></main>
</template>

<script>
class Maze {
  gameData = []
  rowStack = []
  colStack = []
  mazeSize = 0
  pointerRow = 0
  pointerCol = 0
  userRow = 0
  userCol = 0
  goalRow = 0
  goalCol = 0
  moveCount = 0

  pushIt =()=>{this.rowStack.push(this.pointerRow);this.colStack.push(this.pointerCol)}

  rnd = (max) => Math.floor(Math.random() * max)

  shuffle = ([...array]) => {
    for (let i = array.length - 1; i >= 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));[array[i], array[j]] = [array[j], array[i]]}
    return array
  }

  init(size) {
    this.mazeSize = size
    for (let i = 0; i < size; i++) {
      this.gameData[i] = []
      for (let j = 0; j < size; j++) {
        this.gameData[i][j] = true
      }
    }
    this.pointerRow = this.rnd(this.mazeSize - 2) + 1
    this.pointerCol = this.rnd(this.mazeSize - 2) + 1
    this.gameData[this.pointerRow][this.pointerCol] = false
    this.pushIt()
    return this
  }

  create(size) {
    this.init(size)
    let continueFlag = true
    while (continueFlag) {
      this.extend()
      continueFlag = false
      while (this.rowStack.length !== 0 && this.colStack.length !== 0) {
        this.pointerRow = this.rowStack.pop()
        this.pointerCol = this.colStack.pop()
        if (this.canExtendAny()) {
          continueFlag = true
          break
        }
      }
    }
    this.resetUser()
    this.resetGoal()
    this.resetMoveCount()
    return this
  }

  extend() {
    let extendFlag = true
    while (extendFlag) {
      extendFlag = this.extendSub()
    }
  }

  extendSub() {
    const directions = this.shuffle([0, 1, 2, 3])
    while (directions.length !== 0) {
      const pop = directions.pop()
      if (this.canExtend(pop)) {
        this.move(pop)
        return true
      }
    }
    return false
  }

  canExtend(direction) {
    let exRow = this.pointerRow
    let exCol = this.pointerCol
    switch (direction) {
      case 0:
        exRow--
        break
      case 1:
        exRow++
        break
      case 2:
        exCol--
        break
      case 3:
        exCol++
        break
    }
    return this.countSurroundingPath(exRow, exCol) <= 1
  }

  countSurroundingPath(row, col) {
    let num = 0
    if (row - 1 < 0 || !this.gameData[row - 1][col]) {
      num++
    }
    if (row + 1 > this.mazeSize - 1 || !this.gameData[row + 1][col]) {
      num++
    }
    if (col - 1 < 0 || !this.gameData[row][col - 1]) {
      num++
    }
    if (col + 1 > this.mazeSize - 1 || !this.gameData[row][col + 1]) {
      num++
    }
    return num
  }

  canExtendAny() {
    return (
      this.canExtend(0) ||
      this.canExtend(1) ||
      this.canExtend(2) ||
      this.canExtend(3)
    )
  }

  move(direction) {
    switch (direction) {
      case 0:
        this.pointerRow--
        break
      case 1:
        this.pointerRow++
        break
      case 2:
        this.pointerCol--
        break
      case 3:
        this.pointerCol++
        break
    }
    this.gameData[this.pointerRow][this.pointerCol] = false
    this.pushIt()
  }

  resetUser() {
    this.userRow = this.mazeSize - 1
    this.userCol = 1
    while (true) {
      if (this.gameData[this.userRow - 1][this.userCol]) {
        this.userCol++
      } else {
        break
      }
    }
    this.gameData[this.userRow][this.userCol] = false
    return this
  }

  incMoveCount = ()=>{this.moveCount++;document.getElementById('stepCount').innerText="移動数: "+String(this.moveCount)}
  decMoveCount = ()=>{this.moveCount--;document.getElementById('stepCount').innerText="移動数: "+String(this.moveCount)}
  resetMoveCount = ()=>{this.moveCount=0;document.getElementById('stepCount').innerText="移動数: "+String(this.moveCount)}

  resetGoal() {
    this.goalRow = 0
    this.goalCol = this.mazeSize - 2
    while (this.gameData[this.goalRow + 1][this.goalCol]) {
        this.goalCol--
    }
    this.gameData[this.goalRow][this.goalCol] = false
    return this
  }

  userMovable(direction) {
    let exUserRow = this.userRow
    let exUserCol = this.userCol
    switch (direction) {
      case 0:
        exUserRow--
        break
      case 1:
        exUserRow++
        break
      case 2:
        exUserCol--
        break
      case 3:
        exUserCol++
        break
    }
    if (exUserRow > this.mazeSize - 1 || this.gameData[exUserRow][exUserCol]) {
      return [false, null,null]
    } else {
      return [true,exUserRow,exUserCol]
    }
  }

  moveUser(direction) {
    const m = this.userMovable(direction)
    const movable = m[0]
    const exUserRow = m[1]
    const exUserCol = m[2]
    if(!movable){return this}
    const el = document.getElementById('mazeGUI')
    el.rows[this.userRow].cells[this.userCol].classList.remove('user')
    if (document.getElementById('trace').checked) {
      el.rows[this.userRow].cells[this.userCol].setAttribute('class', 'trace')
    } else {
      el.rows[this.userRow].cells[this.userCol].setAttribute('class', 'road')
    }
    [this.userRow,this.userCol,this.pointerRow,this.pointerCol]=[exUserRow,exUserCol,exUserRow,exUserCol]
    el.rows[this.userRow].cells[this.userCol].classList.remove('road')
    el.rows[this.userRow].cells[this.userCol].setAttribute('class', 'user')
    this.incMoveCount()
    if (this.cleared()) {
      clearTimeout(timerId)
      alert('ゴールおめでとう！\nリセットをクリックでもう一回遊べるドン！')
    }
    return this
  }

  cleared() {
    return this.userRow === this.goalRow && this.userCol === this.goalCol
  }

  view() {
    const table = document.createElement('table')
    table.setAttribute('id', 'mazeGUI')
    for (const r in this.gameData) {
      const row = document.createElement('tr')
      for (const c in this.gameData[r]) {
        const col = document.createElement('td')
        if (this.gameData[r][c]) {
          col.setAttribute('class', 'wall')
        }
        if (!this.gameData[r][c]) {
          col.setAttribute('class', 'road')
        }
        if (
          parseInt(r, 10) === this.userRow &&
          parseInt(c, 10) === this.userCol
        ) {
          col.setAttribute('class', 'user')
        }
        if (
          parseInt(r, 10) === this.goalRow &&
          parseInt(c, 10) === this.goalCol
        ) {
          col.setAttribute('class', 'goal')
        }
        row.appendChild(col)
      }
      table.appendChild(row)
    }
    return table
  }
}

let maze
let startTime
let elapsedTime = 0
let timerId
let gameFlag = false

function updateTimetText() {
  const timer = document.getElementById('stopWatch')
  let m = Math.floor(elapsedTime / 60000)
  let s = Math.floor((elapsedTime % 60000) / 1000)
  let ms = elapsedTime % 1000
  m = ('0' + m).slice(-2)
  s = ('0' + s).slice(-2)
  ms = ('0' + ms).slice(-3)
  timer.innerHTML = m + ':' + s + '.' + ms
}

function countUp() {
  timerId = setTimeout(function () {
    elapsedTime = Date.now() - startTime
    updateTimetText()
    countUp()
  }, 100)
}

function timerReset() {
  clearTimeout(timerId)
  const timer = document.getElementById('stopWatch')
  timer.innerHTML = '00:00.000'
  gameFlag = false
}


function keydown(e) {
  if (!gameFlag) {
    gameFlag = true
    startTime = Date.now()
    countUp()
  }
  e.preventDefault()
  switch (e.key) {
    case 'w':
    case 'ArrowUp':
      maze.moveUser(0)
      break
    case 's':
    case 'ArrowDown':
      maze.moveUser(1)
      break
    case 'a':
    case 'ArrowLeft':
      maze.moveUser(2)
      break
    case 'd':
    case 'ArrowRight':
      maze.moveUser(3)
      break
  }
}

function renew() {
  timerReset()
  const s = document.getElementById('size').value
  maze = new Maze().create(parseInt(s, 10))
  const el = document.getElementById('mazeGUI')
  el.parentNode.replaceChild(maze.view(), el)
}

function reset() {
  maze.resetUser()
  maze.resetMoveCount()
  timerReset()
  const el = document.getElementById('mazeGUI')
  el.parentNode.replaceChild(maze.view(), el)
}

function moveButtonClick(e) {
  if (!gameFlag) {
    gameFlag = true
    startTime = Date.now()
    countUp()
  }
  switch (e.target.id) {
    case 'bup':
      maze.moveUser(0)
      break
    case 'bdown':
      maze.moveUser(1)
      break
    case 'bleft':
      maze.moveUser(2)
      break
    case 'bright':
      maze.moveUser(3)
      break
  }
}

const onChangeValue = (e) => {
  document.getElementById('current-value').innerText = e.target.value
}

function shareTwitter(e) {
  let paramTxt = 'https://twitter.com/intent/tweet?text='
  const title =
    '迷路であそぼう - Kariaweb'+"\n"+' https://kariaweb.herokuapp.com/game/maze/ '
  paramTxt += title
  if (maze.cleared()) {
    const size = maze.mazeSize
    const time = document.getElementById('stopWatch').innerHTML
    paramTxt +=
      '\n' +
      String(size) +
      '✕' +
      String(size) +
      'の迷路を ' +
      time +
      'でクリアしたよ！'
  }
  window.open(paramTxt)
}

export default {
  props: { size: { type: Number, default: 10 } },
  mounted() {
    this.build()
  },
  methods: {
    build() {
      maze = new Maze().create(this.size)
      const el = document.getElementById('maze')
      el.appendChild(maze.view())

      document.addEventListener('keydown', keydown)
      document.getElementById('renew').addEventListener('click', renew)
      document.getElementById('reset').addEventListener('click', reset)
      document.querySelectorAll('.moveButton').forEach((mbbtn) => {
        mbbtn.addEventListener('click', moveButtonClick)
      })
      const sizeInput = document.getElementById('size')
      const current = document.getElementById('current-value')
      current.innerHTML = sizeInput.value
      sizeInput.addEventListener('input', onChangeValue)
      const shareT = document.getElementById('shareTwitter')
      shareT.addEventListener('click', shareTwitter)
    },
  },
}
</script>

<style lang="scss">
@mixin mazeDefault() {
  #maze {
    display: inline-block;
    #mazeGUI {
      margin-left: auto;
      table-layout: fixed;
      min-width: 400px;
      min-height: 400px;
      width: calc(100vw * 0.5);
      height: calc(100vw * 0.5);
      max-width: 80vmin;
      max-height: 80vmin;
      background-color: white;
      border-collapse: collapse;
      .wall {
        background-color: rgb(73, 73, 73);
      }
      .road {
        background-color: white;
      }
      .user {
        background-color: rgb(0, 162, 255);
      }
      .goal {
        background-color: red;
      }
      .trace {
        background-color: rgb(154, 197, 83);
      }
    }
  }
}
@include mazeDefault;

@media screen and (max-width: calc(400px + 250px + 20px)) {
  @include mazeDefault;
  #container-inner {
    display: flexbox;
  }
  #mazeGUI {
    min-width: 80vmin !important;
    min-height: 80vmin !important;
  }
}
</style>
