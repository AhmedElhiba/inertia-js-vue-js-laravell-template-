<template>
    <div class="flex flex-col min-h-screen">
      <header v-if="showNavbar" ref="navRef" class="fixed top-0 left-0 right-0 z-50">
        <!-- Top Information Bar -->
        <div id="topbar" class="bg-[#f8ffa8] text-[#2c5530] text-sm lg:text-base font-semibold text-center py-2">
          Free Delivery for Orders Over <span class="font-bold">100 DH</span> | We Deliver to All of Morocco | Minimum Order <span class="font-bold">50 DH</span> Required!
        </div>
  
        <nav class="bg-themegreen p-4 shadow-md">
          <div class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto">
            <!-- Logo -->
            <span class="text-2xl font-semibold text-[rgb(239,227,194)] flex items-center">
              <Link href="/">
                <h1 class="text-[#f8ffa8] py-1 px-1">Swiqa</h1>
              </Link>
            </span>
  
            <!-- Mobile menu button -->
            <button
              class="md:hidden inline-flex items-center p-2 ml-3 text-[#f8ffa8] rounded-lg hover:bg-[rgba(248,255,168,0.2)]"
              @click="mobileMenuOpen = !mobileMenuOpen"
            >
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                <path fill-rule="evenodd" d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 15a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1z" clip-rule="evenodd"></path>
              </svg>
            </button>
  
            <!-- Search Bar - Full Width on Mobile, Centered on Desktop -->
            <div class="w-full md:w-3/5 order-3 md:order-2 mt-4 md:mt-0">
              <form @submit.prevent="handleSearch" class="flex items-center">
                <!-- Category Selector -->
                <div class="relative">
                  <select
                    v-model="searchCategory"
                    class="h-10 appearance-none bg-[#f3f3f3] text-gray-700 px-2 md:px-3 pr-6 md:pr-8 rounded-l-md border-r border-gray-300 focus:outline-none hover:bg-[#e6e6e6] cursor-pointer text-sm md:text-base"
                  >
                    <option v-for="category in categories" :key="category" :value="category">
                      {{ category }}
                    </option>
                  </select>
                  <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-1 md:px-2 text-gray-700">
                    <svg class="fill-current h-3 w-3 md:h-4 md:w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                      <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z" />
                    </svg>
                  </div>
                </div>
  
                <!-- Search Input -->
                <input
                  type="text"
                  v-model="searchQuery"
                  class="w-full h-10 px-2 md:px-4 py-2 bg-white border-0 focus:outline-none focus:ring-2 focus:ring-[#f8ffa8] text-sm md:text-base"
                  placeholder="Search Veg, Fruits and more..."
                />
  
                <!-- Search Button -->
                <button
                  type="submit"
                  class="h-10 px-3 md:px-4 bg-[#f8ffa8] hover:bg-[#e6e68f] text-gray-800 font-medium rounded-r-md transition-colors duration-200"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 md:h-5 md:w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                  </svg>
                </button>
              </form>
            </div>
  
            <!-- Desktop Navigation -->
            <div class="hidden md:flex md:space-x-4 items-center justify-end order-2 md:order-3">
              <Link href="/" class="text-[rgb(239,227,194)] hover:text-[rgb(133,169,71)] transition">
                Home
              </Link>
  
              <!-- Cart Icon -->
              <button 
                @click="navigateToCart"
                class="relative p-1 text-[#f8ffa8] hover:text-white transition mr-2"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="text-2xl w-6 h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
                </svg>
                <span 
                  v-if="cartItemsCount > 0" 
                  class="absolute -top-2 -right-2 bg-[#f8ffa8] text-[#2c5530] rounded-full text-xs font-bold w-5 h-5 flex items-center justify-center"
                >
                  {{ cartItemsCount > 99 ? '99+' : cartItemsCount }}
                </span>
              </button>
  
              <div v-if="isAuthenticated" class="flex items-center">
                <Dropdown align="right" width="48">
                  <template #trigger>
                    <span class="inline-flex">
                      <button type="button" class="text-[#f8ffa8] hover:text-white transition uppercase">
                        {{ $page.props.auth.user.name }}
                      </button>
                    </span>
                  </template>
  
                  <template #content>
                    <DropdownLink :href="route('profile.edit')">
                      Profile
                    </DropdownLink>
                    <DropdownLink :href="route('logout')" method="post" as="button">
                      Log Out
                    </DropdownLink>
                  </template>
                </Dropdown>
              </div>
  
              <div v-else class="flex items-center space-x-3">
                <Link :href="route('login')">
                  <button class="px-4 py-1.5 bg-[#f8ffa8] hover:bg-[#e6e68f] text-gray-800 rounded-md transition-colors duration-200 font-medium">
                    Login
                  </button>
                </Link>
                <Link :href="route('register')" class="text-[rgb(239,227,194)] hover:text-[rgb(133,169,71)] transition">
                  Register
                </Link>
              </div>
            </div>
  
            <!-- Mobile Navigation Menu -->
            <div
              ref="menuRef"
              :class="{ block: mobileMenuOpen, hidden: !mobileMenuOpen }"
              class="w-full md:hidden order-4 mt-4 bg-[#2c5530] rounded-lg p-4"
            >
              <div class="flex flex-col space-y-4">
                <Link href="/" class="text-[rgb(239,227,194)] hover:text-[rgb(133,169,71)] transition px-2 py-1">
                  Home
                </Link>
  
                <!-- Mobile Cart Link -->
                <button 
                  @click="navigateToCart"
                  class="flex items-center text-[rgb(239,227,194)] hover:text-[rgb(133,169,71)] transition px-2 py-1"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" class="mr-2 text-xl w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
                  </svg>
                  <span>Cart</span>
                  <span 
                    v-if="cartItemsCount > 0" 
                    class="ml-2 bg-[#f8ffa8] text-[#2c5530] rounded-full text-xs font-bold w-5 h-5 flex items-center justify-center"
                  >
                    {{ cartItemsCount }}
                  </span>
                </button>
  
                <div v-if="isAuthenticated">
                  <div class="flex items-center px-2 py-1 text-[#f8ffa8]">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clip-rule="evenodd" />
                    </svg>
                    <span class="text-[#f8ffa8] ">{{ $page.props.auth.user.name || "User" }}</span>
                  </div>
                  <div class="px-2 py-1">
                    <Link :href="route('logout')" method="post" as="button" class="text-[rgb(239,227,194)] hover:text-[rgb(133,169,71)] transition">
                      Logout
                    </Link>
                  </div>
                </div>
  
                <div v-else>
                  <Link :href="route('login')" class="px-2 py-1">
                    <button class="w-full px-4 py-1.5 bg-[#f8ffa8] hover:bg-[#e6e68f] text-gray-800 rounded-md transition-colors duration-200 font-medium">
                      Login
                    </button>
                  </Link>
                  <Link :href="route('register')" class="text-[rgb(239,227,194)] hover:text-[rgb(133,169,71)] transition px-2 py-1">
                    Register
                  </Link>
                </div>
              </div>
            </div>
          </div>
        </nav>
      </header>
  
      <!-- Create a spacer element with the same height as the navbar -->
      <div v-if="showNavbar" :style="{ height: `${navHeight}px` }"></div>
  
      <!-- Main content without additional padding -->
      <main class="flex-grow">
        <slot />
      </main>
  
      <!-- Footer -->
      <FooterComponent />
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted, onUnmounted, nextTick, watch } from "vue";
  import { usePage, Link, router } from "@inertiajs/vue3";
  import { route } from "ziggy-js";
  
  // Import components
  import Dropdown from "@/Components/Dropdown.vue";
  import DropdownLink from "@/Components/DropdownLink.vue";
