<template>
  <!-- Header component / ヘッダーコンポーネント -->
  <header class="flex justify-between items-center p-3 h-14 shadow bg-white">
    <!-- Button to toggle sidebar / サイドバーを切り替えるボタン -->
    <button @click="emit('toggle-sidebar')"
            class="flex items-center justify-center rounded transition-colors w-8 h-8 text-gray-700 hover:bg-black/10">
      <MenuIcon class="w-6"/>
    </button>
    <!-- User menu / ユーザーメニュー -->
    <Menu as="div" class="relative inline-block text-left">
      <MenuButton class="flex items-center">
        <!-- User avatar / ユーザーアバター -->
        <img src="https://randomuser.me/api/portraits/men/1.jpg" class="rounded-full w-8 mr-2">
        <!-- User name / ユーザー名 -->
        <small>{{currentUser.name}}</small>
        <!-- Dropdown icon / ドロップダウンアイコン -->
        <ChevronDownIcon
          class="h-5 w-5 text-violet-200 hover:text-violet-100"
          aria-hidden="true"
        />
      </MenuButton>

      <!-- Menu items / メニューアイテム -->
      <transition
        enter-active-class="transition duration-100 ease-out"
        enter-from-class="transform scale-95 opacity-0"
        enter-to-class="transform scale-100 opacity-100"
        leave-active-class="transition duration-75 ease-in"
        leave-from-class="transform scale-100 opacity-100"
        leave-to-class="transform scale-95 opacity-0"
      >
        <MenuItems
          class="absolute right-0 mt-2 w-36 origin-top-right divide-y divide-gray-100 rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
        >
          <div class="px-1 py-1">
            <!-- Profile menu item / プロフィールを表示するメニューアイテム -->
            <MenuItem v-slot="{ active }">
              <button
                :class="[
                  active ? 'bg-indigo-600 text-white' : 'text-gray-900',
                  'group flex w-full items-center rounded-md px-2 py-2 text-sm',
                ]"
              >
                <UserIcon
                  :active="active"
                  class="mr-2 h-5 w-5 text-indigo-400"
                  aria-hidden="true"
                />
                Profile
              </button>
            </MenuItem>
            <!-- Logout menu item / ログアウトを実行するメニューアイテム -->
            <MenuItem v-slot="{ active }">
              <button
                @click="logout"
                :class="[
                  active ? 'bg-indigo-600 text-white' : 'text-gray-900',
                  'group flex w-full items-center rounded-md px-2 py-2 text-sm',
                ]"
              >
                <LogoutIcon
                  :active="active"
                  class="mr-2 h-5 w-5 text-indigo-400"
                  aria-hidden="true"
                />
                Logout
              </button>
            </MenuItem>
          </div>
        </MenuItems>
      </transition>
    </Menu>
  </header>
</template>

<script setup>
import {MenuIcon, LogoutIcon, UserIcon} from '@heroicons/vue/outline'
import {Menu, MenuButton, MenuItems, MenuItem} from '@headlessui/vue'
import store from "../store";
import router from "../router";
import {computed} from "vue";

const emit = defineEmits(['toggle-sidebar'])

// Current user info / 現在のユーザー情報
const currentUser = computed(() => store.state.user.data);

// Logout function / ログアウト関数
function logout() {
  store.dispatch('logout')
    .then(() => {
      router.push({name: 'login'})
    })
}

</script>

<style scoped>

</style>
