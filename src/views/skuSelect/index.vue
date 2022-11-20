<template>
  <div class="div">
    <span
      style="
        text-align: center;
        margin-bottom: 27px;
        border: 3px solid #ffca2a;
        width: 100px;
        line-height: 37px;
        border-radius: 3px;
      "
      >{{ price }}元</span
    >
    <div v-for="(atrArr, idx) in json3" :key="idx" class="atrArr">
      <a
        href="#"
        v-for="(item, idx1) in atrArr"
        :key="idx1"
        :class="`item ${item.atrName ? '' : 'out-of-stock'} ${
          item.isSelect ? 'good-selected' : ''
        }
          `"
        @click="skuSelect(item, idx)"
        >{{ item.atr }}</a
      >
    </div>
  </div>
</template>

<script>
export default {
  name: "skuSelect",
  data() {
    return {
      condition: {},
      json3: [],
      json1: [
        ["红色", "黄色", "蓝色"],
        ["S码", "M码"],
        ["新疆棉", "涤纶"],
      ],
      json2: [
        { color: "红色", type: "S码", mianliao: "涤纶", price: "100" },
        { color: "黄色", type: "S码", mianliao: "涤纶", price: "300" },
        { color: "黄色", type: "M码", mianliao: "新疆棉", price: "123" },
        { color: "蓝色", type: "M码", mianliao: "新疆棉", price: "232" },
        { color: "红色", type: "S码", mianliao: "新疆棉", price: "123" },
        { color: "蓝色", type: "M码", mianliao: "涤纶", price: "123" },
      ],
    };
  },
  methods: {
    skuSelect(item, idx) {
      if (item.atrName == false) return;
      let itemName = Object.keys(this.condition)[idx];
      if (this.condition[itemName] == item.atr) {
        this.condition[itemName] = "";
      } else {
        this.condition[itemName] = item.atr;
      }
    },
  },
  created() {
    let attr = Object.keys(this.json2[0]);
    for (let i = 0; i < this.json1.length; i++) {
      this.$set(this.condition, attr[i], this.json1[i][0]);
      let atrArr = [];
      this.json1[i].forEach((item) => {
        let atr = {
          atr: item,
          isSelect: false,
          atrName: true,
        };
        atrArr.push(atr);
      });
      this.json3.push(atrArr);
    }
  },
  computed: {
    price() {
      let result = Object.values(this.condition);
      let result1 = result.every((item) => item != "");
      if (result1) {
        let range = [...this.json2];
        let condition = Object.values(this.condition);
        for (let i = 0; i < condition.length; i++) {
          range = range.filter((it) => {
            let ii = Object.values(it);
            return ii.indexOf(condition[i]) != -1;
          });
        }
        let price = range[0].price;
        return price;
      } else {
        return 0;
      }
    },
  },
  watch: {
    condition: {
      immediate: true,
      deep: true,
      handler() {
        //选中属性后，sku库存检测
        //一类属性一层，以其他层被选中属性为过滤条件，筛选本层该类属性的库存量
        for (let i = 0; i < this.json3.length; i++) {
          //分层
          let condition = Object.values(this.condition);
          condition.splice(i, 1); //过滤，按照其他层的condition的条件
          let range = condition.reduce((pre, item) => {
            if (pre.length == 0) {
              this.json2.forEach((it) => {
                let dealItem = Object.values(it);

                if (dealItem.indexOf(item) != -1 || item == "") {
                  pre.push(dealItem);
                }
              });
              return pre;
            } else {
              pre = pre.filter((it) => {
                return it.indexOf(item) != -1 || item == "";
              });
              return pre;
            }
          }, []);

          this.json3[i].forEach((item) => {
            let result = range.some((it) => {
              return it.indexOf(item.atr) != -1;
            });
            if (result) {
              item.atrName = true;
            } else {
              item.atrName = false;
            }
          });
        }
        //检查是否按钮被选中
        this.json3.forEach((item, index) => {
          let conditionItem = Object.values(this.condition)[index];
          item.forEach((i) => {
            i.isSelect = false;
            if (i.atr == conditionItem) {
              i.isSelect = true;
            }
          });
        });
      },
    },
  },
};
</script>

<style scoped>
.div {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.atrArr {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 600px;
  margin-block: 20px;
}
.item {
  display: block;
  width: 50px;
  height: 50px;
  margin-inline: 20px;
  font-size: 14px;
  border-radius: 3px;
  text-align: center;
  line-height: 50px;
  background-color: white;
  color: #3a4d19;
  border: 1px solid #8e43437a;
}
.good-selected {
  border: 2px solid #e91e63;
}

.out-of-stock {
  border: 1px solid #d7d4d4;
  background-color: #e5e1e14d;
  color: #1c212133;
}

.out-of-stock::after {
  position: absolute;
  /* right: 0; */
  content: "缺货";
  line-height: 22px;
  width: 26px;
  height: 21px;
  transform: translate(8px, -13px) scale(0.88);
  font-size: 12px;

  border-radius: 2px;
  color: #d7d4d4;
}
</style>