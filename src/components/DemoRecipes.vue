<template>
  <div
    class="bg-primary-light mt-8 w-4/5 p-8 flex justify-center items-center rounded-2xl">
    <div class="bg-white rounded-2xl w-full">
      <!-- Begin Global Recipe Render -->
      <div>
        <div class="overflow-hidden bg-white shadow sm:rounded-2xl">
          <ul role="list" class="divide-y divide-gray-200">
            <li v-for="recipe in globalRecipes" :key="recipe.id">
              <a
                href="#"
                class="block hover:bg-gray-50"
                @click="(open = true), (modalRecipe = recipe)">
                <div class="p-4 sm:px-6">
                  <div class="flex items-center justify-between">
                    <p class="text-primary truncate text-sm font-medium">
                      {{ recipe.title }}
                    </p>
                    <div class="ml-2 flex flex-shrink-0">
                      <p
                        class="bg-primary-light text-primary-muted inline-flex rounded-full px-2 mx-2 text-xs font-semibold leading-5">
                        {{ recipe.category }}
                      </p>
                      <p
                        class="bg-blue-100 text-blue-800 inline-flex rounded-full px-2 mx-2 text-xs font-semibold leading-5 italic">
                        {{ recipe.profiles.username }}
                      </p>
                    </div>
                  </div>
                  <div class="mt-2 sm:flex sm:justify-between">
                    <div class="sm:flex">
                      <p class="flex items-center text-sm text-gray-500">
                        {{ recipe.description }}
                      </p>
                    </div>
                  </div>
                </div>
              </a>
            </li>
          </ul>
        </div>
      </div>
      <!-- End Global Recipe Render -->
    </div>
  </div>
  <TransitionRoot as="template" :show="open">
    <Dialog as="div" class="relative z-10" @close="open = false">
      <TransitionChild
        as="template"
        enter="ease-out duration-300"
        enter-from="opacity-0"
        enter-to="opacity-100"
        leave="ease-in duration-200"
        leave-from="opacity-100"
        leave-to="opacity-0">
        <div
          class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />
      </TransitionChild>

      <div class="fixed inset-0 z-10 overflow-y-auto">
        <div
          class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
          <TransitionChild
            as="template"
            enter="ease-out duration-300"
            enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            enter-to="opacity-100 translate-y-0 sm:scale-100"
            leave="ease-in duration-200"
            leave-from="opacity-100 translate-y-0 sm:scale-100"
            leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95">
            <DialogPanel
              class="relative transform overflow-hidden rounded-lg bg-white px-4 pt-5 pb-4 text-left shadow-xl transition-all sm:my-8 w-full mx-24 p-6">
              <div class="absolute top-0 right-0 hidden pt-4 pr-4 sm:block">
                <button
                  type="button"
                  class="rounded-md bg-white text-gray-400 hover:text-gray-500 focus:outline-none"
                  @click="open = false">
                  <span class="sr-only">Close</span>
                  <XMarkIcon class="h-6 w-6" aria-hidden="true" />
                </button>
              </div>
              <div>
                <div class="mt-3 text-start sm:mt-5">
                  <DialogTitle
                    as="h3"
                    class="text-3xl leading-6 text-gray-900 font-bold"
                    >{{ modalRecipe.title }}
                    <span class="font-normal text-sm italic ml-6">
                      {{ modalRecipe.profiles.username }}</span
                    ></DialogTitle
                  >
                  <p class="text-sm text-gray-500 my-4">
                    Category: {{ modalRecipe.category }}
                  </p>
                  <p class="text-sm text-gray-500">
                    Description: {{ modalRecipe.description }}
                  </p>
                  <div class="mt-2">
                    <span class="font-bold">Ingredients:</span>
                    <span
                      v-for="ingredient in modalRecipe.ingredients"
                      :key="ingredient.id">
                      <p class="text-sm text-gray-500">
                        {{ ingredient.amount }} {{ ingredient.units.name }}
                        {{ ingredient.ingredient }}
                      </p>
                    </span>
                  </div>
                  <div class="mt-2">
                    <span class="font-bold">Instructions:</span>
                    <span
                      v-for="instruction in modalRecipe.instructions"
                      :key="instruction.id">
                      <span
                        v-for="(step, index) in instruction.instructions"
                        :key="index">
                        <ol class="text-sm text-gray-500">
                          <li>{{ index + 1 + '.' }} {{ step }}</li>
                        </ol>
                      </span>
                    </span>
                  </div>
                </div>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>

<script setup>
import { supabase } from '../supabase';
import { onMounted, ref } from 'vue';
import {
  Dialog,
  DialogPanel,
  DialogTitle,
  TransitionChild,
  TransitionRoot,
} from '@headlessui/vue';
import { XMarkIcon } from '@heroicons/vue/24/outline';

const open = ref(false);
const modalRecipe = ref({});
const globalRecipes = ref([]);
const loading = ref(true);
onMounted(() => {
  getRecipes();
});

async function getRecipes() {
  try {
    loading.value = true;

    let {
      data: recipes,
      error,
      status,
    } = await supabase
      .from('recipes')
      .select(
        'id, title, description, global, active, user_id, category, profiles(username), ingredients(id, ingredient, amount, units(name)), instructions(id, instructions)'
      )
      .eq('global', true);

    if (error && status !== 406) throw error;
    if (recipes) {
      globalRecipes.value = recipes;
    }
  } catch (error) {
    alert(error.message);
  } finally {
    loading.value = false;
  }
}
</script>