<script setup>
import Checkbox from '@/Components/Checkbox.vue';
import Layout from '@/Layouts/Layout.vue'; // Uncommented
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';
import { route } from 'ziggy-js'; 
import { router } from '@inertiajs/vue3'; // Import router for navigation

defineProps({
    canResetPassword: Boolean,
    status: String,
});

const form = useForm({
    email: '',
    password: '',
    remember: false,
});

const submit = () => {
    form.post(route('login'), {
        onFinish: () => {
            form.reset('password');
            router.visit(route('home')); 
        },
    });
};


</script>

<template>
            <Head title="Log in" />

<div class="flex min-h-screen items-center justify-center ">
    <div>
    <button class="bg-themegreen rounded-e p4 py-4 px-4 flex m-2 ">
        <h1 class="text-[#ffff]" ><a class="text-whaite" href="/">SWIQA</a></h1>
    </button>
</div>
    <div class="w-full bg-[#F4FFC3] max-w-sm rounded-lg bg-white p-8 shadow-md">
        <h2 class="mb-6 text-center text-2xl font-semibold text-[#5D8736]">Sign in</h2>
        
        <div v-if="status" class="mb-4  text-sm font-medium text-green-600">
            {{ status }}
        </div>

        <form @submit.prevent="submit">
            <div class="">
                <InputLabel for="email" value="Email" class="text-[#5D8736]" />
                <TextInput
                    id="email"
                    type="email"
                    class="mt-1 block w-full border-[#A9C46C] focus:border-[#5D8736] focus:ring-[#5D8736]"
                    v-model="form.email"
                    required
                    autofocus
                    autocomplete="username"
                />
                <InputError class="mt-2 text-red-500" :message="form.errors.email" />
            </div>

            <div class="mt-4">
                <InputLabel for="password" value="Password" class="text-[#5D8736]" />
                <TextInput
                    id="password"
                    type="password"
                    class="mt-1 block w-full border-[#A9C46C] focus:border-[#5D8736] focus:ring-[#5D8736]"
                    v-model="form.password"
                    required
                    autocomplete="current-password"
                />
                <InputError class="mt-2 text-red-500" :message="form.errors.password" />
            </div>

            <div class="mt-4 flex items-center">
                <Checkbox name="remember" v-model:checked="form.remember" class="text-[#5D8736]" />
                <span class="ml-2 text-sm text-gray-700">Remember me</span>
            </div>

            <div class="mt-4 flex flex-col space-y-4">
                <PrimaryButton
                    class="w-full bg-[#809D3C] py-2 text-white hover:bg-[#5D8736] disabled:opacity-50"
                    :class="{ 'opacity-50': form.processing }"
                    :disabled="form.processing"
                >
                    Log in
                </PrimaryButton>
                
                <Link
                    v-if="canResetPassword"
                    :href="route('password.request')"
                    class="text-center text-sm text-[#5D8736] hover:underline"
                >
                    Forgot your password?
                </Link>
            </div>
        </form>
    </div>
</div>
    <!-- <Layout>
    </Layout> -->
</template>