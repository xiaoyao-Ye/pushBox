<template>
  <div id="app">
    <div class="topbar">推箱子</div>
    <div class="container">
      <div class="content">
        <!-- 游戏框 -->
        <div class="game">
          <div class="father" v-for="(father, i) in arr" :key="i">
            <div
              :class="{son: true, qiang: son === 1, box: son === -1, person: son === -2, ok: son === 2}"
              v-for="(son, j) in father"
              :key="j"
            ></div>
          </div>
        </div>
        <!-- 信息框 -->
        <div class="info">
          <h1>当前第{{currentLevel + 1}}关</h1>
          <div class="guanqia">
            <a-input-number v-model="guanqia" placeholder="选择1~100关" />
            <a-button @click="onSearch">选择关卡</a-button>
          </div>
          <br />
          <br />
          <a-button style="margin-bottom: 50px;" @click="init()">重置当前关</a-button>
          <br />
          <br />
          <div class="fangxiang">
            <h2 style="width: 100%;margin-bottom: 30px;border-bottom: 1px solid #ccc;">键盘按键上下左右控制</h2>
            <div>
              <a-button>上</a-button>
            </div>
            <br />
            <br />
            <div class="center">
              <a-button>左</a-button>
              <a-button>下</a-button>
              <a-button>右</a-button>
            </div>
            <br />
            <br />
          </div>
        </div>
      </div>
    </div>

    <!-- 提示框 -->
    <a-modal
      title="恭喜!!!"
      :visible="visible"
      okText="下一关"
      cancelText="重玩本关"
      :maskClosable="false"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <p>恭喜你通过本关,是否进入下一关?</p>
    </a-modal>
  </div>
</template>

<script>
import data from "./utils/data";
export default {
  name: "App",
  data() {
    return {
      // 弹窗
      visible: false,
      // 当前第几关
      currentLevel: 0,
      // 关卡输入框
      guanqia: null,
      arr: [],
      // 确认位置
      ok: [
        // 第几行
        // [2, 3, 4, 5],
        // 第几列
        // [1, 1, 1, 1]
      ],
      // tui
      box: [
        // 第几行
        // [2, 3, 4, 5],
        // 第几列
        // [2, 2, 2, 2]
      ],
      // person
      person: [
        // [5], [7]
      ]
    };
  },
  created() {
    this.fuzhi();
  },
  mounted() {
    // 1.绑定键盘事件
    document.addEventListener("keydown", this.keyDownFn);
  },
  methods: {
    // 0.init初始化
    init() {
      // 先清空地图
      let newArr = this.arr.map(row => {
        let newRow = row.map(col => {
          if (col === -2 || col === -1 || col === 2) {
            col = 0;
          }
          return col;
        });
        return newRow;
      });
      this.arr = newArr;
      // 初始化
      this.$set(this.arr[this.person[0][0]], this.person[1][0], -2);
      this.ok[0].map((v, index) => {
        this.$set(this.arr[this.ok[0][index]], this.ok[1][index], 2);
        this.$set(this.arr[this.box[0][index]], this.box[1][index], -1);
      });
    },
    // 4.判断数据位置是否正确
    isOk() {
      let bool = this.ok[0].every((v, index) => {
        if (this.arr[this.ok[0][index]][this.ok[1][index]] >= 0) {
          this.$set(this.arr[this.ok[0][index]], this.ok[1][index], 2);
        }
        return this.arr[this.ok[0][index]][this.ok[1][index]] === -1;
      });
      if (bool) {
        this.visible = true;
      }
    },
    // === -2 判断位置 && 判断边界(如果边界都是墙的话可以去掉这个判断) && 判断下一个位置是否等于0
    // 3.方法进行数据操作(用到this,使用箭头函数)
    yidong(code) {
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
                this.isOk();
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
                this.isOk();
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
                this.isOk();
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
                this.isOk();
                return;
              }
            }
          }
        }
      }
    },
    // 1.键盘事件
    keyDownFn(e) {
      var code = e.keyCode || e.charCode || e.which;
      if (code >= 37 && code <= 40) {
        // 2.触发相应的方法
        this.yidong(code);
      }
    },

    // ==
    // 弹窗-确定按钮
    handleOk() {
      this.visible = false;
      this.currentLevel++;
      // console.log(this.currentLevel);
      this.fuzhi();
      // console.log("当前第:" + (this.currentLevel + 1) + "关");
    },
    // 弹窗-重玩按钮
    handleCancel() {
      this.init();
      this.visible = false;
    },
    // 选择关卡
    onSearch() {
      if (this.guanqia) {
        this.guanqia = parseInt(this.guanqia);
      } else {
        return this.$message.error("请输入关卡!");
      }
      if (this.guanqia >= 1 && this.guanqia <= 100) {
        this.currentLevel = this.guanqia - 1;
        this.fuzhi();
      } else {
        this.$message.error("请输入正确的关卡!");
      }
    },
    // 取出关卡数据赋值当前关卡.
    fuzhi() {
      // console.log(data[this.currentLevel].arr);
      this.arr = data[this.currentLevel].arr;
      this.ok = data[this.currentLevel].ok;
      this.box = data[this.currentLevel].box;
      this.person = data[this.currentLevel].person;
      this.init();
    }
  }
};
</script>

<style lang='less'>
#app {
  .topbar {
    height: 64px;
    font-size: 30px;
    text-align: center;
    line-height: 64px;
    color: #fff;
    background-color: #1890ff;
  }
  .container {
    padding: 50px 0;
    background-color: #eee;
  }
  .content {
    width: 1000px;
    margin: 0 auto;
    display: flex;
    // flex-direction: column;
    .game {
      flex: 1;
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
        .qiang {
          background-color: #222;
          background: url("./assets/caocong.png") no-repeat;
          background-size: contain;
        }
        .box {
          background-color: green;
          background: url("./assets/box.png") no-repeat;
          background-size: contain;
        }
        .person {
          background-color: blue;
          background: url("./assets/person.png") no-repeat;
          background-size: contain;
        }
        .ok {
          background-color: #eee;
        }
      }
    }
    .info {
      width: 300px;
      .fangxiang {
        display: flex;
        align-items: center;
        flex-direction: column;
        border: 1px solid #ccc;
        .center {
          justify-content: space-between;
          display: flex;
          width: 80%;
        }
      }
      .guanqia {
        display: flex;
        .ant-input-number {
          flex: 1;
          margin-right: 10px;
        }
      }
    }
  }
}
</style>
