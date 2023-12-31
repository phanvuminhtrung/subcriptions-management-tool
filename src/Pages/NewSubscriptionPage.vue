<script setup lang="ts">
import HeaderComponent from "../components/HeaderComponent.vue";
import { ref } from "vue";
import VueDatePicker from "@vuepic/vue-datepicker";
import "@vuepic/vue-datepicker/dist/main.css";
import { enAU } from "date-fns/locale";
import { collection, addDoc } from "firebase/firestore";
import { useFirebaseDataStore, uploadFiles } from "../firebase";
import { v4 as uuidv4 } from "uuid";

const name = ref("");
const date = ref(null);
const images = ref([]);
const loading = ref(false);
const snackbar = ref(false);
const snackbarMsg = ref("");
const snackbarColor = ref("");

const nameRules = [
  (name: string) => {
    if (name?.length >= 3) return true;
    return "Subscription name must be at least 3 characters.";
  },
];

const isAllDataCorrect = () => {
  let isCorrect = true;
  if (name.value.length < 3) {
    isCorrect = false;
  }

  if (date.value === null) {
    isCorrect = false;
  }
  return isCorrect;
};

const submit = async () => {
  if (isAllDataCorrect()) {
    loading.value = true;
    const { db } = useFirebaseDataStore();
    const imageName = `${uuidv4().replaceAll("-", "")}.${(
      images.value[0] as File
    ).name
      .split(".")
      .pop()}`;
    await addDoc(collection(db, "subscriptions"), {
      name: name.value,
      date: date.value,
      imageName: imageName,
    });

    uploadFiles(images.value[0], imageName);
    loading.value = false;
    snackbar.value = true;
    snackbarMsg.value = "Adding a new subscription successful!";
    snackbarColor.value = "success";
  } else {
    snackbar.value = true;
    snackbarMsg.value = "Adding a new subscription failed!";
    snackbarColor.value = "red-darken-2";
  }
};
</script>

<template>
  <HeaderComponent />
  <v-sheet width="300" class="mx-auto">
    <v-form fast-fail validate-on="blur" @submit.prevent="submit">
      <v-text-field
        v-model="name"
        label="Subscription name"
        :rules="nameRules"
      />
      <VueDatePicker
        v-model="date"
        :enable-time-picker="false"
        :format-locale="enAU"
        :clearable="true"
        placeholder="Select Date"
        required
        format="dd/MM/yyyy"
      />
      <v-file-input
        v-model="images"
        accept="image/png, image/jpeg, image/gif"
        label="File input"
        class="mt-5"
        prepend-icon="mdi-camera"
      ></v-file-input>

      <v-btn :loading="loading" type="submit" block class="mt-2">Submit</v-btn>
    </v-form>
    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarMsg }}
    </v-snackbar>
  </v-sheet>
</template>