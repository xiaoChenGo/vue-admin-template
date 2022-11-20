<template>
  <div>
    <!-- home -->
    <div class="to-do-list">
      <div class="head">
        <input
          type="text"
          placeholder="请输入待办事项"
          v-model="createItemTitle"
          @keydown.enter="createItem"
        />
        <button @click="createItem">添加事项</button>
      </div>
      <div class="overflow">
        <h2 style="color: rgb(3 7 55 / 52%)">未完成</h2>
        <div class="unfinished-list">
          <div v-if="itemList.length">
            <transition-group name="add-del-item" appear>
              <listDetail
                v-for="(item, idx) in itemList"
                :item="item"
                :key="item.id"
                v-model="item.isSelect"
                @delOneItem="delOneItem(idx, itemList)"
                @click.native="openItem(item)"
                @done="itemDone(item, idx)"
              ></listDetail>
            </transition-group>
          </div>
          <div v-else class="wait-content">没有待办事项</div>
        </div>
        <div class="unfinished-list-check-box">
          <input
            class="unfinished-list-check-all"
            type="checkbox"
            v-model="isCheckAll"
            @click="checkAll"
            v-if="itemList.length"
          />
        </div>
      </div>
      <div class="overflow" v-if="doneList.length">
        <h2 style="color: rgb(3 7 55 / 52%)">已完成</h2>
        <div class="finished-list">
          <transition-group name="done-del-item" appear v-if="doneList.length">
            <listDetail
              v-for="(item, idx) in doneList"
              :key="item.id"
              :item="item"
              v-model="item.isSelect"
              @active="activeItem(item, idx)"
              @delOneItem="delOneItem(idx, doneList)"
              @click.native="openItem(item)"
            ></listDetail>
          </transition-group>
        </div>
      </div>
      <button v-if="showDelBatchBtn" @click="delBatch" class="del-Batch-btn">
        批量删除
      </button>
    </div>

    <!-- detail -->
    <inputDetail
      @changeShow="changeShow"
      @updateItem="updateItem"
      v-if="isShowDialog"
      :item="toDoItem"
    />
  </div>
</template>

<script>
import listDetail from "./listDetail";
import inputDetail from "./inputDetail";
import { v4 as uuidv4 } from "uuid";
export default {
  name: "toDoList",
  components: { listDetail, inputDetail },
  data() {
    return {
      createItemTitle: "", //新建待办的标题
      isCheckAll: false, //全选
      isShowDialog: false, //显示待办详细
      itemList: [], //未完成列表
      doneList: [], //已完成列表
      toDoItem: {}, //传入详细的具体事项
    };
  },
  computed: {
    // 批量删除按钮，最少一个选中就显示
    showDelBatchBtn() {
      let result = this.itemList.some((item) => {
        return item.isSelect == true;
      });
      let result1 = this.doneList.some((item) => {
        return item.isSelect == true;
      });

      return result || result1 ? true : false;
    },
  },
  watch: {
    itemList: {
      deep: true,
      handler() {
        let num = this.itemList.reduce((pre, item) => {
          pre = item.isSelect == true ? pre + 1 : pre;
          return pre;
        }, 0);
        this.isCheckAll = num == this.itemList.length ? true : false;
      },
    },
  },
  methods: {
    // 待办详细是否显示
    changeShow(val) {
      this.isShowDialog = val;
    },
    //新建待办
    createItem() {
      if (this.createItemTitle.slice(0, 1) == " ") {
        this.createItemTitle = "";
        alert("请不要以空格开头");
      } else if (this.createItemTitle) {
        //创建新待办必须带标题
        let item = {
          title: this.createItemTitle,
          content: "",
          id: uuidv4(),
          isChecked: false,
          isSelect: false,
        };
        this.itemList.unshift(item);
        this.createItemTitle = "";
      }
    },
    //打开详细，更新传入项
    openItem(item) {
      // console.log(4554);
      this.toDoItem = item;
      this.isShowDialog = true;
    },
    //更新待办事项信息
    updateItem(val) {
      this.itemList.some((item, idx) => {
        if (item.id == val.id) {
          this.itemList[idx].title = val.title;
          this.itemList[idx].content = val.content;
          return true;
        }
      });
    },
    //删除事项
    delOneItem(idx, arr) {
      if (window.confirm("确定删除吗")) {
        arr.splice(idx, 1);
      }
    },
    //批量删除
    delBatch() {
      if (window.confirm("确定删除吗")) {
        this.itemList = this.itemList.filter((item) => {
          return item.isSelect == false;
        });
        this.doneList = this.doneList.filter((item) => {
          return item.isSelect == false;
        });
      }
    },
    //事项完成/未完成状态切换
    itemDone(item, idx) {
      item.isChecked = true;
      this.itemList.splice(idx, 1);
      this.doneList.unshift(item);
    },
    activeItem(item, idx) {
      item.isChecked = false;
      this.doneList.splice(idx, 1);
      this.itemList.unshift(item);
    },

    //点击 全选/全不选
    checkAll() {
      this.itemList.forEach((item) => {
        item.isSelect = !this.isCheckAll;
      });
    },
  },
};
</script>

