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
    await fetch(serverURL + "/api/prescriptions/")
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
                // console.log(data);
                processData()
            }
            else {
                hasData.value = false
                loading.value = false
            }
        })
}

const processData = () => {
    for (let i = 0; i < dbData.value.prescriptions.length; i++) {
        for (let j = 0; j < dbData.value.user.length; j++) {
            if (dbData.value.prescriptions[i].userId === dbData.value.user[j]._id) {
                dbData.value.prescriptions[i].phone = dbData.value.user[j].phone
            }
        }
    }
    // console.log(dbData.value.prescriptions);
}

const userId = ref(null)
const flowerId = ref(null)
const flowerName = ref(null)
const health = ref(null)
const plantImagesName = ref(null)
const symptoms = ref(null)
const solution = ref(null)
const education = ref(null)
const medicine = ref(null)
const medicineFiles = ref([])
const imagesFileNames = ref(null)

async function postData() {
    hasData.value = true
    loading.value = true
    await fetch(serverURL + "/api/prescriptions/")
    const formData = new FormData()
    formData.append('userId', userId.value)
    formData.append('flowerId', flowerId.value)
    formData.append('flowerName', flowerName.value)
    formData.append('plantImagesName', plantImagesName.value)
    formData.append('health', health.value)
    formData.append('symptoms', symptoms.value)
    formData.append('solution', solution.value)
    formData.append('education', education.value)
    formData.append('medicine', medicine.value)
    for (let i = 0; i < medicineFiles.value.length; i++) {
        formData.append('files', medicineFiles.value[i])
    }
    formData.append('fileName', imagesFileNames.value)
    //   for (let [key, value] of formData) {
    //   console.log(key, value);
    //     }
    // console.log(...formData)
    await fetch(serverURL + "/api/prescriptions/", {
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

const copyInnerText = (text) => {
    navigator.clipboard.writeText(text)
        .then(() => alert('کپی شد'))
        .catch((error) => console.error(`Error copying text: ${error}`));
}

// set image data
const setNewImage = (e) => {
    // Get file name and file itself to send to the database
    medicineFiles.value = []
    imagesFileNames.value = []
    for (let i = 0; i < e.target.files.length; i++) {
        let selectedFile = e.target.files[i];
        medicineFiles.value.push(selectedFile);
        // save old file name
        imagesFileNames.value.push(selectedFile.name);
    }
};

const flag = ref(true)
const handleAnswerTable = () => {
    if (flag.value) {
        document.querySelector("i[name=openAnswer]").classList.replace('hidden', 'block')
        document.querySelector("i[name=closeAnswer]").classList.replace('block', 'hidden')
        document.querySelector("#answerTable").classList.replace('block', 'hidden')
        flag.value = false
    }
    else {
        document.querySelector("i[name=openAnswer]").classList.replace('block', 'hidden')
        document.querySelector("i[name=closeAnswer]").classList.replace('hidden', 'block')
        document.querySelector("#answerTable").classList.replace('hidden', 'block')
        flag.value = true
    }
}

const flag2 = ref(true)
const openmodal = () => {
    if (flag2.value) {
        document.getElementById("modal").classList.replace("hidden", "flex")
        flag2.value = false
    }
    else {
        document.getElementById("modal").classList.replace("flex", "hidden")
        flag2.value = true
    }
}

</script>
<template>
    <main class="flex flex-col py-[20px] justify-center items-center">
        <!-- <button class="text-[24px] flex justify-center items-center gap-5" @click="handleAnswerTable">
            <span>نسخه ها</span>
            <i name="closeAnswer" class="block">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M12 5V19M12 5L6 11M12 5L18 11" stroke="#000000" stroke-width="2" stroke-linecap="round"
                        stroke-linejoin="round" />
                </svg>
            </i>
            <i name="openAnswer" class="hidden">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M12 6V18M12 18L7 13M12 18L17 13" stroke="#000000" stroke-width="2" stroke-linecap="round"
                        stroke-linejoin="round" />
                </svg>
            </i>
        </button> -->
        <span class="text-[24px] flex justify-center items-center gap-5">نسخه ها</span>
        <div class="border w-[90%] m-10" id="answerTable">
            <div class="py-[10px] px-[20px]">
                <button class="p-2 px-3 bg-blue-500 rounded text-white hover:bg-blue-600" @click="openmodal">افزودن نسخه
                    جدید</button>
            </div>
            <!-- modal -->
            <div class="hidden flex-col gap-1 border p-[20px] w-[90%]" id="modal">
                <span>فرم افزودن نسخه جدید</span>
                <input type="text" class="border" placeholder="آیدی کاربر" v-model="userId">
                <input type="text" class="border" placeholder="آیدی گیاه" v-model="flowerId">
                <input type="text" class="border" placeholder=" نام گل" v-model="flowerName">
                <input type="number" min="1" max="100" class="border" placeholder="درصد سلامتی" v-model="health">
                <textarea class="border outline-none input resize-none" cols="30" rows="3" placeholder="لینک های عکس گل"
                    v-model="plantImagesName"></textarea>
                <input type="text" class="border" placeholder="علائم" v-model="symptoms">
                <input type="text" class="border" placeholder="روش درمان" v-model="solution">
                <input type="text" class="border" placeholder="اموزش های لازم" v-model="education">
                <input type="text" class="border" placeholder="دارو" v-model="medicine">
                <input type="file" class="border" id="files" name="files" placeholder=" عکس دارو ها" @input="setNewImage"
                    multiple>
                <div class="w-full flex justify-between items-center">
                    <button @click="openmodal"
                        class="border bg-red-500 hover:bg-red-600 text-white p-1 px-20 rounded">لغو</button>
                    <button @click="postData"
                        class="border bg-blue-500 hover:bg-blue-600 text-white p-1 px-20 rounded">ذخیره</button>
                </div>
            </div>
            <!-- header -->
            <div class="grid grid-flow-col grid-cols-11 border-b border-t">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1" title="لینک نسخه">
                    لینک نسخه</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="شماره تماس کاربر">شماره تماس کاربر</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="نام گل"> نام گیاه</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="درصد خرابی">درصد سلامتی</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="عکس گل">عکس گیاه</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="علائم">علائم</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="روش درمان">روش درمان</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="آموزش های لازم">اموزش های لازم</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="دارو">دارو</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="عکس داروها"> عکس دارو ها</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="تاریخ">تاریخ</div>
            </div>
            <div v-for="data in dbData.prescriptions" class="grid grid-flow-col grid-cols-11 border-b"
                v-if="dbData != null">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 cursor-pointer"
                    :title="'https://app.blom.land/prescription/' + data.flowerId"
                    @click="copyInnerText('https://app.blom.land/prescription/' + data.flowerId)">کپی کردن لینک نسخه</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 cursor-pointer"
                    :title="data.phone" @click="copyInnerText(data.phone)">{{ data.phone }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.flowerName">{{ data.flowerName }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.health">{{ data.health }}</div>
                <div
                    class="border-r py-2 px-3 flex flex-col justify-center items-center text-[12px] truncate col-span-1 relative">
                    <a class="underline" v-for="(link, index) in data.plantImagesName.split(',')" :href="link"
                        target="_blank">Link file {{ index + 1 }}</a>
                </div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.symptoms">{{ data.symptoms == null ? "-" : data.symptoms }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.solution">{{ data.solution == null ? "-" : data.solution }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.education">{{ data.education == null ? "-" : data.education }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.medicine">{{ data.medicine == null ? "-" : data.medicine }}</div>
                <div
                    class="border-r py-2 px-3 flex flex-col justify-center items-center text-[12px] truncate col-span-1 relative">
                    {{ data.medecineFileNames == 'null' ? "-" : "" }}
                    <a class="underline" :href="data.medecineFileNames.split(',')[0]" target="_blank"
                        v-if="data.medecineFileNames != 'null'">Link file 1</a>
                    <a class="underline" v-for="(data, index) in data.medecineFileNames.split(',')[index + 1]"
                        :href="'https://blom-server.iran.liara.run/medic/images/' + data[index + 1]" target="_blank">Link
                        file
                        {{ index + 1 }}</a>
                </div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 flex-col">
                    <span>{{ data.createdAt.split('.')[0].split('T')[0] }}</span>
                    <span>{{ data.createdAt.split('.')[0].split('T')[1] }}</span>
                </div>
            </div>
        </div>
    </main>
</template>
