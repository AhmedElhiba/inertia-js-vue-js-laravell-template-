<template>
<Layout>

</Layout>
</template>

<script setup>
import { Link, router } from "@inertiajs/vue3";
import { route } from 'ziggy-js'; 
import Layout from '@/Layouts/Layout.vue';


// User data is passed as props from the server
const props = defineProps({
    user: Object
});

// Logout function
const logout = () => {
    router.post('/logout');
};
</script>
