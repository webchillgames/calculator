<template>
  <div class="calc">
    <div class="calc__display">{{ display }}</div>
    <div class="calc__btns">
      <div>
        <div class="calc__center">
          <button @click="clear">C</button>
          <button @click="toggleMinus">+/-</button>
          <button @click="set" value="%">%</button>

          <button v-for="n in 9" :key="n" :value="String(n)" @click="set">{{ n }}</button>
        </div>
        <div class="calc__bottom" @click="set">
          <button value="0">0</button>
          <button value=",">,</button>
        </div>
      </div>
      <div class="calc__right" @click="set">
        <button value="/">/</button>
        <button value="*">x</button>
        <button value="-">-</button>
        <button value="+">+</button>
        <button value="=">=</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

const digits = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', ',', '.']
const actions = ['+/-', '%', '/', '*', '-', '+', '=']

export default defineComponent({
  setup() {
    const number1 = ref('')
    const number2 = ref('')
    const sign = ref('')
    const isFinished = ref(false)
    const display = ref('0')

    function set(e: PointerEvent) {
      const key = getKeyFromClick(e)

      if (digits.includes(key)) {
        return processDigit(key)
      }

      if (key === '=') {
        return processResult()
      }

      if (actions.includes(key)) {
        return processAction(key)
      }

      if (sign.value && number1.value && number2.value && actions.includes(key) && key !== '=') {
        return calculate()
      }
    }

    function getKeyFromClick(e: PointerEvent) {
      const el = e.target as HTMLElement

      const button = el.closest('button')
      if (!button) {
        return ''
      }

      return button.value
    }

    function processDigit(key: string) {
      if (number2.value === '' && sign.value === '') {
        number1.value += key
        display.value = number1.value
      } else if (
        number1.value !== '' &&
        number2.value !== '' &&
        sign.value === '' &&
        isFinished.value
      ) {
        isFinished.value = false
        display.value = number1.value
      } else {
        number2.value += key
        display.value = number2.value
      }

      // console.log('n1:', number1.value)
      // console.log('n2:', number2.value)
    }

    function processAction(key: string) {
      sign.value = key
      display.value = sign.value

      // console.log('sign:', sign.value)
    }

    function processResult() {
      switch (sign.value) {
        case '+':
          number1.value = String(Number(number1.value) + Number(number2.value))
          break
        case '-':
          number1.value = String(Number(number1.value) - Number(number2.value))
          break
        case '*':
          number1.value = String(Number(number1.value) * Number(number2.value))
          break
        case '/':
          number1.value = String(Number(number1.value) / Number(number2.value))
          break
      }

      display.value = number1.value
      number2.value = ''
      sign.value = ''

      isFinished.value = true
    }

    function clear() {
      number1.value = ''
      number2.value = ''
      sign.value = ''
      display.value = '0'
      isFinished.value = false
    }

    return {
      set,
      clear,
      display
    }
  }
})
</script>

<style lang="scss">
.calc {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
    Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  max-width: 235px;
  background-color: #515151;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px, rgba(0, 0, 0, 0.12) 0px -12px 30px,
    rgba(0, 0, 0, 0.12) 0px 4px 6px, rgba(0, 0, 0, 0.17) 0px 12px 13px,
    rgba(0, 0, 0, 0.09) 0px -3px 5px;

  &__btns {
    display: flex;
  }

  &__display {
    height: 80px;
    background-color: #4b4b4b;
    color: #fff;
    display: flex;
    align-items: flex-end;
    justify-content: flex-end;
    font-size: 50px;
    line-height: 60px;
    box-sizing: border-box;
    padding: 0 16px;
  }

  button {
    font-size: 20px;
    line-height: 22px;
    border: none;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fff;
    padding: 0;
    width: 100%;
    height: 100%;

    &:active {
      box-shadow: rgba(50, 50, 93, 0.25) 0px 30px 60px -12px inset,
        rgba(0, 0, 0, 0.3) 0px 18px 36px -18px inset;
    }
  }

  &__center {
    display: grid;
    grid-gap: 1px;
    grid-template-columns: 58px 58px 58px;
    grid-template-rows: 48px 48px 48px 48px;

    button {
      background-color: #7c7c7c;
    }

    button:nth-child(1),
    button:nth-child(2),
    button:nth-child(3) {
      background-color: #5c5c5e;
    }
  }

  &__bottom {
    display: grid;
    grid-gap: 1px;
    grid-template-columns: 117px 58px;
    grid-template-rows: 48px;
    margin-top: 1px;

    button {
      background-color: #7c7c7c;
    }
  }

  &__right {
    display: grid;
    grid-gap: 1px;
    grid-template-columns: 58px;
    margin-left: 1px;

    button {
      background-color: #efa43b;
    }
  }
}
</style>
