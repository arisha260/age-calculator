<script setup>
import { ref } from "vue";

const isActive = ref(0);
const inputValue = ref("");
const age = ref(null);
const today = new Date();

const day = today.getDate();
const month = today.getMonth() + 1;
const year = today.getFullYear();

// Переключение вкладки
const toggleActive = (tabIndex) => {
  isActive.value = tabIndex;
};

// Применение маски для ввода даты
const applyMask = (event) => {
  let value = event.target.value.replace(/\D/g, "");
  if (value.length > 2) value = value.slice(0, 2) + "." + value.slice(2);
  if (value.length > 5) value = value.slice(0, 5) + "." + value.slice(5);
  value = value.slice(0, 10);
  inputValue.value = value;
};

// Вычисление возраста
const calcDOB = (date) => {
  if (date === "") return;

  const userDate = new Date(date.split(".").reverse().join("-"));

  if (isNaN(userDate)) {
    console.log("Invalid date format");
    return;
  }

  let calculatedAge = today.getFullYear() - userDate.getFullYear();

  const hasBirthdayPassed = today.getMonth() > userDate.getMonth() ||
      (today.getMonth() === userDate.getMonth() && today.getDate() >= userDate.getDate());

  if (!hasBirthdayPassed) {
    calculatedAge--;
  }

  age.value = calculatedAge;
  toggleActive(1);
};

const handleKeydown = (event) => {
  if (event.key === "Enter") {
    event.preventDefault(); // Предотвращаем стандартное поведение
    calcDOB(inputValue.value); // Вызываем функцию расчета возраста
  }
};
</script>

<template>
  <div class="calculator">
    <div class="container calculator__container">
      <!-- Вкладка 1 -->
      <div class="calculator__tab first-tab" :class="{ active: isActive === 0 }">
        <h1 class="calculator__title">Your date of birth</h1>
        <p class="calculator__text">The date you entered: {{ inputValue }}</p>
        <p class="calculator__text">Today: {{ `${day}.${month}.${year}` }} </p>
        <div class="input">
          <input type="text" @input="applyMask" id="date" class="input-reset calculator__input" @keydown="handleKeydown" v-model="inputValue"/>
          <label for="date" class="calculator__label" :class="{ active: inputValue !== '' }">
            Enter your D.O.B in format: MM.DD.YYYY
          </label>
        </div>
        <button class="btn-reset calculator__btn" @keydown.enter="calcDOB(inputValue)" @click="calcDOB(inputValue)">Calculate Age</button>
      </div>

      <!-- Вкладка 2 -->
      <div class="calculator__tab second-tab" :class="{ active: isActive === 1 }">
        <h1 class="calculator__title">Your age</h1>
        <p class="calculator__text">{{ age }}</p>
        <button class="btn-reset calculator__btn" @click="toggleActive(0)">Back</button>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
  .calculator{
    height: 100vh;
    padding: 0 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #1b1b1b;
    &__container{
      width: 100%;
      max-width: 800px;
      min-height: 50vh;
      background-color: var(--dark-color);
      border-radius: 50px;
      position: relative;
      box-shadow: 8px 8px #ff0099;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    &__title{
      margin: 0;
      font-family: var(--font-family);
      font-size: 50px;
      color: var(--light-color);
    }
    &__input{
      padding: 20px;
      font-size: 20px;
      outline: none;
      border-radius: 15px;
      font-family: var(--second-family);
    }
    &__label{
      font-family: var(--second-family);
      position: absolute;
      top: 50%;
      left: 20px;
      transform: translateY(-50%);
      font-size: 20px;
      color: var(--dark-color);
      transition: .3s;
      &.active{
        display: none;
      }
    }
    &__btn{
      font-family: var(--second-family);

      color: var(--dark-color);
      background-color: #ff0099;
      border-radius: 10px;
      padding: 10px 20px;
      font-size: 30px;
      transition: .3s;
      &:hover{
        color: var(--light-color);
      }
    }
    &__text{
      margin: 0;
      font-family: var(--second-family);
      color: var(--light-color);
      font-size: 40px;
    }
    &__tab{
      padding: 50px 10px;
      display: none;
      width: 100%;
      height: 100%;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      text-align: center;
      gap: 30px;
      &.active{
        display: flex;
      }
    }
  }

  .input{
    width: 100%;
    max-width: 500px;
    margin-top: 20px;
    display: flex;
    flex-direction: column;
    position: relative;
  }

  .calculator__input:focus~.calculator__label {
    top: 10px;
    font-size: 15px;
  }
</style>