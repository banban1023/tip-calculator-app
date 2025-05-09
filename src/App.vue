<template>
  <div class="app">
    <header>
      <h1>SPLI<br>TTER</h1>
    </header>
    <main>
      <div class="left">
        <h2>Bill <span v-if="billError" class="error-text">Can't be zero</span></h2>
        <div class="inp-number">
          <input
            v-model.number="bill"
            type="number"
            placeholder="0"
            @blur="removeLeadingZero('bill')"
            :class="{ 'error-border': billError }"
          >
        </div>
        <h2>Select Tip %</h2>
        <div class="sel-tip">
          <ul>
            <li
            v-for="item in tipList"
            :key="item.id"
            @click="handleNum(item.num)"
            :class="{active: item.num === activeIndex}"
            >{{item.num}}%</li>
            <li><input type="number"
            v-model="custom"
            @click="clearActive"
            @blur="removeLeadingZero('custom')"
            placeholder="Custom"></li>
          </ul>
        </div>
        <h2>Number of People <span v-if="peopleError" class="error-text">Can't be zero</span></h2>
        <div class="inp-number">
          <input
            type="number"
            v-model="numPeople"
            placeholder="0"
            @blur="removeLeadingZero('numPeople')"
            :class="{ 'error-border': peopleError }"
          >
        </div>
      </div>
      <div class="cal-result">
        <div class="amount">
          <div class="info">
            <p>Tip Amount</p>
            <span>/ person</span>
          </div>
          <div class="money">
            ${{tipAmount.toFixed(2)}}
          </div>
        </div>
        <div class="amount">
          <div class="info">
            <p>Total</p>
            <span>/ person</span>
          </div>
          <div class="money">
            ${{total.toFixed(2)}}
          </div>
        </div>
       <button
        @click="resetForm"
        :disabled="!bill && !numPeople && !activeIndex && !custom"
        :class="{ stopButton: !bill && !numPeople && !activeIndex && !custom }"
      >RESET</button>
      </div>
    </main>
    </div>
</template>

<script>
export default {
  data () {
    return {
      tipList: [
        { id: 1, num: 5 },
        { id: 2, num: 10 },
        { id: 3, num: 15 },
        { id: 4, num: 25 },
        { id: 5, num: 50 }
      ],
      activeIndex: null,
      bill: null,
      numPeople: null,
      tipAmount: 0,
      total: 0,
      custom: null,
      ratio: 0,
      isShow: false,
      numPeopleError: false
    }
  },
  computed: {
    billError () {
      return this.bill === 0
    },
    peopleError () {
      return this.numPeople === 0
    }
  },
  watch: {
    bill: 'amountTip',
    numPeople: 'amountTip',
    activeIndex: 'amountTip',
    custom: 'amountTip'
  },
  methods: {
    removeLeadingZero (field) {
      if (this[field] !== null && this[field] !== '') {
        const cleaned = Number(this[field]).toString()
        // 保留 "0" 不动，让错误提示逻辑正常触发
        this[field] = cleaned === '0' ? 0 : cleaned
      }
    },
    handleNum (num) {
      this.activeIndex = this.activeIndex === num ? null : num
      this.custom = null
    },
    clearActive () {
      this.activeIndex = null
    },
    amountTip () {
      const bill = Number(this.bill)
      const people = Number(this.numPeople)

      if (bill <= 0 || people <= 0) {
        this.tipAmount = 0
        this.total = 0
        return
      }
      if (this.activeIndex !== null) {
        this.ratio = this.activeIndex
        console.log(this.ratio)
      } else if (this.custom && this.custom !== '') {
        this.ratio = Number(this.custom)
        console.log(this.ratio)
      } else {
        this.ratio = 0
      }
      const tip = bill * (this.ratio / 100)
      this.tipAmount = tip / people
      this.total = (bill + tip) / people
    },
    resetForm () {
      this.bill = null
      this.numPeople = null
      this.custom = null
      this.activeIndex = null
      this.tipAmount = 0
      this.total = 0
    }
  }
}
</script>

<style lang="less">
@import '@/style/style.less';
</style>