<style lang="less" scoped>
@import "./common";
// @list-width: 600px;
// @font-size: 14px;

h2 {
  font-size: calc(@font-size + 3px);
  margin-left: 5px;
}
.to-do-list {
  display: flex;
  flex-direction: column;
  align-items: center;
  // height: 100vh;
  .head {
    width: calc(@list-width + 3px);
    margin-top: 10px;
    input {
      width: 80%;
      height: 40px;
      font-size: @font-size;
      border: 2px solid #3d3c3c96;
      border-right: 0;
      border-top-left-radius: 4px;
      border-bottom-left-radius: 4px;
      vertical-align: middle;
      outline: 0;
      padding-inline: 15px;
    }
    input:focus-within {
      border: 2px solid #4e6ef2;
      border-right: 0;
    }
    button {
      width: 20%;
      font-size: @font-size;
      height: 40px;
      line-height: 20px;
      background-color: #4e6ef2;
      border: 0;
      border-top-right-radius: 3px;
      border-bottom-right-radius: 3px;
      color: #ffffffe6;
      vertical-align: middle;
    }
  }
  .overflow {
    position: relative;
    overflow: hidden;
    width: @list-width;
    height: 250px;
    margin-top: 20px;
    border-radius: 4px;
    box-shadow: 2px 1px 6px 3px #2c2f2f75;
    .unfinished-list,
    .finished-list {
      width: calc(@list-width + 50px);
      height: 175px;
      overflow-x: hidden;
      overflow-y: scroll;
      position: absolute;
    }
    .finished-list {
      height: 200px;
    }
    .unfinished-list {
      // color: #4caf50;
      .wait-content {
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: center;
        font-size: @font-size;
        font-weight: bold;
        color: #636d759e;
        height: 100%;
      }
    }
    .unfinished-list-check-box {
      position: absolute;
      width: 100%;
      height: 25px;
      bottom: 0;
      background-color: white;
      .unfinished-list-check-all {
        position: absolute;
        left: 10px;
        bottom: 10px;
      }
    }
  }
  .del-Batch-btn {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translateX(320px);
    width: 50px;
    font-size: @font-size;
    color: white;
    background-color: #d12945;
    border: none;
    border-radius: 2px;
    box-shadow:  -1px 3px 3px #716b6b;
    &:active{
    box-shadow: 0px 1px 1px 1px #716b6b;
    transform: translate3d(321px, 3px, 0);
    }
  }
}

.add-del-item-enter-active {
  animation: bounce linear 1s;
}
.add-del-item-leave-active {
  animation: bounce linear 0.3s reverse;
}
.done-del-item-enter-active {
  animation: bounces linear 1s;
}
.done-del-item-leave-active {
  animation: bounces linear 0.3s reverse;
}
@keyframes bounce {
  0% {
    opacity: 0;
    transform: translateY(-20px;);
  }
  50% {
    opacity: 0.5;
    transform: translateY(3px;);
  }

  100% {
    transform: translateY(0px;);
  }
}

@keyframes bounces {
  0% {
    opacity: 0;
    transform: translateY(-20px;);
  }
  30% {
    opacity: 0.5;
    transform: translateY(-3px;);
  }
  100% {
    transform: translateY(0px;);
  }
}
</style>>

