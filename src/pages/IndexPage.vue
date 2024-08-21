<template>
  <q-page>
    <div :class="!isMobile ? 'row' : 'column'">
      <div
        class="col-3 column"
        :class="isMobile ? 'mobile-background' : 'background-container'"
      >
        <q-img
          :class="isMobile ? 'mobile-front-img q-ml-md' : 'front-img'"
          src="/images/bg-card-front.png"
        >
          <CardLogo
            :class="
              isMobile
                ? 'q-my-sm q-mx-md  mobile-logo'
                : 'q-mt-lg q-mb-sm q-mx-lg card-logo'
            "
          />
          <q-input
            v-model="cardNumber"
            readonly
            dense
            borderless
            placeholder="0000 0000 0000 0000"
            input-class="text-center text-weight-medium text-white "
            :style="
              isMobile
                ? 'font-size: 24px; margin-top: 0px'
                : 'font-size: 34px; margin-top: 35px'
            "
          >
          </q-input>
          <div
            class="row bg-transparent items-center"
            :class="isMobile ? 'q-ml-xs' : 'q-ml-md'"
          >
            <q-input
              v-model="cardholderName"
              readonly
              dense
              borderless
              input-class="text-uppercase text-weight-bold text-white"
              placeholder="JANE APPLESEED"
            ></q-input>
            <span
              :style="isMobile ? 'margin-left: 53px' : 'margin-left: 175px'"
              >{{ showExpDate }}</span
            >
          </div>
        </q-img>
        <q-img
          :class="isMobile ? 'mobile-back-img' : 'back-img'"
          src="/images/bg-card-back.png"
        >
          <q-input
            v-model="cvc"
            :class="isMobile ? 'cvc-mobile' : 'cvc q-mr-xl'"
            dense
            readonly
            borderless
            placeholder="000"
            input-class="text-right text-weight-medium text-white"
          >
          </q-input>
        </q-img>
      </div>

      <div class="col" style="position: relative">
        <q-form
          v-if="!submited"
          @submit.prevent="submitForm"
          class="text-primary text-weight-medium column q-gutter-md"
          :class="!isMobile ? 'absolute-center' : 'fixed-center'"
          :style="isMobile ? 'margin-top: 150px' : ''"
          style="max-width: 370px; min-width: 300px"
        >
          <div class="row">
            <div class="q-mb-md q-gutter-y-sm col-12">
              <label for="name">CARDHOLDER NAME</label>
              <q-input
                v-model="cardholderName"
                input-class="text-capitalize text-weight-medium "
                placeholder="e.g. Jane Appleseed"
                outlined
                dense
                id="name"
                :rules="[validateRequired]"
                @keypress="filterInput"
              />
            </div>
          </div>
          <div class="row">
            <div class="q-mb-md q-gutter-y-sm col-12">
              <label for="number">CARD NUMBER</label>
              <q-input
                v-model="cardNumber"
                mask="#### #### #### ####"
                input-class="text-weight-medium"
                placeholder="e.g. 1234 5678 9123 0000"
                outlined
                dense
                id="number"
                :rules="[validateRequired]"
              />
            </div>
          </div>
          <div class="row q-mb-md">
            <div class="col-5">
              <label for="year">EXP. DATE (MM/YY)</label>
            </div>
            <div class="col q-ml-sm q-mb-sm">
              <label for="cvc">CVC</label>
            </div>

            <div class="row" style="gap: 10px">
              <div class="col-2">
                <q-input
                  v-model="expDateMonth"
                  mask="##"
                  input-class="text-weight-medium"
                  placeholder="MM"
                  outlined
                  dense
                  id="month"
                  :rules="[validateRequired]"
                />
              </div>
              <div class="col-2">
                <q-input
                  v-model="expDateYear"
                  mask="##"
                  input-class="text-weight-medium"
                  placeholder="YY"
                  outlined
                  dense
                  id="year"
                  :rules="[validateRequired]"
                />
              </div>

              <div class="col q-ml-md">
                <q-input
                  v-model="cvc"
                  mask="###"
                  input-class="text-weight-medium"
                  placeholder="e.g. 123"
                  outlined
                  dense
                  id="cvc"
                  :rules="[validateRequired]"
                />
              </div>
            </div>
          </div>
          <q-btn
            type="submit"
            class="q-pa-sm"
            color="primary"
            label="Confirm"
            no-caps
          />
        </q-form>
        <div
          :class="
            isMobile
              ? 'column flex-center q-mt-xl'
              : 'column flex-center absolute-center'
          "
          v-else
        >
          <SubmitPage />
          <p class="q-mt-lg text-h4 text-primary">THANK YOU!</p>
          <p class="q-mb-lg text-subtitle1 text-grey-6">
            We've added your card details
          </p>
          <q-btn
            @click="resetForm"
            color="primary"
            label="Continue"
            no-caps
            style="width: 300px"
          />
        </div>
      </div>
    </div>
  </q-page>
</template>

<style scoped>
.background-container {
  position: relative;
  background-image: url("/images/bg-main-desktop.png");
  height: 100vh;
  background-size: cover;
  background-position: center;
}

.mobile-background {
  position: relative;
  background-image: url("/images/bg-main-mobile.png");
  height: 300px;
  background-size: cover;
  background-position: center;
}

.front-img {
  top: 22%;
  left: 40%;
  width: 440px;
}

.mobile-front-img {
  position: absolute;
  top: 50%;
  width: 300px;
  z-index: 10;
}
.mobile-back-img {
  position: relative;
  top: 19%;
  left: 14%;
  width: 300px;
}
.back-img {
  top: 25%;
  left: 55%;
  width: 440px;
}
.cvc {
  margin-top: 99px;
}

.cvc-mobile {
  margin-top: 61px;
  margin-right: 35px;
}

.card-logo {
  width: 100px;
  height: auto;
}

.mobile-logo {
  width: 70px;
}
</style>

<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue";
import CardLogo from "/src/components/CardLogo.vue";
import SubmitPage from "./SubmitPage.vue";

/*
Refs
*/

const cardholderName = ref("");
const cardNumber = ref("");
const expDateMonth = ref("");
const expDateYear = ref("");
const cvc = ref("");
const submited = ref(false);
const isMobile = ref(false);

/*
Validations
*/

const validateRequired = (val) => {
  return !!val || "Can't be blank";
};

/*
computed properties
*/

const showExpDate = computed(() => {
  if (!expDateMonth.value) return "00/00";
  else return `${expDateMonth.value}/${expDateYear.value}`;
});

const filterInput = (e) => {
  const char = String.fromCharCode(e.keyCode);
  if (!/[A-Za-z\s]/.test(char)) {
    e.preventDefault();
  }
};

const submitForm = () => {
  submited.value = !submited.value;
};

const resetForm = () => {
  submited.value = !submited.value;
  cardholderName.value = "";
  cardNumber.value = "";
  expDateMonth.value = "";
  expDateYear.value = "";
  cvc.value = "";
};

const windowWidth = ref(window.innerWidth);

const updateWidth = () => {
  windowWidth.value = window.innerWidth;
  if (window.innerWidth < 1440) isMobile.value = true;
  else isMobile.value = false;
};

onMounted(() => {
  window.addEventListener("resize", updateWidth);
});

onUnmounted(() => {
  window.removeEventListener("resize", updateWidth);
});
</script>
