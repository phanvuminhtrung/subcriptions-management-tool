<script setup lang="ts">
import SubscriptionCard from "./SubscriptionCard.vue";
import { Card } from "./types";
import { PropType } from "vue";

defineProps({
  subscriptionItems: { type: Array as PropType<Card[]>, default: () => [] },
});
</script>

<template>
  <v-container>
    <v-row no-gutters>
      <v-col
        v-for="(item, index) in subscriptionItems"
        :key="index"
        cols="12"
        sm="4"
      >
        <v-sheet class="ma-2 pa-2">
          <SubscriptionCard
            :card="{
              id: item.id,
              title: item.title,
              date: item.date,
              imageUrl: item.imageUrl,
            }"
          />
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped></style>
