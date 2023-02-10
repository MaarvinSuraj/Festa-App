<template>
    <div class="ml-[420px] w-full">
        <div class="w-full">
        <div id="BG"></div>
            <div class="border-l border-green-500 w-full">
                <div 
                    class="chatheader">
                    <div class="flex items-center">
                        <img 
                            v-if="userDataForChat[0] && userDataForChat[0].picture"
                            class="rounded-full mx-1 w-10" 
                            :src="userDataForChat[0].picture"
                        >
                        <div 
                        v-if="userDataForChat[0] && userDataForChat[0].firstName"
                        class="text-white ml-1 font-semibold">
                            {{ userDataForChat[0].firstName }}
                        </div>
                    </div>

                    <DotsVerticalIcon fillColor="#515151" />
                </div>
            </div>

            <div 
            id="MessagesSection"
            class="
                pt-20 
                pb-8 
                z-[-1]
                h-[calc(100vh-65px)]
                w-[calc(100vw-420px)]
                overflow-auto
                
                touch-auto
            "
            >
                <div v-if="currentChat && currentChat.length" class="px-10 text-sm">

                <!-- {{ currentChat }} -->

                    <div v-for="msg in currentChat[0].messages" :key="msg">
                        <div v-if="msg.sub === sub" class="flex w-[calc(100%-50px)]">
                            <div class="sendermsg inline-block backdrop-blur-md bg-green-500/30 p-2 rounded-lg my-1">
                                {{ msg.message }}
                            </div>
                        </div>

                        <div v-else class="flex justify-end space-x-1 w-[calc(100%-50px)] float-right">
                            <div class="receivermsg inline-block backdrop-blur-md bg-white/30 p-2 rounded-lg my-1">
                                {{ msg.message }}
                            </div>
                        </div>
                    </div>

                </div>
            </div>

            <div class="w-[calc(100vw-420px)] p-2.5 z-10 bg-[black] fixed bottom-0">
                <div class="flex items-center justify-center">
                    <EmoticonExcitedOutlineIcon :size="27" fillColor="white" class="mx-1.5" />
                    <PaperclipIcon :size="27" fillColor="white" class="mx-1.5 mr-3" />
                    <input 
                    v-model="message"
                    class="
                        bg-gray-700
                        mr-1
                        shadow
                        apperance-none
                        rounded-lg
                        w-full
                        py-3
                        px-4
                        text-white
                        leading-tight
                        focus:outline-none 
                        focus:shadow-outline
                    "
                    autocomplete="off"
                    type="text"
                    placeholder="Message"
                    >

                    <button 
                        :disabled="disableBtn"
                        @click="sendMessage" 
                        class="ml-3 p-2 w-12 flex items-center justify-center"
                    >
                        <SendIcon fillColor="white" />
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import DotsVerticalIcon from 'vue-material-design-icons/DotsVertical.vue'
import EmoticonExcitedOutlineIcon from 'vue-material-design-icons/EmoticonExcitedOutline.vue'
import PaperclipIcon from 'vue-material-design-icons/Paperclip.vue'
import SendIcon from 'vue-material-design-icons/Send.vue'
import { ref, watch } from 'vue'

import { useUserStore } from '../store/user-store'
import { storeToRefs } from 'pinia'
const userStore = useUserStore()
const { userDataForChat, currentChat, sub } = storeToRefs(userStore)

let message = ref('')
let disableBtn = ref(false)

watch(() => currentChat.value, (chat) => {
    if (chat.length) {
        setTimeout(() => {
            let objDiv = document.getElementById('MessagesSection')
            objDiv.scrollTop = objDiv.scrollHeight
        }, 50)
    }
}, { deep:true })

const sendMessage = async () => {
    if (message.value === '') return 
    disableBtn.value = true

    await userStore.sendMessage({
        message: message.value,
        sub2: userDataForChat.value[0].sub2,
        chatId: userDataForChat.value[0].id,
    })
    message.value = ''

    const userData = userDataForChat.value[0]

    let data = {
        id: userData.id,
        key1: 'sub1HasViewed', val1: false, 
        key2: 'sub2HasViewed', val2: false, 
    }
    if (userData.sub1 === sub.value) {
        data.val1 = true
        data.val2 = false
    } else if (userData.sub2 === sub.value) {
        data.val1 = false
        data.val2 = true
    }
    await userStore.hasReadMessage(data)

    let objDiv = document.getElementById('MessagesSection')
    objDiv.scrollTop = objDiv.scrollHeight

    disableBtn.value = false
}
</script>

<style>
#BG {
    background-image: radial-gradient( circle 610px at 5.2% 51.6%,  rgb(0, 4, 90) 0%, rgba(7,3,53,1) 97.5% );    width: 100%;
    height: 100%;
    position: fixed;
    z-index: -1;
}

.chatheader{
    background-image: linear-gradient( 105.5deg,  rgba(0,0,0,1) 11%, rgba(0,0,0,1) 74.9% );
    margin-right: 0.25rem;
    
    width: 100%;
    position: fixed;
    z-index: 10;
    min-width: calc(100vw-420px) ;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0.5rem;
}

.sendermsg{
    background-color: #85FFBD;
    background-image: linear-gradient(45deg, #85FFBD 0%, #FFFB7D 100%);
}

.receivermsg{
    background-color: #FF3CAC;
    background-image: linear-gradient(225deg, #FF3CAC 0%, #8800ff 50%, #2B86C5 100%);
    color: white;
}
</style>