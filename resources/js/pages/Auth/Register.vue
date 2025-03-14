<script setup>
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';
import { route } from 'ziggy-js'; 

const form = useForm({
    name: '',
    email: '',
    password: '',
    password_confirmation: '',
});

const submit = () => {
    form.post(route('register'), {
        onFinish: () => form.reset('password', 'password_confirmation'),
    });
};
</script>

<template>
    <Head title="Register" />

    <div class="flex min-h-screen items-center justify-center">
        <div>
    <button class="bg-themegreen rounded-e p4 py-4 px-4 flex m-2 ">
        <h1 class="text-[#ffff]" ><a class="text-whaite" href="/">SWIQA</a></h1>
    </button>
</div>
        <div class="w-full max-w-sm rounded-lg bg-[#F4FFC3] p-8 shadow-md">
            <h2 class="mb-6 text-center text-2xl font-semibold text-[#5D8736]">Create Account</h2>
            
            <form @submit.prevent="submit">
                <div>
                    <InputLabel for="name" value="Name" class="text-[#5D8736]" />
                    <TextInput
                        id="name"
                        type="text"
                        class="mt-1 block w-full border-[#A9C46C] focus:border-[#5D8736] focus:ring-[#5D8736]"
                        v-model="form.name"
                        required
                        autofocus
                        autocomplete="name"
                    />
                    <InputError class="mt-2 text-red-500" :message="form.errors.name" />
                </div>

                <div class="mt-4">
                    <InputLabel for="email" value="Email" class="text-[#5D8736]" />
                    <TextInput
                        id="email"
                        type="email"
                        class="mt-1 block w-full border-[#A9C46C] focus:border-[#5D8736] focus:ring-[#5D8736]"
                        v-model="form.email"
                        required
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
                        autocomplete="new-password"
                    />
                    <InputError class="mt-2 text-red-500" :message="form.errors.password" />
                </div>

                <div class="mt-4">
                    <InputLabel for="password_confirmation" value="Confirm Password" class="text-[#5D8736]" />
                    <TextInput
                        id="password_confirmation"
                        type="password"
                        class="mt-1 block w-full border-[#A9C46C] focus:border-[#5D8736] focus:ring-[#5D8736]"
                        v-model="form.password_confirmation"
                        required
                        autocomplete="new-password"
                    />
                    <InputError class="mt-2 text-red-500" :message="form.errors.password_confirmation" />
                </div>

                <div class="mt-4 flex flex-col space-y-4">
                    <PrimaryButton
                        class="w-full bg-[#809D3C] py-2 text-white hover:bg-[#5D8736] disabled:opacity-50"
                        :class="{ 'opacity-50': form.processing }"
                        :disabled="form.processing"
                    >
                        Register
                    </PrimaryButton>
                    
                    <Link
                        :href="route('login')"
                        class="text-center text-sm text-[#5D8736] hover:underline"
                    >
                        Already have an account? Sign in
                    </Link>
                </div>
            </form>
        </div>
    </div>
</template>
