<script setup>
import { ref } from 'vue';
import { supabase } from '../supabase';
import LogoWhite from '../assets/cookbook-white.svg?component';

const loading = ref(false);
const email = ref('');

const handleLogin = async () => {
  try {
    loading.value = true;
    const { error } = await supabase.auth.signInWithOtp(
      {
        email: email.value,
      },
      {
        redirectTo: window.location.origin,
      }
    );
    if (error) throw error;
    alert('Check your email for the login link!');
  } catch (error) {
    if (error instanceof Error) {
      alert(error.message);
    }
  } finally {
    loading.value = false;
  }
};
</script>

<template>
  <div
    class="bg-gray-900 flex min-h-full flex-col justify-center items-center py-12 px-6 lg:px-8 w-full md:w-1/2 rounded-lg my-28">
    <div class="sm:mx-auto sm:w-full sm:max-w-md w-3/4">
      <div class="w-full flex flex-row justify-center items-center">
        <LogoWhite class="w-24 h-auto" />
      </div>
      <h2 class="mt-6 text-center text-3xl font-bold tracking-tight text-white">
        Sign in via magic link with your email below
      </h2>
    </div>

    <div class="mt-8 sm:mx-auto w-full">
      <div class="bg-primary-white py-8 px-4 shadow rounded-lg sm:px-10 w-full">
        <form class="space-y-6" @submit.prevent="handleLogin">
          <div>
            <label for="email" class="block text-sm font-medium text-gray-700"
              >Email address</label
            >
            <div class="mt-1">
              <input
                id="email"
                name="email"
                type="email"
                v-model="email"
                autocomplete="email"
                required=""
                class="block w-full appearance-none text-gray-900 rounded-md border border-gray-300 px-3 py-2 placeholder-gray-400 shadow-sm focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm" />
            </div>
          </div>
          <div>
            <input
              type="submit"
              class="font-bold text-white bg-gradient-to-r from-teal-500 to-cyan-600 hover:italic my-2 flex w-full text-center justify-center rounded-md border border-transparent py-2 px-4 text-sm shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 cursor-pointer"
              :value="loading ? 'Loading' : 'Send magic link'"
              :disabled="loading" />
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
