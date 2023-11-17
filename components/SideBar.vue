<template>
  <aside
    class="hidden lg:fixed lg:inset-y-0 lg:z-50 lg:flex lg:w-72 lg:flex-col"
  >
    <!-- Sidebar component, swap this element with another sidebar if you like -->
    <div class="flex grow flex-col gap-y-5 overflow-y-auto bg-white px-3 pb-4">
      <div class="flex h-16 shrink-0 items-center text-black font-bold">
        <img
          class="h-8 w-auto"
          src="https://tailwindui.com/img/logos/mark.svg?color=white"
          alt="Your Company"
        />
        Mazrz Finance
      </div>
      <nav class="flex flex-1 flex-col">
        <ul role="list" class="flex flex-1 flex-col gap-y-7">
          <li>
            <ul role="list" class="-mx-2 space-y-4">
              <li v-for="item in navigation" :key="item.name">
                <NuxtLink
                  :href="item.href"
                  :class="[
                    item.current
                      ? 'bg-primary/10 text-primary '
                      : 'text-gray-300 ',
                    'border-l-4 border-primary group flex gap-x-3  p-2 text-sm leading-6 font-semibold ',
                  ]"
                >
                  <component
                    :is="item.icon"
                    :class="[
                      item.current
                        ? 'text-primary'
                        : 'text-indigo-200 group-hover:text-white',
                      'h-6 w-6 shrink-0',
                    ]"
                    aria-hidden="true"
                  />
                  {{ item.name }}
                </NuxtLink>
              </li>
            </ul>
          </li>
        </ul>
      </nav>
    </div>
  </aside>
</template>

<script setup>
import { HomeIcon, UsersIcon } from "@heroicons/vue/24/outline";
const navigation = [
  { name: "Dashboard", href: "/", icon: HomeIcon, current: false },

  {
    name: "Form Templates",
    href: "form-template",
    icon: UsersIcon,
    current: true,
  },
];
</script>
