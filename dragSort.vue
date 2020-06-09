<template>
  <div id="yyDrag" class="yy-drag" @mouseup="up()" @mousemove="mousemove">
    <div
      class="mask"
      :style="'left:' + left + 'px;top:' + top + 'px'"
      v-show="choose != null"
    ></div>
    <div class="list">
      <div
        v-for="(item, index) in arr"
        :key="index"
        class="item"
        :class="{ pos: position == index }"
      >
        <div
          @mousedown.prevent="down(...arguments, index)"
          :class="{ on: choose == index }"
        >
          <input @mousedown.stop type="text" v-model="item.value" />
        </div>
        <button @click="reduce(index)">-</button>
      </div>
      <!-- ↓在末尾放一个空的div，因为虽然数组有n个元素，但是可移动的间隙位置有n+1个 -->
      <div
        class="item"
        :class="{ pos: position == arr.length }"
        style="height:0"
      ></div>
    </div>
    <div>
      <button @click="add">+</button>
    </div>
    <div>
      <span>当前数据顺序</span>
      <span v-for="(item, index) in arr" :key="index">{{ item.value }},</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "Drag",
  data() {
    return {
      arr: [
        //需要排序的数组
        {
          value: "1"
        },
        {
          value: "2"
        },
        {
          value: "3"
        },
        {
          value: "4"
        }
      ],
      choose: null, //当前选择的item
      position: null, //即将移动去的位置
      left: 0, //选择浮层的左定位
      top: 0, //选择浮层的上定位
      offsetLeft: 0, //可移动区域距离左部
      offsetTop: 0 //可移动区域距离顶部
      //   position:
    };
  },
  methods: {
    down(e, index) {
      //   console.log("点击");
      this.choose = index;
      this.mousemove(e);
    },
    up(index) {
      //   console.log("抬起");
      if (
        this.choose != null &&
        this.position != null &&
        this.choose != this.position //相等不需要排序
      )
        this.sort(this.choose, this.position);
      this.choose = null;
      this.position = null;
    },
    add() {
      this.arr.push({
        value: this.arr.length + 1 //这个数据只是方便看，可以加空的
      });
    },
    reduce(index) {
      this.arr.splice(index, 1);
    },
    mousemove(e) {
      if (this.choose == null) return false;
      //鼠标位置减去区域距离页面位置，再减浮层的一半大小，就是浮层的定位位置
      this.left = e.clientX - this.offsetLeft - 55;
      this.top = e.clientY - this.offsetTop - 55;
      //110是盒子高度加marginTop，判断现在鼠标位置的纵坐标移动到了数组的哪里，每110就是往下了一个，这个用来决定当前移动的position
      for (let i = 0; i < this.arr.length + 1; i++) {
        if (this.top < 110 * i) {
          this.position = i;
          break;
        }
      }
    },
    sort(choose, position) {
      let item = this.arr[choose]; //先拷贝一份需要排序的item
      this.arr.splice(position, 0, item); //把这个item插入新位置
      //↓ 删掉原来的 根据是向上还是向下移动（新元素插入位置），决定删除原位置还是原位置的下一个
      if (choose < position) this.arr.splice(choose, 1);
      else this.arr.splice(choose + 1, 1);
      //   console.log("排序");
    }
  },

  mounted() {
    //5是边框 加上
    this.offsetTop = document.getElementById("yyDrag").offsetTop + 5;
    this.offsetLeft = document.getElementById("yyDrag").offsetLeft + 5;
  }
};
</script>

<style scoped lang="scss">
.yy-drag {
  position: relative;
  display: flex;
  flex-direction: column;
  border: 5px solid grey;
  margin: 50px;
  > div {
    margin: 20px 0 0;
  }
  .mask {
    position: absolute;
    z-index: 1;
    width: 100px;
    height: 100px;
    margin: 0;
    border: 2px solid rgb(177, 177, 177);
    background: #fff;
    opacity: 0.8;
    cursor: pointer;
  }
  .list {
    list-style: none;
    margin: 0 auto;
    padding: 0;

    .item {
      position: relative;
      display: flex;
      align-items: center;
      margin: 10px 0 0;
      &.pos::after {
        content: "";
        position: absolute;
        top: -5px;
        left: -15px;
        z-index: 1;
        display: block;
        width: 130px;
        height: 3px;
        background: grey;
      }
      button {
        margin-left: 10px;
        width: 20px;
        height: 20px;
      }
      div {
        cursor: pointer;
        width: 100px;
        height: 100px;
        box-sizing: border-box;
        border: 5px solid rgb(255, 109, 109);
        background: pink;
        input {
          width: 80px;
          margin: 0 auto;
        }
        &.on {
          opacity: 0.5;
        }
      }
    }
  }
}
</style>
