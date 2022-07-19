<!--
* @Description
* @fileName 编辑型表格数字输入框
* @author duzhaolei
* @date 2020-07-10 15:13:10
-->

<template>
  <div class="tableInput">
    <!--    <el-tooltip-->
    <!--      v-if="isShow && !noBorder"-->
    <!--      class="item"-->
    <!--      :disabled="toolTipsFlag"-->
    <!--      effect="dark"-->
    <!--      :content="value | currency(integer ? 0 : 2)"-->
    <!--      placement="top"-->
    <!--    >-->
    <!--      <span-->
    <!--        :class="{ 'el-input__inner': true, bgGrey: disabled, btnStyle: true }"-->
    <!--        @click="editFn(disabled)"-->
    <!--        @mouseenter="enter"-->
    <!--        @mouseleave="leave"-->
    <!--        style="padding:0 14px;"-->
    <!--        ><span-->
    <!--          ref="tableSpan"-->
    <!--          class="tableSpan"-->
    <!--          :style="{ textAlign: align }"-->
    <!--          >{{ value | currency(integer ? 0 : 2) }}</span-->
    <!--        ></span-->
    <!--      >-->
    <!--    </el-tooltip>-->
    <!--    v-if="!isShow && !noBorder"-->
    <input
      class="el-input__inner"
      v-bind:value="value"
      id="inputNumber"
      title=""
      type="number"
      :step="integer ? 0 : 0.01"
      ref="inputNumber"
      autocomplete="off"
      :placeholder="integer ? '请输入整数' : '0.00'"
      :disabled="disabled"
      :style="{
        width: '100%',
        textAlign: align,
        backgroundColor: disabled ? '#f7f7f7' : '#fff'
      }"
      @mousewheel="stopScrollFun($event)"
      v-on:input="handleClick"
      @blur="isShow = true"
      @focus="getCursor(value)"
    />
    <div
      v-if="isShow && noBorder"
      :style="{ width: '100%', textAlign: align }"
      class=" mr15"
    >
      {{ value | currency(integer ? 0 : 2) }}
    </div>
  </div>
</template>

<script>
export default {
  name: "yTableNumberInput",
  props: {
    value: {
      type: Number,
      defalut: null
    },
    maxlength: {
      type: String || Number,
      defalut: 3
    },
    align: {
      type: String,
      defalut: "right"
    },
    disabled: {
      type: Boolean,
      defalut: false
    },
    positiveNumberFlag: {
      type: Boolean,
      default: true
    },
    integer: {
      type: Boolean,
      defalut: false
    },
    noBorder: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      isShow: true,
      toolTipsFlag: true
    };
  },
  watch: {},
  computed: {
    placeholderStr() {
      let str = "";
      str = this.integer
        ? `请输入${this.positiveNumberFlag ? "" : "正"}整数`
        : `请输入小数位2位数字`;
      return str;
    }
  },
  methods: {
    editFn(bool) {
      if (bool != true) {
        this.isShow = false;
        this.$nextTick(() => {
          this.$refs.inputNumber.focus();
        });
      }
    },
    handleClick(e) {
      // console.log(e.target.value);
      function ifParseFloat(num) {
        let str = num.toString();
        let bool = false;
        if (str.indexOf(".") != -1) {
          bool = true;
        }
        return bool;
      }
      let reg;
      if (this.positiveNumberFlag === true) {
        reg = this.integer == false ? /\d+(\.\d{0,2})?/ : /[1-9]\d*/;
      } else {
        reg = this.integer == false ? /^[-+]?\d+(\.\d{0,2})?$/ : /-?[1-9]\d*/;
      }
      if (e.target.value == "0.0") {
        e.target.value = "0.0";
      } else {
        e.target.value = e.target.value.match(reg)
          ? ifParseFloat(e.target.value.match(reg)[0])
            ? e.target.value.match(reg)[0]
            : parseFloat(e.target.value.match(reg)[0])
          : "";
        //最大值判断
        let numStr = e.target.value.toString();
        if (numStr.indexOf(0) === 0) {
          if (numStr.indexOf(".") == -1) {
            e.target.value = parseFloat(e.target.value);
          }
        }
        let maxNum = this.integer
          ? 10 ** parseInt(this.maxlength) - 1
          : 10 ** (parseInt(this.maxlength) - 2) - 0.01;
        if (parseFloat(e.target.value) > maxNum) {
          e.target.value = maxNum;
        }
      }
      this.$emit("input", Number(e.target.value));
    },
    getCursor(val) {
      if (window.ActiveXObject || "ActiveXObject" in window) {
        this.$refs.inputNumber.setSelectionRange(
          val.toString().length,
          val.toString().length
        );
      }
    },
    enter() {
      if (
        this.$refs.tableSpan.scrollWidth != this.$refs.tableSpan.clientWidth &&
        this.$refs.tableSpan.scrollWidth > this.$refs.tableSpan.clientWidth - 32
      ) {
        this.toolTipsFlag = false;
      } else {
        this.toolTipsFlag = true;
      }
    },
    leave() {
      this.toolTipsFlag = true;
    },
    stopScrollFun(evt) {
      evt = evt || window.event;
      if (evt.preventDefault) {
        // Firefox
        evt.preventDefault();
        evt.stopPropagation();
      } else {
        // IE
        evt.cancelBubble = true;
        evt.returnValue = false;
      }
      return false;
    }
  },
  created() {},
  mounted() {}
};
</script>

<style lang="scss" scoped>
.tableInput {
  display: flex;
  justify-content: center;
  align-items: center;
}
.el-input__inner {
  height: 30px;
  line-height: 28px;
  -webkit-appearance: none;
  background-color: #fff;
  background-image: none;
  border-radius: 4px;
  border: 1px solid #dcdfe6;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  color: #333;
  display: inline-block;
  font-size: inherit;
  outline: 0;
  padding: 0 15px;
  -webkit-transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
  transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
  width: 100%;
}
.tableSpan {
  display: inline-block;
  width: 100%;
  color: rgb(102, 102, 102);
  overflow: hidden;
  overflow: hidden;
  text-align: right;
  text-overflow: ellipsis;
  white-space: nowrap;
}
</style>
