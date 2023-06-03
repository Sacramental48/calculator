<template>
    <div class="main">
        <BackgroundAnimationVue></BackgroundAnimationVue>
        <div class="main__calculator calculator">
            <div class="calculator__display display">
                <input type="text" class="display__input-main" v-model="display" disabled @keydown="handleKeyDown(event)">
            </div>
            <div class="calculator__button">
                <button
                v-for="btn in buttons"
                :key="btn"
                :class="`button button__${btn.style || 'default'}`"
                @click="updateInputDisplay(btn.value)"
                >
                    {{btn.value}}
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import BackgroundAnimationVue from '../UI/BackgroundAnimation.vue';

const display = ref(0);
const currentNumber = ref(0);
const flag = ref(false);
const pendingOperations = ref('')

const buttons = ref([
    {value: 'C', style: 'colored'}, {value: '%', style: 'colored'}, {value: '\u232B', style: 'backspace colored'}, {value: '/', style: 'colored'},
    
    {value: '7'}, {value: '8'}, {value: '9'}, {value: '\u00D7', style: 'colored'},

    {value: '4',}, {value: '5',}, {value: '6',}, {value: '\u2212', style: 'colored'},
    
    {value: '1',}, {value: '2',}, {value: '3',}, {value: '+', style: 'colored'},

    {value: '00'}, {value: '0'}, {value: '.'}, {value: '=', style: 'colored'},
]);

function updateInputDisplay(value) {
    if(value === '+' || value === '\u2212' || value === '\u00D7' || value === '/' || value === '=') {
        operations(value);
    } else if (value === '.') {
        decimal();
    } else if (value === '%') {
        percent();
    } else if (value === 'C') {
        currentNumber.value = 0;
        pendingOperations.value = '';
        clearDisplay();
    } else if (value === '\u232B') {
        try {
            display.value = display.value.slice(0, -1);
            if(display.value === '0' || display.value === '') {
                display.value = '0'
            }
        } catch(e) {
            console.log('error');
        }
    } else {
        numPussed(value);
    }
}

function operations(value) {
    let digits = display.value;
    if (flag.value && pendingOperations.value !== "="){   
        console.log('cccc', currentNumber.value)
        display.value = currentNumber.value;
    } else {
        flag.value = true;
        if ( '+' === pendingOperations.value ) {
            currentNumber.value += parseFloat(digits);
        }
        else if ( '\u2212' === pendingOperations.value ) {
            currentNumber.value -= parseFloat(digits);
        }
        else if ( '/' === pendingOperations.value ) {
            currentNumber.value /= parseFloat(digits);
        }
        else if ( '\u00D7' === pendingOperations.value ) { 
            currentNumber.value *= parseFloat(digits);
        }
        else {
            currentNumber.value = parseFloat(digits);
        }
        display.value = currentNumber.value;
        pendingOperations.value = value;
    }
}

function numPussed(value) {
    if (flag.value){
			display.value = value;
			flag.value = false;
    } else {
        if (display.value == "0") {
            display.value = value;
        } else if(display.value == '00') {
            display.value = value;
        }
        else {
            display.value += value;
        }
    }
}

function decimal (){
		let currentDigit = display.value;
		if (flag.value) {
			currentDigit = "0.";
			flag.value = false;
		} else {
			if (currentDigit.indexOf(".") == -1)
				currentDigit += ".";
            }
		display.value = currentDigit;
}

function percent (){
    display.value = (parseFloat(display.value) / 100) * parseFloat(currentNumber.value);
}

function clearDisplay () {
    display.value = '0';
    flag.value = true;
}

</script>

<style scoped lang="scss">
@import './style.scss';
</style>