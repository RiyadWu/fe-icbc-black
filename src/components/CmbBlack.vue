<template>
  <div>
    <div v-if="showErrorTip" class="weui-toptips weui-toptips_warn js_tooltips">
      {{ errorMessage }}
    </div>
    <form @submit="checkForm" action="#">
      <div class="weui-cells weui-cells_form">
        <div class="weui-cell" :class="invalidBusinessNumber? 'weui-cell_warn' : ''">
          <div class="weui-cell__bd">
            <input
              ref="businessNumberInput"
              class="weui-input"
              type="number"
              pattern="[0-9]{15}"
              placeholder="输入15位商户号"
              v-model="businessNumber"
            />
          </div>
          <div class="weui-cell__ft">
            <i v-if="businessNumber.length > 0"
               class="weui-icon-clear input_clear" @click="clearInput"></i>
          </div>
        </div>
      </div>

      <div class="search_button_container">
        <button type="button" @click="checkForm" class="weui-btn weui-btn_primary">查询</button>
      </div>
    </form>

    <div v-if="hasSearched">
      <div v-if="hasPreviewData" class="shop_info">
        <div class="weui-form-preview">
          <div class="weui-form-preview__hd">
            <label class="weui-form-preview__label">收单机构</label>
            <em class="weui-form-preview__value">{{ company }}</em>
          </div>
          <div class="weui-form-preview__bd">
            <p>
              <label class="weui-form-preview__label">地区代码</label>
              <span class="weui-form-preview__value">{{ areaCode }}</span>
            </p>
            <p>
              <label class="weui-form-preview__label">商户类型</label>
              <span class="weui-form-preview__value">{{ businessType }}</span>
            </p>
            <p>
              <label class="weui-form-preview__label">商户费率</label>
              <span class="weui-form-preview__value">{{ businessfee }}</span>
            </p>
            <p>
              <label class="weui-form-preview__label">招行积分</label>
              <i class="weui-icon-success integral_icon" v-if="cmbIntegral"></i>
              <i class="weui-icon-warn integral_icon" v-else></i>
            </p>
            <p>
              <label class="weui-form-preview__label">交行积分</label>
              <i class="weui-icon-success integral_icon" v-if="ccbIntegral"></i>
              <i class="weui-icon-warn integral_icon" v-else></i>
            </p>
            <p>
              <label class="weui-form-preview__label">招行积分黑名单查询</label>
              <i class="weui-icon-success integral_icon" v-if="cmbBlacklistRes.length === 0"></i>
              <span class="weui-form-preview__value" v-else>{{ cmbBlacklistRes }}</span>
            </p>
          </div>
        </div>
        <p align="left" class="cmbBlacklistResHint">
          近6个月未在招行黑名单上发现此商户号,则【招行积分黑名单查询】项显示为
          <i class="weui-icon-success integral_icon"></i>,
          否则显示出现过的月份
        </p>
      </div>
      <div class="shop_info" v-else>
        <h3>未查询到指定商户</h3>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'cmb-black',
  data() {
    return {
      businessNumber: '',
      company: '收单机构',
      areaCode: '地区代码',
      businessType: '商户类型',
      businessfee: '商户费率',
      cmbIntegral: true,
      ccbIntegral: true,
      cmbBlacklistRes: '',
      hasSearched: false,
      hasPreviewData: false,
      invalidBusinessNumber: false,
      errorMessage: '请输入15位商户号',
      showErrorTip: false,
    };
  },
  methods: {
    checkForm(e) {
      e.preventDefault();
      if (this.businessNumber.length !== 15) {
        this.invalidBusinessNumber = true;
        this.showErrorTip = true;
        setTimeout(() => {
          this.showErrorTip = false;
        }, 2000);
        return;
      }

      axios.get(`http://0.0.0.0/api?businessNumber=${this.businessNumber}`)
        .then(({ data }) => {
          console.log(data);

          this.$refs.businessNumberInput.blur();
          this.hasSearched = true;
          this.invalidBusinessNumber = false;
          this.hasPreviewData = true;
        });
    },
    clearInput() {
      this.businessNumber = '';
    },
  },
};
</script>

<style scoped>
  form {
    margin-top: 2.2em;
  }

  .shop_info {
    margin-top: 2em;
  }

  .integral_icon {
    font-size: 1.2em;
  }
  .cmbBlacklistResHint {
    font-size: 0.5em;
    padding: 10px 15px;
    color: #999;
  }
  .input_clear {
    display: inline-block;
    position: absolute;
    right: 10px;
    top: 50%;
    transform: translate(-50%, -50%);
  }
  .search_button_container {
    margin-top: 1em;
  }
  .js_tooltips {
    display: block;
  }
</style>
