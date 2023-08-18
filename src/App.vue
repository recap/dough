<template>
  <div class="container w3-blue w3-cell">
    <label class="labels">Desired dough weight gr</label>
    <input class="w3-input"  type="number" v-model="desiredDoughWeight"  @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Hydration %</label>
    <input class="w3-input" type="number" v-model="waterPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Starter % </label>
    <input class="w3-input" type="number" v-model="culturePercentage" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Salt %</label>
    <input class="w3-input" type="number" v-model="saltPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Flour composition ratio %</label>
    <input class="w3-input" type="text" v-model="flourComposition" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Egg %</label>
    <input class="w3-input"  type="number" v-model="eggPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Butter %</label>
    <input class="w3-input" type="number" v-model="butterPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Sugar %</label>
    <input class="w3-input"  type="number" v-model="sugarPercentage" @change="calculate" @paste="calculate" @keyup="calculate">
    <label class="labels">Flour temperature &#8451;</label>
    <input class="w3-input"  type="number" v-model="flourTemperature" @change="calculate" @paste="calculate" @keyup="calculate">
  </div>

  <div class= "container w3-red w3-cell">
    <label class="labels">Total flour gr</label>
    <input class="w3-input w3-yellow"  type="number" v-model.lazy="flourWeight" @change="reverseCalculate" @paste="reverseCalculate" @keyup="reverseCalculate">
    <label class="labels">Water gr</label>
    <input class="w3-input w3-yellow" type="number" v-model.lazy="waterWeight">
    <label class="labels">Starter gr</label>
    <input class="w3-input w3-yellow" type="number" v-model.lazy="cultureWeight">
    <label class="labels">Salt gr</label>
    <input class="w3-input w3-yellow" type="number" v-model.lazy="saltWeight">
    <label class="labels">Flour composition weights gr</label>
    <input class="w3-input w3-gray" type="text"  v-model="flourCompositionWeight"  readonly>
    <label class="labels">Egg gr</label>
    <input class="w3-input w3-yellow" type="number" v-model.lazy="eggWeight"> 
    <label class="labels">Butter gr</label>
    <input class="w3-input w3-yellow" type="number" v-model.lazy="butterWeight">
    <label class="labels">Sugar gr</label>
    <input class="w3-input w3-yellow" type="number" v-model.lazy="sugarWeight"> 
    <label class="labels">Water temperature &#8451;</label>
    <input class="w3-input w3-gray" type="number" v-model="waterTemperature" readonly> 
  </div>
</template>

<style>
  .labels {
    font-size: 12px;
  }
  .container {
    padding-right: 25px;
    padding-left: 10px;
    padding-bottom: 10px;
  }
</style>

<script setup>
    import { ref, onMounted, computed } from 'vue'
    
    const desiredDoughWeight = ref(1200)
    const waterPercentage = ref(70)
    const eggPercentage = ref(0)
    const culturePercentage = ref(20)
    const butterPercentage = ref(0)
    const saltPercentage = ref(2)
    const sugarPercentage = ref(0)
    const flourComposition = ref('40:30:30')
    const flourTemperature = ref(17)

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
    const waterTemperature = computed({
        get() {
          // Calculate water temperature so that end dough is 24 deg celcius.
          // tf = (m1 cp1 t1 + m2 cp2 t2 + .... + mn cpn tn) / (m1 cp1 + m2 cp2 + .... + mn cpn)
          // where
          // tf = final mixed temperature (oC)
          // m = mass of substance (kg)
          // cp = specific heat of substance (J/kgoC)
          // t = temperature of substance (oC)
          // cp for water =   4186J/kgoC, cp for flour is 1590J/kgoC

          const tf = 24
          const cp2 = 1590
          const m2 = flourWeight.value + cultureWeight.value
          const t2 = flourTemperature.value
          const cp1 = 4186
          const m1 = waterWeight.value
          const t1 = (tf*m1*cp1 + m2*cp2*(tf - t2)) / (m1*cp1)
          return Math.round(t1)
        }
    })
    const flourCompositionWeight = computed({
        get(){
          if(100 != flourComposition.value.split(':').reduce((a,b) => parseInt(a) + parseInt(b), 0)) {
            return "WRONG RATIOS!"
          } else {
            return flourComposition.value.split(':')
                                                .map((f) => Math.round((f * flourWeight.value) / 100 ))
                                                .join(':')
          }
        }
    })

    function reverseCalculate() {
      const p = (v) => v.value / 100
      desiredDoughWeight.value = Math.round(flourWeight.value * (p(waterPercentage) + p(eggPercentage) + p(culturePercentage) + p(butterPercentage) + p(saltPercentage) + p(sugarPercentage)) + flourWeight.value)
      calculate()
    }

    function calculate() {
      flourWeight.value = Math.round((desiredDoughWeight.value * 100) / (waterPercentage.value + culturePercentage.value + saltPercentage.value + eggPercentage.value + butterPercentage.value + sugarPercentage.value + 100))
    }

    onMounted(() => {
      calculate()
    })

</script>
