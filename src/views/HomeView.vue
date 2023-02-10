<template>
    <div class="flex">
        <div id="Header" class="fixed w-[420px] z-10">

            <div class="bg-[black] w-full flex justify-between items-center px-3 py-2">
                <img class="rounded-full ml-1 w-10" :src="userStore.picture || ''" alt="">
                <div class="flex items-center justify-center">
                    <AccountGroupIcon fillColor="white" class="mr-6" />
                    <DotsVerticalIcon @click="logout" fillColor="white" class=" cursor-pointer" />
                </div>
            </div>

            <div id="Search" class="bg-black w-full px-2 border-y shadow-sm">
                <div class="px-1 m-2 bg-gray-700 flex items-center justify-center rounded-md">
                    <MagnifyIcon fillColor="white" :size="18" class="ml-2" />
                    <input 
                    @click="showFindFriends = !showFindFriends"
                    class="
                        ml-5
                        bg-gray-700
                        apperance-none
                        w-full
                        bg-[black]
                        py-1.5
                        px-2.5
                        text-white
                        leading-tight
                        focus:outline-none 
                        focus:shadow-outline
                        placeholder:text-sm
                        placeholder:text-gray-500
                    "
                    autocomplete="off"
                    type="text"
                    placeholder="Start a new chat"
                    >
                </div>
            </div>
        </div>

        <div v-if="showFindFriends">
            <FindFriendsView class="pt-28" />
        </div>
        <div v-else>
            <ChatsView class="mt-[100px]" />
        </div>

        <div v-if="userDataForChat.length">
            <MessageView />
        </div>
        <div v-else>
            <div class="ml-[420px] fixed w-[calc(100vw-420px)] h-[100vh] bg-gray-100 text-center">
            <div class="rpart grid h-screen place-items-center">
                <div>
                    <div class="w-full flex items-center justify-center">
                        <img width="375" src="../assets/glitch.png" alt="">
                    </div>
                    <div class="text-[32px] text-white font-light mt-1">FESTA</div>
                    <div class="text-[14px] text-white mt-2">
                        <div>Send and receive messages without keeping your phone online.</div>
                    </div>
                </div>
            </div>
        </div>
        </div>
    </div>
</template>

<script setup>
import ChatsView from './ChatsView.vue';
import MessageView from './MessageView.vue';
import FindFriendsView from './FindFriendsView.vue';

import AccountGroupIcon from 'vue-material-design-icons/AccountGroup.vue'
import DotsVerticalIcon from 'vue-material-design-icons/DotsVertical.vue'
import MagnifyIcon from 'vue-material-design-icons/Magnify.vue'
import { ref, onMounted } from 'vue';

import { useUserStore } from '@/store/user-store'
import { useRouter } from 'vue-router'
import { storeToRefs } from 'pinia';
const router = useRouter()
const userStore = useUserStore()
const { showFindFriends, userDataForChat } = storeToRefs(userStore)

// let open = ref(true)
// let showFindFriends = ref(false)

onMounted(async () => {
    try {
        userStore.getAllUsers()
        await userStore.getAllChatsByUser()
    } catch (error) {
        console.log(error)
    }
})

const logout = () => {
    let res = confirm('Are you sure you want to logout?')
    if (res) { userStore.logout(); router.push('/login') }
}
</script>

<style>

.rpart{
    background-image: radial-gradient( circle 610px at 5.2% 51.6%,  rgb(0, 4, 90) 0%, rgba(7,3,53,1) 97.5% );    width: 100%;
}

</style>
