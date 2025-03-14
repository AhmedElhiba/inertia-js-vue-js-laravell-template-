<template>
    <div class="flex flex-col lg:flex-row bg-gray-100">
      <!-- Filter Sidebar -->
      <div class="w-full lg:w-1/4 p-5 lg:min-h-screen bg-white shadow-md">
        <div class="sticky top-5">
          <!-- Search Bar -->
          <div class="mb-6">
            <div class="relative">
              <input
                type="text"
                placeholder="Search products..."
                class="w-full p-3 pl-10 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-themegreen"
                v-model="searchTerm"
                @input="handleSearchChange"
              />
              <FaSearch class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" />
            </div>
          </div>
  
          <div class="flex items-center gap-2 mb-6">
            <FaFilter class="text-themegreen" />
            <h2 class="text-xl font-bold">Filters</h2>
          </div>
  
          <div class="mb-6">
            <h3 class="text-lg font-semibold mb-3">Categories</h3>
            <div class="flex flex-col gap-2">
              <button
                v-for="(category, index) in categories"
                :key="index"
                class="text-left p-2 rounded-md transition-all"
                :class="selectedCategory === category ? 'bg-themegreen text-white' : 'hover:bg-gray-100'"
                @click="setSelectedCategory(category)"
              >
                {{ category }}
              </button>
            </div>
          </div>
        </div>
      </div>
  
      <!-- Products Grid -->
      <div class="w-full lg:w-3/4 lg:px-10 px-5 py-10">
        <div class="flex justify-between items-center mb-6">
          <h1 data-aos="zoom-in" data-aos-delay="100" class="text-themesage text-xl font-semibold">
            Browse Collection {{ selectedCategory !== 'All' ? `- ${selectedCategory}` : '' }}
          </h1>
          <p class="text-gray-500">{{ filteredProducts.length }} products found</p>
        </div>
  
        <div v-if="loading">Loading...</div>
        <div v-else-if="filteredProducts.length > 0" data-aos="zoom-in" data-aos-delay="300" class="grid lg:grid-cols-4 md:grid-cols-2 grid-cols-1 gap-6">
          <div
            v-for="item in filteredProducts"
            :key="item.id"
            class="flex flex-col justify-center items-center gap-2 bg-white p-3 rounded-lg cursor-pointer relative transform-gpu hover:scale-105 transition-transform duration-300 ease-in-out hover:shadow-lg"
          >
            <img :src="item.img" :alt="item.name" class="w-full h-56 object-cover rounded-lg" />
            <div class="flex justify-center items-center gap-3 absolute top-[10px] left-0 right-0 mx-auto">
              <div class="bg-themegreen hover:bg-themesage hover:text-black rounded-full p-2 text-white">
                <MdOutlineRemoveRedEye />
              </div>
              <div class="bg-themegreen hover:bg-themesage hover:text-black rounded-full p-2 text-white">
                <FaRegHeart />
              </div>
              <div
                class="bg-themegreen hover:bg-themesage hover:text-black rounded-full p-2 text-white"
                @click="addToCart(item)"
              >
                <MdAddShoppingCart />
              </div>
            </div>
            <h1 class="text-md text-gray-400 font-semibold">{{ item.category }}</h1>
            <h1 class="text-lg text-black font-semibold">{{ item.name }}</h1>
            <h1 class="text-lg text-themegreen font-bold">{{ item.price }}</h1>
            <div class="w-full mt-2">
              <hr />
              <div class="flex justify-between items-center gap-4 mt-3">
                <div class="flex justify-start items-center gap-1">
                  <FaStar v-for="i in 5" :key="i" class="text-themegreen" />
                </div>
                <button
                  @click="addToCart(item)"
                  :class="`px-4 py-2 rounded-lg text-sm font-bold transform-gpu hover:scale-105 transition-transform duration-300 ease-in-out ${isInCart(item) ? 'bg-red-500' : 'bg-green-500'} text-white`"
                >
                  {{ isInCart(item) ? 'Remove From Cart' : 'Add To Cart' }}
                </button>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="flex flex-col items-center justify-center py-16 text-center">
          <div class="text-6xl mb-4">🔍</div>
          <h2 class="text-2xl font-bold mb-2">No products found</h2>
          <p class="text-gray-500 mb-6">Try adjusting your search or filter criteria</p>
          <button
            class="bg-themegreen text-white px-6 py-3 rounded-lg font-bold"
            @click="clearFilters"
          >
            Clear all filters
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import AOS from 'aos';
  import { FaStar, FaRegHeart, FaSearch, FaFilter } from 'react-icons/fa';
  import { MdOutlineRemoveRedEye, MdAddShoppingCart } from 'react-icons/md';
  import axios from 'axios';
  import { ref, onMounted } from 'vue';
  
  export default {
    components: {
      FaStar,
      FaRegHeart,
      FaSearch,
      FaFilter,
      MdOutlineRemoveRedEye,
      MdAddShoppingCart
    },
    setup() {
      const cartItems = ref([]);
      const categories = ref([]);
      const selectedCategory = ref('All');
      const searchTerm = ref('');
      const products = ref([]);
      const loading = ref(true);
  
      onMounted(() => {
        const storedCart = JSON.parse(localStorage.getItem('cart')) || [];
        cartItems.value = storedCart;
  
        axios
          .get('http://127.0.0.1:8000/api/products')
          .then((response) => {
            products.value = response.data;
            const uniqueCategories = ['All', ...new Set(response.data.map(product => product.category))];
            categories.value = uniqueCategories;
            loading.value = false;
          })
          .catch((error) => {
            console.error('Error fetching products:', error);
            loading.value = false;
          });
  
        AOS.init({
          offset: 100,
          duration: 500,
          easing: 'ease-in-out',
        });
      });
  
      const addToCart = (product) => {
        let cart = JSON.parse(localStorage.getItem('cart')) || [];
        const productIndex = cart.findIndex(item => item.id === product.id);
        let updatedCart;
  
        if (productIndex !== -1) {
          updatedCart = cart.filter(item => item.id !== product.id);
        } else {
          updatedCart = [...cart, product];
        }
  
        localStorage.setItem('cart', JSON.stringify(updatedCart));
        cartItems.value = updatedCart;
        window.dispatchEvent(new Event('cartUpdated'));
      };
  
      const filteredProducts = computed(() => {
        return products.value.filter(product => {
          const matchesCategory = selectedCategory.value === 'All' || product.category === selectedCategory.value;
          const matchesSearch = product.name.toLowerCase().includes(searchTerm.value.toLowerCase());
          return matchesCategory && matchesSearch;
        });
      });
  
      const handleSearchChange = (e) => {
        searchTerm.value = e.target.value;
      };
  
      const clearFilters = () => {
        searchTerm.value = '';
        selectedCategory.value = 'All';
      };
  
      const isInCart = (item) => {
        return cartItems.value.some(product => product.id === item.id);
      };
  
      return {
        cartItems,
        categories,
        selectedCategory,
        searchTerm,
        products,
        loading,
        addToCart,
        filteredProducts,
        handleSearchChange,
        clearFilters,
        isInCart
      };
    }
  };
  </script>
  
  <style scoped>
  /* Add your styles here */
  </style>