<script setup lang="ts">
import {
  ref,
  reactive,
  computed,
  provide,
  type InjectionKey,
  onMounted,
} from "vue";
import ShopIcon from "./components/Icon/ShopIcon.vue";
import ProductItem from "./components/ProductItem.vue";
import ActionAndFilters from "./components/ActionAndFilters.vue";
import { Theme } from "./keys/Theme";

interface Product {
  id: number;
  title: string;
  price: number;
  inStock: boolean;
  imageUrl: string;
}

const products = ref<Product[]>([
  {
    id: 1,
    title: "纯棉 T 恤",
    price: 66,
    inStock: true,
    imageUrl: "/images/t-shirt1.jpg",
  },
  {
    id: 2,
    title: "夹克外套",
    price: 128,
    inStock: true,
    imageUrl: "/images/jacket1.jpg",
  },
  {
    id: 3,
    title: "水洗牛仔裤",
    price: 99,
    inStock: false,
    imageUrl: "/images/jeans1.jpg",
  },
  {
    id: 4,
    title: "印花 T 恤",
    price: 72,
    inStock: true,
    imageUrl: "/images/t-shirt2.jpg",
  },
]);

type SortDirections = "asc" | "desc" | "";

interface SortAndFilter {
  price: SortDirections;
  name: SortDirections;
  inStock: boolean | null;
}

const sortAndFilter: SortAndFilter = reactive({
  price: "",
  name: "",
  inStock: null,
});

const productResult = computed(() => {
  return products.value
    .filter((p) =>
      sortAndFilter.inStock === null
        ? true
        : p.inStock === sortAndFilter.inStock
    )
    .sort((a, b) => {
      console.log(a, b);

      if (sortAndFilter.price) {
        return sortAndFilter.price === "asc"
          ? a.price - b.price
          : b.price - a.price;
      }
      if (sortAndFilter.name) {
        return sortAndFilter.name === "asc"
          ? a.title.localeCompare(b.title)
          : b.title.localeCompare(a.title);
      }
      return 0;
    });
});

function handleSortByPrice() {
  console.log("handleprice");

  if (sortAndFilter.price === "asc") {
    sortAndFilter.price = "desc";
  } else {
    sortAndFilter.price = "asc";
  }

  sortAndFilter.name = "";
}

function handleSortByName() {
  console.log("handlename");
  if (sortAndFilter.name === "asc") {
    sortAndFilter.name = "desc";
  } else {
    sortAndFilter.name = "asc";
  }

  sortAndFilter.price = "";
}

function handleFilterByStock(inStock: boolean | null) {
  sortAndFilter.inStock = inStock;
}

provide(Theme, "dark");

provide("siteTitle", "kyle");

const actionAndFiltersRef = ref<InstanceType<typeof ActionAndFilters> | null>(
  null
);

onMounted(() => {
  console.log(actionAndFiltersRef.value);
});
</script>

<template>
  <main>
    <h1><ShopIcon />K y l e 的 商 店</h1>
    <ActionAndFilters
      @sortByPrice="handleSortByPrice"
      @sortByName="handleSortByName"
      @filterByStock="handleFilterByStock"
      ref="actionAndFiltersRef"
    />
    <div class="productList">
      <ProductItem
        v-for="product in productResult"
        :key="product.id"
        v-bind="product"
      />
    </div>
  </main>
</template>
<style scoped>
main {
  display: grid;
  place-items: center;
  gap: 48px;
  width: 100vw;
  padding: 24px;
}

h1 {
  display: flex;
  align-items: center;
  gap: 32px;
}

h1 svg {
  width: 64px;
  height: 64px;
}
.productList {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 48px;
  width: 60%;
}
</style>
