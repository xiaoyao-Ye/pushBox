<template>
  <div id="app">
    <div class="topbar"></div>
    <a-button ghost>Ghost</a-button>
    <div class="content">
      <div class="father" v-for="(item, i) in arr" :key="i">
        <div
          :class="{son: true, qiang: son === 1, box: son === -1, person: son === -2}"
          v-for="(son, j) in item"
          :key="j"
        >{{son}}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      arr: [
        [1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
        [1, 1, 0, 0, 1, 1, 1, 1, 1, 1],
        [1, 1, 0, 0, 0, 0, 0, 0, 1, 1],
        [1, 0, 0, 0, 0, 0, 0, 0, 1, 1],
        [1, 0, 0, 0, 0, 0, 0, 0, 1, 1],
        [1, 1, 0, 0, 0, 0, 0, 0, 1, 1],
        [1, 1, 1, 0, 0, 0, 0, 1, 1, 1],
        [1, 1, 1, 1, 0, 0, 0, 1, 1, 1],
        [1, 0, 0, 0, 0, 0, 0, 1, 1, 1],
        [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
      ],
      // 确认位置
      ok: [
        // 第几行
        [2, 3, 4, 5],
        // 第几列
        [1, 1, 1, 1]
      ],
      // tui
      box: [
        // 第几行
        [2, 3, 4, 5],
        // 第几列
        [2, 2, 2, 2]
      ],
      // person
      person: [[5], [7]]
    };
  },
  mounted() {
    // 0.init
    const init = () => {
      this.$set(this.arr[this.person[0][0]], this.person[1][0], -2);
      this.ok[0].map((v, index) => {
        this.$set(this.arr[this.ok[0][index]], this.ok[1][index], 2);
        this.$set(this.arr[this.box[0][index]], this.box[1][index], -1);
      });
    };
    init();
    // === -2 判断位置 && 判断边界(如果边界都是墙的话可以去掉这个判断) && 判断下一个位置是否等于0
    // 3.方法进行数据操作(用到this,使用箭头函数)
    const yidong = code => {
      if (code === 37) {
        let arr1 = this.arr.map(x => {
          x.map((y, yi) => {
            if (y === -2 && yi != 0) {
              if (x[yi - 1] === 0 || x[yi - 1] === 2) {
                x[yi - 1] = -2;
                x[yi] = 0;
              }
              if (x[yi - 1] === -1 && (x[yi - 2] === 0 || x[yi - 2] === 2)) {
                x[yi - 2] = -1;
                x[yi - 1] = -2;
                x[yi] = 0;
                isOk();
              }
            }
          });
          return x;
        });
        this.arr = arr1;
      } else if (code === 38) {
        let arr1 = this.arr.map((x, xi) => {
          x.map((y, yi) => {
            if (y === -2 && xi != 0) {
              if (this.arr[xi - 1][yi] === 0 || this.arr[xi - 1][yi] === 2) {
                this.arr[xi - 1][yi] = -2;
                x[yi] = 0;
              }
              if (
                this.arr[xi - 1][yi] === -1 &&
                (this.arr[xi - 2][yi] === 0 || this.arr[xi - 2][yi] == 2)
              ) {
                this.arr[xi - 2][yi] = -1;
                this.arr[xi - 1][yi] = -2;
                x[yi] = 0;
                isOk();
              }
            }
          });
          return x;
        });
        this.arr = arr1;
      } else if (code === 39) {
        let arr1 = this.arr.map(x => {
          for (var i = 0; i < x.length; i++) {
            if (x[i] === -2 && i != x.length - 1) {
              if (x[i + 1] === 0 || x[i + 1] === 2) {
                x[i + 1] = -2;
                x[i] = 0;
                break;
              }
              if (x[i + 1] === -1 && (x[i + 2] === 0 || x[i + 2] === 2)) {
                x[i + 2] = -1;
                x[i + 1] = -2;
                x[i] = 0;
                isOk();
                break;
              }
            }
          }
          return x;
        });
        this.arr = arr1;
      } else if (code === 40) {
        let arr1 = this.arr;
        for (var i = 0; i < arr1.length; i++) {
          let sonarr = arr1[i];
          for (var j = 0; j < sonarr.length; j++) {
            if (sonarr[j] === -2 && i != arr1.length - 1) {
              if (arr1[i + 1][j] === 0 || arr1[i + 1][j] === 2) {
                this.$set(arr1[i + 1], j, -2);
                this.$set(sonarr, j, 0);
                return;
              }
              if (
                arr1[i + 1][j] === -1 &&
                (arr1[i + 2][j] === 0 || arr1[i + 2][j] === 2)
              ) {
                this.$set(arr1[i + 2], j, -1);
                this.$set(arr1[i + 1], j, -2);
                this.$set(sonarr, j, 0);
                isOk();
                return;
              }
            }
          }
        }
      }
    };
    // 1.绑定键盘事件
    document.addEventListener("keydown", keyDownFn);
    function keyDownFn(e) {
      var code = e.keyCode || e.charCode || e.which;
      if (code >= 37 && code <= 40) {
        // 2.触发相应的方法
        yidong(code);
      }
    }

    // 4.判断数据位置是否正确
    const isOk = () => {
      let bool = this.ok[0].every((v, index) => {
        if (this.arr[this.ok[0][index]][this.ok[1][index]] >= 0) {
          this.$set(this.arr[this.ok[0][index]], this.ok[1][index], 2);
        }
        return this.arr[this.ok[0][index]][this.ok[1][index]] === -1;
      });
      if (bool) {
        console.log("ok!");
      } else {
        console.log("xx");
      }
    };
  }
};
</script>

<style lang='less'>
#app {
  .topbar {
    height: 64px;
    background-color: #1890ff;
  }
  .content {
    .father {
      display: flex;
      width: 500px;
      height: 50px;
      margin: 0 auto;
      .son {
        flex: 1;
        text-align: center;
        line-height: 50px;
      }
      // .qiang {
      //   background-color: #eee;
      // }
      // .box {
      //   background-color: green;
      // }
      // .person {
      //   background-color: skyblue;
      // }
    }
  }
}
</style>
