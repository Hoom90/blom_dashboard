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
    await fetch(serverURL + "/api/library/")
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

const name = ref(null)
const image = ref(null)
const imageFileName = ref(null)

async function postData() {
    hasData.value = true
    loading.value = true
    const formData = new FormData()
    formData.append('name', name.value)
    formData.append('file', image.value)
    formData.append('image', imageFileName.value)
    console.log(...formData)
    await fetch(serverURL + "/api/library/", {
        method: "POST",
        body: formData,
    })
        .then((res) => {
            if (!res.ok) {
                hasData.value = false
                loading.value = false
                alert('sth 404')
            }
            hasData.value = true
            loading.value = false
            return res.json();
        })
        .then((data) => location.reload());
}

// set image data
const setNewImage = (e) => {
    // Get file name and file itself to send to the database
    image.value = null
    imageFileName.value = null

    let selectedFile = e.target.files[0];
    image.value = selectedFile;
    // save old file name
    imageFileName.value = selectedFile.name;
};

const flag = ref(false)
const handleBasicTable = () => {
    if (flag.value) {
        document.querySelector("i[name=openbasic]").classList.replace('hidden', 'block')
        document.querySelector("i[name=closebasic]").classList.replace('block', 'hidden')
        document.querySelector("#basicTable").classList.replace('block', 'hidden')
        flag.value = false
    }
    else {
        document.querySelector("i[name=openbasic]").classList.replace('block', 'hidden')
        document.querySelector("i[name=closebasic]").classList.replace('hidden', 'block')

        document.querySelector("#basicTable").classList.replace('hidden', 'block')
        flag.value = true
    }
}

const flag2 = ref(true)
const openmodal = () => {
    if (flag2.value) {
        document.getElementById("basicmodal").classList.replace("hidden", "flex")
        flag2.value = false
    }
    else {
        document.getElementById("basicmodal").classList.replace("flex", "hidden")
        flag2.value = true
    }
}

</script>
<template>
    <main class="flex flex-col py-[20px] justify-center items-center">
        <span class="text-[24px] flex justify-center items-center gap-5">کتابخانه</span>
        <div class="border w-[90%] m-10" id="basicTable">
            <div class="py-[10px] px-[20px]">
                <button class="p-2 px-3 bg-blue-500 rounded text-white hover:bg-blue-600" @click="openmodal">افزودن
                    جدید</button>
            </div>
            <!-- modal -->
            <div class="hidden flex-col gap-1 border p-[20px] w-[90%]" id="basicmodal">
                <span>فرم افزودن نسخه جدید</span>
                <input type="text" class="border" placeholder=" نام گل" v-model="name">
                <input type="file" class="border" placeholder="عکس گل" @input="setNewImage">
                <div class="w-full flex justify-between items-center">
                    <button @click="openmodal"
                        class="border bg-red-500 hover:bg-red-600 text-white p-1 px-20 rounded">لغو</button>
                    <button @click="postData"
                        class="border bg-blue-500 hover:bg-blue-600 text-white p-1 px-20 rounded">ذخیره</button>
                </div>
            </div>
            <!-- header -->
            <div class="grid grid-flow-col grid-cols-2 border-b border-t">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1" title="نام">نام
                </div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="عکس">عکس</div>
            </div>
            <div v-for="data in  dbData " class="grid grid-flow-col grid-cols-2 border-b">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">
                    {{ data.name }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">
                    <span v-if="data.image == '-'">عکس ندارد</span>
                    <a v-else :href="data.image" target="_blank">{{ data.image }}</a>
                </div>
            </div>
        </div>
    </main>
</template>
