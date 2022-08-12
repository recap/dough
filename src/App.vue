<template>
<div class="w3-container w3-cell w3-padding-small">
	<label class="labels">Final dough weight</label>
	<input class="w3-input"  type="number" v-model="finalDoughWeight"  @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Hydration %</label>
	<input class="w3-input" type="number" v-model="waterPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Starter % </label>
	<input class="w3-input" type="number" v-model="culturePercentage" @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Salt %</label>
	<input class="w3-input" type="number" v-model="saltPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Flour composition</label>
	<input class="w3-input" type="text" v-model="flourComposition" @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Egg %</label>
	<input class="w3-input"  type="number" v-model="eggPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Butter %</label>
	<input class="w3-input" type="number" v-model="butterPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
	<label class="labels">Sugar %</label>
	<input class="w3-input"  type="number" v-model="sugarPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
</div>

<div class="w3-container w3-cell w3-padding-small">
	<label class="labels">Total Flour </label>
	<input class="w3-input w3-yellow"  type="number" v-model.lazy="flourWeight" @change="reverseCalculate" @paste="reverseCalculate" @keyup="reverseCalculate">
	<label class="labels">Water </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="waterWeight">
	<label class="labels">Starter  </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="cultureWeight">
	<label class="labels">Salt </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="saltWeight">
	<label class="labels">Flour composition</label>
	<input class="w3-input w3-gray" type="text"  v-model="flourCompositionWeight"  readonly>
	<label class="labels">Egg </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="eggWeight"> 
	<label class="labels">Butter </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="butterWeight">
	<label class="labels">Sugar </label>
	<input class="w3-input w3-yellow" type="number" v-model.lazy="sugarWeight"> 
</div>
</template>

<style>
.labels {
	font-size: 12px;
}
</style>

<script setup>
    import { ref, onMounted, computed } from 'vue'
    const finalDoughWeight = ref(1200)

    const waterPercentage = ref(75)
    const eggPercentage = ref(0)
    const culturePercentage = ref(20)
    const butterPercentage = ref(0)
    const saltPercentage = ref(2)
    const sugarPercentage = ref(0)
    const flourComposition = ref('40:30:30')

    const flourWeight = ref(0)
    const computedTemplate = (refObject) => {
      return {
        get() {
          return Math.round(refObject.value / 100 * flourWeight.value)
        },
        set(newValue) {
          flourWeight.value = (newValue / refObject.value) * 100
          reverseCalculate()
        }
      }
    }
    const waterWeight = computed(computedTemplate(waterPercentage))
    const saltWeight = computed(computedTemplate(saltPercentage))
    const eggWeight = computed(computedTemplate(eggPercentage))
    const butterWeight = computed(computedTemplate(butterPercentage))
    const sugarWeight = computed(computedTemplate(sugarPercentage))
    const cultureWeight = computed(computedTemplate(culturePercentage))
    const flourCompositionWeight = ref('')


    function reverseCalculate() {
      const p = (v) => v.value / 100
      finalDoughWeight.value = Math.round(flourWeight.value * (p(waterPercentage) + p(eggPercentage) + p(culturePercentage) + p(butterPercentage) + p(saltPercentage) + p(sugarPercentage)) + flourWeight.value)
      calculate()
      console.log(`final dough weight: ${finalDoughWeight.value}`)
    }

    function calculate() {
      flourWeight.value = Math.round((finalDoughWeight.value * 100) / (waterPercentage.value + culturePercentage.value + saltPercentage.value + eggPercentage.value + butterPercentage.value + sugarPercentage.value + 100))
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
