<template>
<div id="app">
<div class="w3-container w3-cell w3-padding-small">
  <p>
	<label class="labels">Final dough weight</label>
	<input class="w3-input"  type="number" v-model="finalDoughWeight"  @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Hydration %</label>
	<input class="w3-input" type="number" v-model="waterPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Starter % </label>
	<input class="w3-input" type="number" v-model="culturePercentage" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Salt %</label>
	<input class="w3-input" type="number" v-model="saltPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Flour composition</label>
	<input class="w3-input" type="text" v-model="flourComposition" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Egg %</label>
	<input class="w3-input"  type="number" v-model="eggPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Butter %</label>
	<input class="w3-input" type="number" v-model="butterPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
  <p>
	<label class="labels">Sugar %</label>
	<input class="w3-input"  type="number" v-model="sugarPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
  </p>
</div>

<div class="w3-container w3-cell w3-padding-small">
  <p>
	<label class="labels">Total Flour </label>
	<input class="w3-input w3-yellow"  type="number" v-model.lazy="flourWeight" @change="reverseCalculate" @paste="reverseCalculate" @keyup="reverseCalculate">
  </p>
  <p>
	<label class="labels">Water </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="waterWeight" @change="reverseItem('water')" @paste="reverseItem('water')" @keyup="reverseItem('water')">
  </p>
  <p>
	<label class="labels">Starter  </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="cultureWeight" @change="reverseItem('culture')" @paste="reverseItem('culture')" @keyup="reverseItem('culture')">
  </p>
  <p>
	<label class="labels">Salt </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="saltWeight" @change="reverseItem('salt')" @paste="reverseItem('salt')" @keyup="reverseItem('salt')">
  </p>
  <p>
	<label class="labels">Flour composition</label>
	<input class="w3-input w3-gray" type="text"  v-model="flourCompositionWeight"  readonly>
  </p>
  <p>
	<label class="labels">Egg </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="eggWeight" @change="reverseItem('egg')" @paste="reverseItem('egg')" @keyup="reverseItem('egg')">
  </p>
  <p>
	<label class="labels">Butter </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="butterWeight" @change="reverseItem('butter')" @paste="reverseItem('butter')" @keyup="reverseItem('butter')">
  </p>
  <p>
	<label class="labels">Sugar </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="sugarWeight" @change="reverseItem('sugar')" @paste="reverseItem('sugar')" @keyup="reverseItem('sugar')">
  </p>
</div>
</div>
</template>

<style>
.labels {
	font-size: 12px;
}
</style>

<script setup>
    import { ref, onMounted } from 'vue'
    const finalDoughWeight = ref(1200)

    const waterPercentage = ref(75)
    const eggPercentage = ref(0)
    const culturePercentage = ref(20)
    const butterPercentage = ref(0)
    const saltPercentage = ref(2)
    const sugarPercentage = ref(0)
    const flourComposition = ref('40:30:30')

    const flourWeight = ref(0)
    const waterWeight = ref(0)
    const saltWeight = ref(0)
    const eggWeight = ref(0)
    const butterWeight = ref(0)
    const sugarWeight = ref(0)
    const cultureWeight = ref(0)
    const flourCompositionWeight = ref('')

    const registry = {
      sugar: {
        percentage: sugarPercentage,
        weight: sugarWeight
      },
      culture: {
        percentage: culturePercentage,
        weight: cultureWeight
      },
      salt: {
        percentage: saltPercentage,
        weight: saltWeight
      },
      water: {
        percentage: waterPercentage,
        weight: waterWeight
      },
      egg: {
        percentage: eggPercentage,
        weight: eggWeight
      },
      butter: {
        percentage: butterPercentage,
        weight: butterWeight
      }
    }


    function reverseItem(itemName) {
      const item = registry[itemName]
      if(!item) {
        return
      }
      flourWeight.value = (item.weight.value / item.percentage.value) * 100
      console.log("rev water: ", flourWeight.value)
      reverseCalculate()
    }

    function reverseCalculate() {
      const p = (v) => v.value / 100
      finalDoughWeight.value = Math.round(flourWeight.value * (p(waterPercentage) + p(eggPercentage) + p(culturePercentage) + p(butterPercentage) + p(saltPercentage) + p(sugarPercentage)) + flourWeight.value)

      calculate()

      console.log(`final dough weight: ${finalDoughWeight.value}`)
    }

    function calculate() {
      flourWeight.value = Math.round((finalDoughWeight.value * 100) / (waterPercentage.value + culturePercentage.value + saltPercentage.value + eggPercentage.value + butterPercentage.value + sugarPercentage.value + 100))
      const convertToWeight = (refItem) => Math.round(refItem.value / 100 * flourWeight.value)
      waterWeight.value = convertToWeight(waterPercentage)
      cultureWeight.value = convertToWeight(culturePercentage)
      saltWeight.value = convertToWeight(saltPercentage)
      eggWeight.value = convertToWeight(eggPercentage)
      sugarWeight.value = convertToWeight(sugarPercentage)
      butterWeight.value = convertToWeight(butterPercentage)

      const sum = flourComposition.value.split(':').reduce((a,b) => parseInt(a) + parseInt(b), 0)

      if(sum != 100) {
        flourCompositionWeight.value = "WRONG RATIOS!"
      }else {
        flourCompositionWeight.value = flourComposition.value
                                            .split(':')
                                            .map((f) => Math.round((f * flourWeight.value) / 100 ))
                                            .join(':')
            }
      
    }

    onMounted(() => {
      calculate()
    })

</script>
