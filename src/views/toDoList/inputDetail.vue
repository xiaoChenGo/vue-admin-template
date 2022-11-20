<template>
  <div class="dialog" @click.self="changeShow">
    <div class="detail" @click.prevent>
      <label>
        <span>事项:</span>
        <input
          v-model="list.title"
          :disabled="item.isChecked"
          :style="
            item.isChecked
              ? 'text-decoration:line-through;color:rgba(36, 39, 43, 0.44);'
              : ''
          "
        />
      </label>
      <label>
        <!-- <span>详细:</span> -->
        <textarea
          class="detail-content"
          v-model="list.content"
          :disabled="item.isChecked"
          placeholder="可输入详细信息"
        ></textarea>
      </label>
      <label>
        <button @click="updateItem" class="confirm">确定</button>
        <button @click="changeShow" class="cancel">取消</button>
      </label>
    </div>
  </div>
</template>

<script>
export default {
  name: "inputDetail",
  props: ["item"],
  data() {
    return {
      list: {
        title: "",
        content: "",
        isChecked: false,
      },
    };
  },
  methods: {
    changeShow() {
      this.$emit("changeShow", false);
    },
    updateItem() {
      if (this.list.title) {
        this.$emit("updateItem", this.list);
        this.changeShow();
      }
    },
  },
  mounted() {
    Object.assign(this.list, this.item);
  },
};
</script>

<style lang="less" scoped>
@import "./common"; 
* {
  outline: 0;
}
.dialog {
  position: absolute;
  height: 100vh;
  width: 100vw;
  left: 0;
  top: 0;
  background-color: #d9d9dded;
  .detail {
    display: flex;
    flex-direction: column;
    position: absolute;
    top: 45px;
    left: 20%;
    border-radius: 5px;
    width: 800px;
    box-shadow: 1px 3px 10px 1px black;
    background-color: #f4f0f3;
    padding: 5px;

    span {
      display: inline-block;
      margin-left: 10px;
      border-right: 0;
      width: 15%;
      text-align: center;
      font-size: @font-size;
      background-color: #2e827a;
      color: #ffffff;
      vertical-align: bottom;
      border-top-left-radius: 5px;
      border-bottom-left-radius: 5px;
      // height: 23px;
      line-height: 23px;
    }
    label {
      color: #020202b0;
    }
    input {
      height: 23px;
      background-color: #f4f0f3;
      color: #020202b0;
      font-weight: 550;
      font-size: @font-size;
      border: 2px solid #607d8b57;
      border-left-width: 0;
      border-left: 0;
      border-bottom-right-radius: 5px;
      border-top-right-radius: 5px;
      vertical-align: bottom;
      width: 60%;
      padding-left: 3px;
    }
    input:focus-within {
      border: 2px solid #2e827a;
      border-left-width: 0;
      border-left: 0;
      border-bottom-right-radius: 9px;
      border-top-right-radius: 9px;
    }

    .detail-content {
      font-size: @font-size;
      resize: none;
      width: 100%;
      height: 60vh;
      border: 1px solid #cfcdcdb3;
      border-radius: 3px;
      margin-top: 10px;
      background: #eeeeee;
      color: #3d4042;
    }
    button {
      float: right;
      // margin: 3px;
      // border: 0;
      line-height: 24px;
      width: 50px;
      font-size: @font-size;
      margin-right: 4px;
      outline: 0;
      border: 0;
      border-radius: 2px;
      color: #f5f5f3;
    }
    .confirm {
      background-color: #697575;
    }
    .cancel {
      background-color: #06928c;
    }
  }
}
</style>