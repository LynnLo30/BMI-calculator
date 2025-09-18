<script setup>
  import { ref, computed } from 'vue'

  const userHeight = ref(null)
  const userWeight = ref(null)

  const result = ref({
    BMI: null,
    minWeight: null,
    maxWeight: null
  })

  const heightInMetersSquared = computed(() => (userHeight.value / 100) ** 2)

  function roundTo1dp(num) {
    return Math.round(num * 10) / 10
  }

  function calculateBMI() {
    const BMI = roundTo1dp(userWeight.value / heightInMetersSquared.value)
    const [minWeight, maxWeight] = [18.5, 24].map(factor => roundTo1dp(factor * heightInMetersSquared.value))

    result.value = { BMI, minWeight, maxWeight }
  }

  const cleanData = () => {
    userHeight.value = null
    userWeight.value = null
    result.value = { BMI: null, minWeight: null, maxWeight: null }
  }
</script>

<template>
  <div class="container position-relative">
    <div class="row justify-content-center">
      <div class="calculator-box col-6 card p-5">
        <div class="bmi-header mb-3">
          <h3 class="title">身體質量指數 (BMI) 計算器</h3>
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
            <button @click.prevent="calculateBMI" type="submit"
              class="btn btn-primary mx-3">開始計算</button>
            <button @click="cleanData" type="reset" class="btn btn-secondary mx-3">全部清除</button>
          </div>
        </form>
      </div>
      <div class="result-box col-4 d-flex flex-column">
        <div class="grade-card">
          <div class="underweight card">
            <div class="card-body row align-items-center mx-1">
              <p class="card-title col">BMI<span class="numberStyle"> &lt; 18.5</span></p>
              <p class="card-text col text-center">體重過輕</p>
            </div>
          </div>
          <div class="normalWeight card">
            <div class="card-body row align-items-center mx-1">
              <p class="card-title col"><span class="numberStyle">18.5 &le; </span>BMI<span
                  class="numberStyle"> &lt; 24</span>
              </p>
              <p class="card-text col text-center">健康體重</p>
            </div>
          </div>
          <div class="overweight card">
            <div class="card-body row align-items-center mx-1">
              <p class="card-title col"><span class="numberStyle">24 &le; </span>BMI<span
                  class="numberStyle"> &lt; 27</span>
              </p>
              <p class="card-text col text-center">體重過重</p>
            </div>
          </div>
          <div class="obesity card">
            <div class="card-body row align-items-center mx-1">
              <p class="card-title col">BMI<span class="numberStyle"> &ge; 27</span></p>
              <p class="card-text col text-center">肥胖</p>
            </div>
          </div>
        </div>
        <div class="resultData mx-auto">
          <h6>你的 BMI 為 <span class="userBMI numberStyle">{{ result.BMI }}</span></h6>
          <h6>正常體重範圍：<span class="idealWeight numberStyle">{{ result.minWeight }} ～ {{
            result.maxWeight }}</span>
          </h6>
        </div>
        <figure class="caption-text position-absolute bottom-0">
          <p class="text-secondary">18歲（含）以上成人BMI範圍值及體重對照表</p>
          <figcaption class="blockquote-footer">
            資料來源： <cite title="Source Title">衛生福利部國民健康署</cite>
          </figcaption>
        </figure>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
  // * {
  //   outline: 1px solid salmon;
  // }

  .numberStyle {
    font-family: "Noto Sans Math", sans-serif;
    font-size: 1.22rem;
  }

  .actions-control {
    width: fit-content;
  }

  .grade-card {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    height: 65vh;

    .card {
      min-height: 4.5rem;

      .card-title {
        margin: 0;
      }
    }
  }

  .caption-text,
  .caption-text>* {
    margin: 0;
    font-size: 0.8rem;
  }
</style>