//   import FooterComponent from "@/Components/FooterComponent.vue"; // Uncomment if you have this component
  
  // Define props
  const props = defineProps({
    showNavbar: {
      type: Boolean,
      default: true
    }
  });
  
  // State variables
  const navRef = ref(null);
  const menuRef = ref(null);
  const searchQuery = ref("");
  const searchCategory = ref("All");
  const mobileMenuOpen = ref(false);
  const navHeight = ref(0);
  const cartItemsCount = ref(0);
  const categories = ["All", "Veg", "Fruits", "Bio"];
  
  // Check if the user is authenticated
  const page = usePage();
  const isAuthenticated = computed(() => {
    return !!page.props.auth.user;
  });
  
  // Load cart items count from localStorage
  const loadCartItems = () => {
    try {
      const cartItems = JSON.parse(localStorage.getItem('cart')) || [];
      cartItemsCount.value = cartItems.length;
    } catch (error) {
      console.error("Error loading cart items:", error);
      cartItemsCount.value = 0;
    }
  };
  
  // Handle search form submission
  const handleSearch = (e) => {
    console.log(`Searching for: "${searchQuery.value}" in category: ${searchCategory.value}`);
    // You can implement actual search navigation here
    // router.visit(`/search?q=${encodeURIComponent(searchQuery.value)}&category=${encodeURIComponent(searchCategory.value)}`);
  };
  
  // Navigate to cart page
  const navigateToCart = () => {
    router.visit(route('cart')); // Make sure you have a 'cart' route defined
  };
  
  // Update navbar height on mount and window resize
  const updateNavHeight = () => {
    nextTick(() => {
      if (navRef.value) {
        navHeight.value = navRef.value.offsetHeight;
      }
    });
  };
  
  // Watch for changes in localStorage to update cart items count
  watch(() => localStorage.getItem('cart'), () => {
    loadCartItems();
  });
  
  // Lifecycle hooks
  onMounted(() => {
    loadCartItems();
    updateNavHeight();
    window.addEventListener('resize', updateNavHeight);
  });
  
  onUnmounted(() => {
    window.removeEventListener('resize', updateNavHeight);
  });
  </script>