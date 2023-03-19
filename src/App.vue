<script setup>
import TheHeader from "@/components/TheHeader.vue";
import ProductCard from "@/components/ProductCard.vue";
import { useProductStore } from "@/stores/ProductStore";
import { useCartStore } from "@/stores/CartStore";
import AppButton from "@/components/AppButton.vue";
// import { reactive } from "vue";
const productStore = useProductStore();
const cartStore = useCartStore();

/* All the code of history for Undo/Redo is moved to a PiniaHistoryPlugin.js */
// const doingHistory = ref(false)

// Create a history of states
// const history = reactive([])
// Go all the way down to the beginning of the state
// history.push(JSON.stringify(cartStore.$state))

// const undo = () => {
//   if (history.length === 1 ) return
//   doingHistory.value = true
//   history.pop()
//   cartStore.$state = JSON.parse(history.at(-1))
//   doingHistory.value = false
// }

// Subscribe to the State
// cartStore.$subscribe(({ mutation, state }) => {
  // if (!doingHistory.value){
  //   history.push(JSON.stringify(state));
  // }
  // console.log({ mutation });
  // console.log({ state });
//   history.push(JSON.stringify(state))
// })

cartStore.$onAction(({ name, store, args, after, onError }) => {
  if (name === "addItems") {
    after((/* result */) => {
      // This returns undefined because our action doesn't return anything:
      // console.log(result);
      console.log(args[0]);
    });
    // The error is still uncaught though
    onError((error) => {
      console.log("Hello error: ", error.message);
    });
  }
});
productStore.fill();

// Mutate a store with a callback function
// cartStore.$patch((state) => {
//   for (let i = 0; i < count; i++){
//     cartStore.items.push({ product })
//   }
// })
</script>

<template>
  <div class="container">
    <TheHeader />
    <div class="mb-5 flex justify-end">
      <AppButton @click="cartStore.undo">Undo</AppButton>
      <AppButton class="ml-2" @click="cartStore.redo">Redo</AppButton>
    </div>
    <ul class="sm:flex flex-wrap lg:flex-nowrap gap-5">
      <ProductCard
        v-for="product in productStore.products"
        :key="product.name"
        :product="product"
        @addToCart="cartStore.addItems($event, product)"
      />
    </ul>
  </div>
</template>
