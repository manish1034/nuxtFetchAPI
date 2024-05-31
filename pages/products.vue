<script setup>

// 1st way
// const { pending, data: products } = await useFetch("https://fakestoreapi.com/products");


// 2nd way
// const { pending, data: products, refresh } = useLazyFetch("https://fakestoreapi.com/products", {
// //   lazy: false,
// //   pick: ["id", "title", "image"],   //used in case of only fetching one data instead of array.
//   transform: (products) => {
//     return products.map((product) => ({
//       id: product.id,
//       title: product.title,
//       image: product.image,
//     }));
//   },
// });


// 3rd way
const { pending, data: productInfo, refresh, error } = useAsyncData(
  "productInfo",
  async () => {
    const [products, categories] = await Promise.all([
      $fetch("https://fakestoreapi.com/products"),
      $fetch("https://fakestoreapi.com/products/categories"),
    ]);

    return {
      products,
      categories,
    };
  },
  {
    lazy: false,
    transform: (productInfo) => {
      return {
        categories: productInfo.categories,
        products: productInfo.products.map((product) => ({
        id: product.id,
        title: product.title,
        image: product.image,
      }))
    }},
  }
);

</script>

<template>
  <div v-if="pending">
    <p class="text-center text-lg italic">Loading...</p>
  </div>
  <div v-else-if="error" class="text-center mt-10 text-lg font-thin tracking-wide">{{ error.message }}</div>
  <div v-else>
    <button @click="refresh" class="bg-sky-300 px-3 py-1 rounded text-black/80 mx-4">
      Refresh
    </button>
    <div class="grid grid-cols-5 gap-4 mb-5">
      <div
        v-for="product in productInfo.products"
        class="flex flex-col shadow bg-white p-6 rounded-md"
      >
        <img class="w-[75px] h-auto self-center" :src="product.image" alt="" />
        <h2 class="text-black mt-auto text-sm">{{ product.title }}</h2>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
