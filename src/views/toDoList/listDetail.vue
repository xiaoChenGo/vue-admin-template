<template>
  <div class="list-item" :title="item.title">
    <input
      type="checkbox"
      class="list-item-check"
      :checked="checked"
      @change="$emit('change', $event.target.checked)"
      @click.stop
    />
    <span
      :class="
        item.isChecked == true
          ? 'list-item-content list-item-content-unfinish'
          : 'list-item-content list-item-content-finish'
      "
      >{{ item.title }}</span
    >
    <div class="list-item-btn">
      <button @click.stop="done" v-if="!item.isChecked" class="item-finish">
        完成
      </button>
      <button @click.stop="active" v-if="item.isChecked" class="item-unfinish">
        激活
      </button>
      <button @click.stop="delOneItem" class="item-del">删除</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "listDetail",
  props: ["item", "checked"],
  model: {
    prop: "checked",
    event: "change",
  },
  methods: {
    delOneItem() {
      this.$emit("delOneItem", this.item.id);
    },
    done() {
      this.$emit("done");
    },
    active() {
      this.$emit("active");
    },
  },
};
</script>

<style lang="less" scoped>
@import "./common";
* {
  padding: 0%;
  margin: 0%;
}
.list-item {
  display: flex;
  align-items: center;
  position: relative;
  line-height: 18px;
  .list-item-content {
    border-bottom: 1.5px solid #d9d9d930;
    width: 450px;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    margin-left: 5px;
    margin-right: 80px;
    font-size: @font-size;
    padding: 5px;
  }
  .list-item-content-finish {
    color: #03a9f4;
  }
  .list-item-content-unfinish {
    color: rgba(36, 39, 43, 0.44);

    text-decoration: line-through;
  }
  .list-item-check {
    margin-inline: 10px;
    width: 13px;
    height: 20px;
  }
  .list-item-btn {
    position: absolute;
    width: 100px;
    right: 30px;

    button {
      line-height: 24px;
      width: 40px;
      font-size: @font-size;
      margin-right: 4px;
      outline: 0;
      border: 0;
      border-radius: 2px;
      color: #f5f5f3;
    }
    .item-finish {
      background-color: #06928c;
    }
    .item-unfinish {
      background-color: #03abf2;
    }
    .item-del {
      background-color: #cd1735eb;
    }
  }
}
</style>