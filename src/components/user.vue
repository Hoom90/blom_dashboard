<script setup>
import { ref } from 'vue'
// let serverURL = "http://127.0.0.1:3000";
let serverURL = "https://blom-server.iran.liara.run";

const dbData = ref(null)
const hasData = ref(true)
const loading = ref(true)
const init = ref(true)
getData()

async function getData() {
    hasData.value = true
    loading.value = true
    await fetch(serverURL + "/api/users/")
        .then((res) => res.json())
        .then((data) => {
            if (data.length != 0) {
                if (init) {
                    hasData.value = true
                    dbData.value = data;
                    init.value = false
                    loading.value = false
                }
                else {
                    hasData.value = true
                    dbData.value = dbData.value + data;
                    loading.value = false
                }
            }
            else {
                hasData.value = false
                loading.value = false
            }
        })
}

const copyInnerText = (text) => {
    navigator.clipboard.writeText(text)
        .then(() => alert('کپی شد'))
        .catch((error) => console.error(`Error copying text: ${error}`));
}

const flag = ref(false)
const handleUserTable = () => {
    if (flag.value) {
        document.querySelector("i[name=openuser]").classList.replace('hidden', 'block')
        document.querySelector("i[name=closeuser]").classList.replace('block', 'hidden')
        document.querySelector("#userTable").classList.replace('block', 'hidden')
        flag.value = false
    }
    else {
        document.querySelector("i[name=openuser]").classList.replace('block', 'hidden')
        document.querySelector("i[name=closeuser]").classList.replace('hidden', 'block')

        document.querySelector("#userTable").classList.replace('hidden', 'block')
        flag.value = true
    }
}

</script>
<template>
    <main class="flex flex-col py-[20px] justify-center items-center">
        <!-- <button class="text-[24px] flex justify-center items-center gap-5" @click="handleUserTable">
            <span>مدیریت کاربران</span>
            <i name="closeuser" class="hidden">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M12 5V19M12 5L6 11M12 5L18 11" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
            </i>
            <i name="openuser" class="block">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M12 6V18M12 18L7 13M12 18L17 13" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
            </i>
        </button> -->
        <span class="text-[24px] flex justify-center items-center gap-5">کاربران</span>
        <!-- <div class="hidden border w-[90%] m-10" id="userTable"> -->
        <div class="border w-[90%] m-10" id="userTable">
            <!-- header -->
            <div class="grid grid-flow-col grid-cols-8 border-b">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-3" title="آیدی کاربر">
                    آیدی کاربر</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-3"
                    title="نام فایل عکس ها">شماره تماس کاربر</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="زمان">زمان</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="تاریخ">تاریخ</div>
            </div>
            <div v-for="data in dbData" class="grid grid-flow-col grid-cols-8 border-b">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-3" :title="data._id"
                    @click="copyInnerText(data._id)">کپی کردن آیدی کاربر</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-3">{{
                    data.phone }}</div>
                <!-- <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">{{ data.createdAt.split('T')[0] }}</div> -->
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 flex-col">
                    <span>{{ data.createdAt.split('.')[0].split('T')[1] }}</span>
                </div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 flex-col">
                    <span>{{ data.createdAt.split('.')[0].split('T')[0] }}</span>
                </div>
            </div>
        </div>
    </main>
</template>
