<template>
  <div class="calculator">
      <DisplayT :value="DisplayValue" />
      <ButtonT label="AC" triple @onCalcButtonClick="clearMemory" />
      <ButtonT label="/" operation @onCalcButtonClick="setOperation" />
      <ButtonT label="7" @onCalcButtonClick="addDigit" />
      <ButtonT label="8" @onCalcButtonClick="addDigit" />
      <ButtonT label="9" @onCalcButtonClick="addDigit" />
      <ButtonT label="*" operation @onCalcButtonClick="setOperation" />
      <ButtonT label="4" @onCalcButtonClick="addDigit" />
      <ButtonT label="5" @onCalcButtonClick="addDigit" />
      <ButtonT label="6" @onCalcButtonClick="addDigit" />
      <ButtonT label="-" operation @onCalcButtonClick="setOperation" />
      <ButtonT label="1" @onCalcButtonClick="addDigit" />
      <ButtonT label="2" @onCalcButtonClick="addDigit"/>
      <ButtonT label="3" @onCalcButtonClick="addDigit" />
      <ButtonT label="+" operation @onCalcButtonClick="setOperation" />
      <ButtonT label="0" double @onCalcButtonClick="addDigit" />
      <ButtonT label="." @onCalcButtonClick="addDigit"/>
      <ButtonT label="=" operation @onCalcButtonClick="setOperation" />

  </div>
  
</template>

<script>
import ButtonT from "../components/ButtonT.vue"
import DisplayT from "../components/DisplayT.vue"

export default {
    data: function(){
        return {
            DisplayValue: "0",
            clearDisplay: false,
            operation: null,
            values: [0, 0],
            current: 0
        }
    },
    components: { ButtonT, DisplayT },

    methods: {
        clearMemory(){
            Object.assign(this.$data, this.$options.data())
        },
        setOperation(operation){
            if (this.current === 0){
                this.operation = operation
                this.current = 1
                this.clearDisplay = true
            }else{
                const equals = operation === "="
                const currentOperation = this.operation

                try{
                    this.values[0] = eval( `${this.values[0]} ${currentOperation} ${this.values[1]}`);
                    
                    if (isNaN(this.values[0]) 
                    || !isFinite(this.values[0])) {               
                    this.clearMemory(); 
                    return;
                    }

                    } catch(e){
                        this.$emit('onError', e)
                    }
                    this.values[1] = 0

                    this.DisplayValue = this.values[0]
                    this.operation = equals ? null : operation
                    this.current = equals ? 0 : 1
                    this.clearDisplay = !equals
                }           
        },
        addDigit(n){
            if (n == "." && this.DisplayValue.includes(".")){
                return
            }
            const clearDisplay = this.DisplayValue === "0" 
            || this.clearDisplay
            const currentValue = clearDisplay ? "": this.DisplayValue
            const DisplayValue = currentValue + n

            this.DisplayValue = DisplayValue
            this.clearDisplay = false

            if (n !== "."){
                const i = this.current
                const newValue = parseFloat(DisplayValue)
                this.values[i] = newValue
            }
        }
    }

}

</script>

<style>

.calculator{
    height: 320px;
    width: 235px;
    border-radius: 5px;
    overflow: hidden;
    display: grid;
    grid-template-columns: repeat(4, 25%);
    grid-template-rows: 1fr 48px 48px 48px 48px 48px;
}
</style>