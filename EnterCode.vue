<script lang="ts" setup>
import { debounce } from 'quasar';
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

defineOptions({
  name: 'EnterCode',
});

const router = useRouter();

let wrongOtp = ref<boolean>(false);
let timeOut = ref<boolean>(false);
// Reactive state for OTP inputs (4 boxes)
let otp = ref<string[]>(['', '', '', '']); // Array for each digit

// Refs for inputs
const inputs = ref<(HTMLInputElement | null)[]>([]); 

// Handle input event to move to next box
const handleInput = (index: number) => {
  if (otp.value[index] && index < otp.value.length - 1) {
    inputs.value[index + 1]?.focus();
  }
};

// Handle backspace to focus the previous box
const handleBackspace = (index: number) => {
  if (!otp.value[index] && index > 0) {
    inputs.value[index - 1]?.focus();
  }
};

// Handle arrow key navigation
const handleArrowkeys = (event: KeyboardEvent, index: number) => {
  if (event.key === 'ArrowRight' && index < otp.value.length - 1) {
    inputs.value[index + 1]?.focus();
  } else if (event.key === 'ArrowLeft' && index > 0) {
    inputs.value[index - 1]?.focus();
  }
};

// Focus the first input box when the component is mounted
onMounted(() => {
  if (inputs.value[0]) {
    (inputs.value[0] as HTMLElement).focus();
  }
});

const handleSubmit = debounce(() => {

  // Check OTP expary
  // timeOut.value = true

  const code: number = 1234;
  const submittedCode = otp.value.join('');

  if (code.toString() === submittedCode) {
    router.push('/reset-password');
  } else {
    wrongOtp.value = true
  }
}, 1000);
</script>

<template>
  <q-card class="q-mb-xl" style="max-width: 400px; width: 100%">
    <q-card-section>
      <div class="text-h6">Enter Code</div>
      <p class="text-dark">Please enter the code send to your email</p>
      <p class="text-negative" v-if="timeOut">OTP timeout, Please try again</p>
      <p class="text-negative" v-if="wrongOtp">Wrong OTP, Please try again</p>

      <div class="row q-col-gutter-md justifi-center no-wrap">
        <q-input
          v-for="(digit, index) in otp"
          :key="index"
          v-model="otp[index]"
          ref="inputs"
          type="text"
          maxlength="1"
          dense
          input-class="text-center"
          hide-bottom-space
          @update:model-value="handleInput(index)"
          @keyup.backspace="handleBackspace(index)"
          @keyup="handleArrowkeys($event, index)"
        />
      </div>
      <q-btn
        type="submit"
        label="Submit"
        color="primary"
        class="q-mt-md full-width"
        @click="handleSubmit"
      />
    </q-card-section>
  </q-card>
</template>
