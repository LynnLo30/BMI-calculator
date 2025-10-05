<script setup>
  import { ref, computed } from 'vue'

  const userHeight = ref(null)
  const userWeight = ref(null)
  const isShow = ref(false)
  const result = ref({
    BMI: null,
    minWeight: null,
    maxWeight: null
  })
  const currentGrade = ref(null)

  const BMIGrades = [
    {
      label: "underweight",
      text: '體重過輕',
      rangeTxt: '<small>BMI </small>&lt; 18.5'
    },
    {
      label: "normalWeight",
      text: '健康體重',
      rangeTxt: '18.5 &le;<small> BMI </small>&lt; 24 '
    },
    {
      label: "overweight",
      text: '體重過重',
      rangeTxt: '24 &le;<small> BMI </small>&lt; 27'
    },
    {
      label: "obesity",
      text: '肥胖',
      rangeTxt: '<small>BMI </small>&ge; 27'
    }
  ]

  const heightInMetersSquared = computed(() => (userHeight.value / 100) ** 2)

  function roundTo1dp(num) {
    return Math.round(num * 10) / 10
  }

  function calculateBMI() {
    const BMI = roundTo1dp(userWeight.value / heightInMetersSquared.value)
    const minWeight = roundTo1dp(18.5 * heightInMetersSquared.value)
    const maxWeight = roundTo1dp(24 * heightInMetersSquared.value - 0.1)

    result.value = { BMI, minWeight, maxWeight }
  }

  function getBMIGrade(bmi) {
    if (bmi < 18.5) {
      return 'underweight'
    } else if (bmi >= 18.5 && bmi < 24) {
      return 'normalWeight'
    } else if (bmi >= 24 && bmi < 27) {
      return 'overweight'
    } else if (bmi >= 27) {
      return 'obesity'
    }
  }

  function showResult() {
    if (userHeight.value > 40 && userWeight.value > 2) {
      calculateBMI()
      currentGrade.value = getBMIGrade(result.value.BMI)
      isShow.value = true
    } else {
      alert('請輸入正確的身高與體重')
    }
  }

  function cleanData() {
    [userHeight, userWeight, currentGrade].forEach(i => i.value = null)
    result.value = { BMI: null, minWeight: null, maxWeight: null }
    isShow.value = false
  }
</script>

<template>
  <div class="container position-relative vh-100 d-flex justify-content-center align-items-center">
    <div class="row justify-content-evenly">
      <div class="calculator-box col-12 col-md-6 card p-5">
        <div class="bmi-header mb-3">
          <p class="title fs-3">身體質量指數 (BMI) 計算器</p>
          <p class="description">世界衛生組織(WHO)指出以身高與體重計算出的身體質量指數(Body Mass
            Index，簡稱BMI)是最常用於評估體位的一項簡易測量指標。</p>
        </div>
        <form class="calculator-form">
          <div class="mb-3">
            <label for="height" class="form-label">身高（公分/ cm）</label>
            <input v-model.number="userHeight" type="number" class="form-control" id="height"
              min="40" max="272" placeholder="請輸入身高">
          </div>
          <div class="mb-5">
            <label for="weight" class="form-label">體重（公斤/ kg）</label>
            <input v-model.number="userWeight" type="number" class="form-control" id="weight"
              min="2" max="635" placeholder="請輸入體重">
          </div>
          <div class="actions-control mx-auto">
            <button @click.prevent="showResult" type="submit"
              class="btn btn-primary mx-3">開始計算</button>
            <button @click="cleanData" type="reset" class="btn btn-secondary mx-3">全部清除</button>
          </div>
        </form>
      </div>
      <div class="result-box col-12 col-md-4 d-flex flex-column justify-content-between">
        <div class="grade-card d-flex flex-column justify-content-evenly">
          <div class="card" v-for="grade in BMIGrades" :key="grade.label" :class="[
            grade.label,
            currentGrade ? (currentGrade === grade.label ? '-highlight' : 'd-none') : ''
          ]">
            <div class="card-body row align-items-center mx-1">
              <p class="card-title col-6 col-md-12 col-lg-6 m-0 numberStyle"
                v-html="grade.rangeTxt"></p>
              <p class="card-text col-6 col-md-12 col-lg-6 text-center">{{ grade.text }}</p>
            </div>
          </div>
        </div>
        <div v-if="isShow" class="resultData mx-auto">
          <p class="fz-6 mb-2">你的 BMI 為
            <span class="userBMI numberStyle">{{ result.BMI }}</span>
          </p>
          <p class="fz-6">正常體重範圍：
            <span class="idealWeight numberStyle">
              {{ result.minWeight }} ～ {{ result.maxWeight }}
            </span>
          </p>
        </div>
        <figure class="caption-text">
          <p class="text-secondary">18歲（含）以上成人BMI範圍值及體重對照表</p>
          <figcaption class="blockquote-footer">
            資料來源： <cite title="Source Title">衛生福利部國民健康署</cite>
          </figcaption>
        </figure>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
  @keyframes slide-in {
    0% {
      opacity: 0;
      transform: translateY(20px);
    }

    60% {
      opacity: 1;
      transform: translateY(-5px);
    }

    100% {
      transform: translateY(0);
    }
  }

  .-highlight {
    animation: slide-in 0.8s ease-in-out;
  }

  .numberStyle {
    font-family: "Noto Sans Math", sans-serif;
    font-size: 1.22rem;
  }

  .actions-control {
    width: fit-content;
  }

  .grade-card {
    height: 80%;

    >.card {
      min-height: 4.5rem;

      .card-title {
        padding: 0 .5rem;

        @include media-breakpoint-down(lg) {
          text-align: center;
        }
      }
    }
  }

  .resultData {
    position: absolute;
    top: 55%;
    left: 65%;
    animation: slide-in 0.8s ease-in-out;
  }

  .caption-text,
  .caption-text>* {
    margin: 0;
    font-size: 0.8rem;
  }
</style>
