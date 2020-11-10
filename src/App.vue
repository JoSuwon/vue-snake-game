<template>
  <div id="app">
    <div
      v-for="(rowMap,i) in map"
      :key="rowMap.id"
      id="rowMap"
    >
      <div 
        v-for="(tile,j) in rowMap"
        :key="tile.id"
        :class="{wall:tile===1, empty:tile===0}"
        id="tile"
      >
        <!-- {{i}}, {{j}} -->
        <div
          v-if="snake.filter(item => item.x===j && item.y===i).length !== 0"
        >🟧</div>
        <div
          v-else-if="i === apple.y && j === apple.x"
        >💛</div>
      </div>
    </div>
    <input ref="joystick" type="text"  @keydown="inputKey" placeholder="조이스틱"/>
    <h2>점수 : {{score}} 점</h2>
    <div v-if="status === 'finish'">
      <h1>Game Over!!</h1>
      <button @click="restart">새로하기</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      map: [
        [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
        [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
      ],
      snake: [
        { id:0, y: 5, x: 13 },
        { id:1, y: 5, x: 12 },
        { id:2, y: 5, x: 11 },
        { id:3, y: 5, x: 10 },
      ],
      direction: 'right',
      moveTimer: null,
      apple: { y: 1, x: 1},
      score: 0,
      status: null,
    };
  },
  mounted() {
    this.$refs.joystick.focus();
    this.$refs.joystick.addEventListener('blur', () => {
      this.$refs.joystick.focus();
    });

    let newX = Math.floor(Math.random()*(this.map[0].length-2)) + 1;
    let newY = Math.floor(Math.random()*(this.map.length-2)) + 1;
    this.apple = { y: newY, x: newX };

    this.moveTimer = setInterval(() => {
      this.move();
    }, 150);

    // console.log('mounted');
    // let i = 0;
    // let j = 0;
    // console.log(this.snake.filter(item => item.x===j && item.y===i));
  },
  methods: {
    inputKey(e) {
      // if(e.code === 'Space') {
      //   this.move();
      // }
      // let str = 'hello';
      if(e.key === 'ArrowRight' && this.direction !== 'left') {
        this.direction = 'right';
      } else if(e.key === 'ArrowLeft' && this.direction !== 'right') {
        this.direction = 'left';
      } else if(e.key === 'ArrowUp' && this.direction !== 'down') {
        this.direction = 'up';
      } else if(e.key === 'ArrowDown' && this.direction !== 'up') {
        this.direction = 'down';
      }
    },
    move() {
      let newSnake = [];

      console.log(this.snake);

      let tail = Object.assign({}, this.snake[this.snake.length-1], { id: this.snake.length });
      console.log('tail: ', tail);

      if(this.direction === 'right') {
        let newHead = Object.assign({}, this.snake[0]);
        newHead.x = newHead.x + 1;
        newSnake.push(newHead);
        for(let i=0; i<this.snake.length-1; i++) {
          newSnake.push({ x:this.snake[i].x, y:this.snake[i].y, id:i+1 });
        }
      } else if(this.direction === 'left') {
        let newHead = Object.assign({}, this.snake[0]);
        newHead.x = newHead.x - 1;
        newSnake.push(newHead);
        for(let i=0; i<this.snake.length-1; i++) {
          newSnake.push({ x:this.snake[i].x, y:this.snake[i].y, id:i+1 });
        }
      } else if(this.direction === 'up') {
        let newHead = Object.assign({}, this.snake[0]);
        newHead.y = newHead.y - 1;
        newSnake.push(newHead);
        for(let i=0; i<this.snake.length-1; i++) {
          newSnake.push({ x:this.snake[i].x, y:this.snake[i].y, id:i+1 });
        }
      } else if(this.direction === 'down') {
        let newHead = Object.assign({}, this.snake[0]);
        newHead.y = newHead.y + 1;
        newSnake.push(newHead);
        for(let i=0; i<this.snake.length-1; i++) {
          newSnake.push({ x:this.snake[i].x, y:this.snake[i].y, id:i+1 });
        }
      }
      console.log('newSnake: ', newSnake);

      // 하트먹을때
      if(this.snake[0].x === this.apple.x && this.snake[0].y === this.apple.y) {
        let newX = Math.floor(Math.random()*(this.map[0].length-2)) + 1;
        let newY = Math.floor(Math.random()*(this.map.length-2)) + 1;
        this.apple = { y: newY, x: newX };
        this.score += 10;
        newSnake.push(tail);
      }

      this.snake = newSnake;

      // 벽에 박을때
      if(this.map[this.snake[0].y][this.snake[0].x] === 1) {
        clearInterval(this.moveTimer);
        console.log('game over!!');
        this.status = 'finish';
      }

      console.log(this.snake);
      // 자기몸에 박을때
      this.snake.find(item => {
        // console.log('id: ' + item.id);
        // console.log(`head(y, x): (${this.snake[0].y}, ${this.snake[0].x})    item(y, x): (${item.y}, ${item.x})`);
        if(item.id !== 0 && this.snake[0].y === item.y && this.snake[0].x === item.x) {
          clearInterval(this.moveTimer);
          console.log('game over!!');
          this.status = 'finish';
        }
      });
    },
    restart() {
      console.log('restart');
      location = '/';
    }
  }
}
</script>

<style lang="scss">
#rowMap {
  width: 1200px;
  height: 50px;
}
#tile {
  width: 50px;
  height: 50px;
  float: left;
}
div.empty {
  background-color: greenyellow;
}
div.wall {
  background-color: brown;
}
</style>
