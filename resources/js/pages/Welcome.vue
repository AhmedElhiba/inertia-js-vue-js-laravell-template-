<script setup>
import { Link } from "@inertiajs/vue3";
</script>

<template>
    <div class="flex min-h-screen items-center justify-center bg-[#F4FFC3]">
        <div class="w-full max-w-sm rounded-lg bg-white p-8 shadow-md">
            <h1 class="mb-6 text-center text-2xl font-semibold text-[#5D8736]">Welcome to My App</h1>
            <div class="flex justify-center space-x-4">
                <Link 
                    href="/login" 
                    class="px-4 py-2 bg-[#809D3C] text-white rounded hover:bg-[#5D8736]"
                >
                    Login
                </Link>
                <Link 
                    href="/register" 
                    class="px-4 py-2 bg-[#A9C46C] text-white rounded hover:bg-[#809D3C]"
                >
                    Register
                </Link>
            </div>
        </div>
    </div>
</template>
