<template>
    <div class="back w-full">
        <div class="mt-8 ml-10 flex items-center w-full">
                <img width="50" src="../assets/festaLogo.jpeg" alt="">
                <div class="text-3xl font-bold text-white ml-2">FESTA</div>
                </div>
        <div class="max-w-xl mx-auto">
            

            <div class="card bg-white z-10 p-24 m-6 mt-20">
                
                <div class="text-center text-4xl text-white font-light pb-10">
                Login
                </div>
                <div class="w-full flex justify-center bg-[#191919] p-3 rounded-md">
                    <GoogleLogin :callback="callback"/>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { useUserStore } from '@/store/user-store'
import { useRouter } from 'vue-router'
const userStore = useUserStore()
const router = useRouter()  

const callback = async (response) => {
    console.log(response)
    await userStore.getUserDetailsFromGoogle(response)
    setTimeout(() => { router.push('/') }, 200)
}
</script>

<style>
body{
    background-image: linear-gradient(225deg, #FF3CAC 0%, #8800ff 50%, black 100%);
    width: 100vw;
    height: 100vh;
    }

.card{
    /* From https://css.glass */
background: rgba(255, 255, 255, 0.2);
border-radius: 16px;
box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
backdrop-filter: blur(5px);
-webkit-backdrop-filter: blur(5px);
border: 1px solid rgba(255, 255, 255, 0.27);
}
</style>