<script setup lang="ts">
import { onMounted } from "vue";
import { storeToRefs } from "pinia";
import HeaderComponent from "../components/HeaderComponent.vue";
import SubscriptionList from "../components/SubscriptionList.vue";
import { useSubscriptionItemStore } from "../stores/SubscriptionItemsStore";

const store = useSubscriptionItemStore();
onMounted(() => {
  store.getSubscriptionItemsFromServer();
});
const { subscriptionItems } = storeToRefs(store);
</script>

<template>
  <HeaderComponent />
  <SubscriptionList :subscription-items="subscriptionItems" />
</template>
