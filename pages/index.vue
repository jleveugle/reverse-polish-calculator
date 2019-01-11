<template>
  <div class="container">
    <header class="text-center mt-5">
      <app-logo/>
      <h1>Reverse Polish Calculator</h1>
      <p class="lead">A calculator using reverse polish notation</p>
    </header>
    <div class="row no-gutters">
      <div class="col-md-6 mx-auto mt-4 mb-5">
        <div id="result" class="text-right p-5 bg-light">
          <span>&nbsp;{{ operation.join(" ") }}</span>
          <hr class="w-25 ml-auto" />
          <small>{{ this.currentOperand || 0 }}</small>
          <hr class="w-25 ml-auto" />
          <strong class="d-block">{{ this.stack[0] || 0 }}</strong>
        </div>
        <div class="row no-gutters">
          <div class="col-md-9">
            <div class="row no-gutters bg-light">
              <div class="col-4 mx-auto" v-for="number in [0, 1, 2, 3, 4, 5, 6, 7, 8, 9].reverse()" v-bind:key="number">
                <button class="btn btn-light text-center py-3 d-block w-100 rounded-0" type="button" v-on:click="addInOperand(number)">
                  {{ number }}
                </button>
              </div>
            </div>
          </div>
          <div class="col-md-3">
            <button class="btn btn-info d-block w-100 rounded-0 py-3" type="button" v-on:click="space()" :disabled="currentOperand.length === 0">
              SPACE
            </button>
            <div class="row no-gutters">
              <div class="col-md-6" v-for="operator in ['+', '-', '*', '/']" v-bind:key="operator">
                <button class="btn btn-primary d-block w-100 rounded-0 py-3" type="button" v-on:click="space(); add(operator)" :disabled="!canAddOperator">
                  {{ operator }}
                </button>
              </div>
              <div class="col-md-6">
                <button class="btn btn-warning d-block w-100 rounded-0 py-3" type="button" v-on:click="clear()">
                  <i class="fas fa-backspace"></i>
                </button>
              </div>
              <div class="col-md-6">
                <button class="btn btn-success d-block w-100 rounded-0 py-3" type="button" v-on:click="calc()" :disabled="!isValid">
                  =
                </button>
              </div>
            </div>
          </div>
        </div>
        <div class="row mt-5">
          <div class="col-md-6">
            <a href="https://en.wikipedia.org/wiki/Reverse_Polish_notation" class="btn btn-outline-success text-center d-block w-100" title="What is RPN? (New window)">
              <i class="fas fa-book"></i> What is RPN?
            </a>
          </div>
          <div class="col-md-6">
            <a href="https://github.com/jleveugle/reverse-polish-calculator" class="btn btn-outline-info text-center d-block w-100" title="GitHub (New window)">
              <i class="fab fa-github"></i> GitHub
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
  #result {
    position: relative;
    z-index: 1;
    box-shadow: 0 5px 5px -5px #333;
  }
</style>

<script>
import AppLogo from '~/components/AppLogo.vue'

export default {
  components: {
    AppLogo
  },
  data: function () {
    return {
      stack: [],
      operation: [],
      currentOperand: '',
    };
  },
  methods: {
    add(el) {
      this.operation.push(el);
    },
    addInOperand(el) {
      this.currentOperand += el;
    },
    calcPart(operator) {
      const n1 = this.stack.pop();
      const n2 = this.stack.pop();

      if(operator === '+') {
        this.stack.push(n2 + n1);
      } else if(operator === '-') {
        this.stack.push(n2 - n1);
      } else if(operator === '*') {
        this.stack.push(n2 * n1);
      } else if(operator === '/') {
        this.stack.push(n2 / n1);
      }
    },
    calc(operator) {
      this.space();

      this.operation.forEach(el => {
        if(typeof el === 'string') {
          this.calcPart(el)
        } else {
          this.stack.push(el)
        }
      });
      this.operation = [];
    },
    clear() {
      this.currentOperand = '';
      this.stack = [];
      this.operation = [];
    },
    space() {
      if(this.currentOperand.length > 0) {
        this.add(parseInt(this.currentOperand));
        this.currentOperand = '';
      }
    }
  },
  computed: {
    numbersLength: function() {
      return this.operation.filter(el => typeof el !== 'string').length;
    },
    operatorsLength: function() {
      return this.operation.filter(el => typeof el === 'string').length;
    },
    isValid: function() {
      return this.operatorsLength !== 0
        && 
        (
          this.numbersLength - 1 === this.operatorsLength
          || (this.stack[0] && this.numbersLength === this.operatorsLength)
        )
    },
    canAddOperator: function() {
      return (this.operatorsLength < this.numbersLength - 1)
        || (this.operatorsLength < this.numbersLength && this.currentOperand.length > 0)
        || (this.stack[0] && this.operatorsLength < this.numbersLength)
        || (this.stack[0] && this.operatorsLength <= this.numbersLength && this.currentOperand.length > 0);
    }
  }
}
</script>

