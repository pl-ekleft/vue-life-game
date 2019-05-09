<template>
  <main>
    <table>
      <tr v-for ="(row, i) in rows"
          :key="i"
      >
        <td v-for ="(col, i) in row.cols"
            :key="i"
            :class="{'new': col.active === 1, 'old': col.active >1 }"
            @click="activate(rows.indexOf(row), i)"
        ></td>
      </tr>
    </table>

    <div class="controllers">
      Зарождение: {{ generation }} <br><br>
      <button @click="stopGame">Остановить</button>
      <button @click="startGame(false)">Запустить</button>
      <button @click="clearField">Очистить</button>
    </div>
  </main>
</template>

<script>
  export default {
    name: 'AppLifeGame',
    data: () => ({
      rows: [],
      rowMax: 25,
      colMax: 25,
      interval: 0,
      generation: 0,
    }),
    methods: {
      stopGame() {
        clearInterval(this.interval);
      },
      startGame(random) {
        this.generation = 0;
        if (random) {
          this.randomSeeds();
        }
        this.interval = setInterval(() => {
          this.generation++;
          for (let i=0; i < this.rowMax; i++){
            for (let j=0; j < this.colMax; j++){
              this.rows[i].cols[j].neighbours = this.checkNeighbours(i, j);
            }
          }
          for (let i=0; i < this.rowMax; i++){
            for (let j=0; j < this.colMax; j++){
              if (this.rows[i].cols[j].active >= 1) {
                if (this.rows[i].cols[j].neighbours >= 4 || this.rows[i].cols[j].neighbours <= 1) { // все соседи кроме 2 и 3
                  this.rows[i].cols[j].active = 0;
                } else {
                  this.rows[i].cols[j].active++;
                }
              } else {
                if (this.rows[i].cols[j].neighbours === 3) { // если есть 3 активных соседних клеток
                  this.rows[i].cols[j].active = 1;
                }
              }
            }
          }
        }, 100);
      },
      clearField() { // очистка поля
        for (let i=0; i < this.rowMax; i++){
          for (let j=0; j < this.colMax; j++){
            this.rows[i].cols[j].active = 0;
          }
        }
      },
      randomSeeds() { // генерируем случайную скорость
        for (let i = 0; i < (this.rowMax * this.colMax / 2); i++){
          let row = Math.floor(Math.random() * (this.rowMax -  1)),
            col = Math.floor(Math.random() * (this.colMax -  1));
          this.activate(row, col);
        }
      },
      activate(row, col) { // активируем клетку
        this.rows[row].cols[col].active = 1;
      },
      checkNeighbours(row, col){ // проверяем соедние клетки
        let count = 0;
        for (let i = row-1; i <= row+1; i++){
          for (let j= col-1; j <= col+1; j++){
            if ((i !== row || j !== col) &&(this.rows[i]) && (this.rows[i].cols[j]) ){
              if (this.rows[i].cols[j].active >= 1){
                count++;
              }
            }
          }
        }
        return count;
      },
    },
    created() {
      // генерируем поле
      for (let i = 0; i < this.rowMax; i++){
        let col = [];
        for (let j = 0; j < this.colMax; j++){
          let elem = {'active': 0};
          col.push(elem);
        }
        this.rows.push({'cols': col});
      }
    },
    mounted() {
      this.startGame(true);
    }
  }
</script>

<style>
  body {
    color: #fff;
    background-color: #15353c;
  }
  table, th, td {
    border: 1px solid #15353c;
    border-collapse: collapse;
  }
  table {
    margin: auto;
    background-color: #fff;
  }
  td {
    width: 20px;
    height: 20px;
  }
  button {
    padding: 6px 20px;
    font-size: 14px;
    color: #000;
    border:  none;
    border-radius: 6px;
    box-shadow: 0 0 0 2px #ffffff40;
    background-color: white;
    outline: none;
  }
  button + button {
    margin-left: 20px;
  }
  button:hover {
    cursor: pointer;
    background-color: #dcfff1;
  }
  button:active {
    cursor: pointer;
    background-color: #98ff78;
  }
  .new {
    background-color: #beffa6;
  }
  .old {
    background-color: #11ff24;
  }
  .controllers {
    display: block;
    margin: 20px auto 20px;
    text-align: center;
  }
</style>
