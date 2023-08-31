<script setup>
import { ref } from 'vue'
// let serverURL = "http://127.0.0.1:3000";
let serverURL = "https://blom-server.iran.liara.run";


let userId = ref(null)
let flowerId = ref(null)
let flowerName = ref(null)
let light = ref(null)
let temprature = ref(null)
let soil = ref(null)
let symptoms = ref(null)
let description = ref(null)
let health = ref(null)
let solution = ref(null)
let education = ref(null)
let medicine = ref(null)
let plantImagesName = ref(null)
let medicineFiles = ref([])
let imagesFileNames = ref(null)


const dbData = ref(null)
const hasData = ref(true)
const loading = ref(true)
const init = ref(true)
getData()

async function getData() {
    hasData.value = true
    loading.value = true
    await fetch(serverURL + "/api/flowers/")
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

                dbData.value.map(element => {
                    const fileNameArr = element.fileName.split(',');
                    fileNameArr[1] = "https://blom-server.iran.liara.run/flower/images/" + fileNameArr[1];
                    fileNameArr[2] = "https://blom-server.iran.liara.run/flower/images/" + fileNameArr[2];
                    element.fileName = fileNameArr.join(',');
                    return element;
                });

            }
            else {
                hasData.value = false
                loading.value = false
            }
        })
}

async function postData() {
    hasData.value = true
    loading.value = true
    await fetch(serverURL + "/api/prescriptions/")
    const formData = new FormData()
    formData.append('userId', userId.value.value)
    formData.append('flowerId', flowerId.value.value)
    formData.append('flowerName', flowerName.value.value)
    formData.append('plantImagesName', plantImagesName.value.value)
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

const reqmodal = ref(null)
const flag3 = ref(false)
const opennewrequestmodal = () => {
    if (flag3.value) {
        reqmodal.value.classList.replace("flex", "hidden")
        document.querySelector("#newRequest").classList.replace("flex", "hidden")
        flag3.value = false
    }
    else {
        reqmodal.value.classList.replace("hidden", "flex")
        document.querySelector("#newRequest").classList.replace("hidden", "flex")
        flag3.value = true

    }
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

const flag2 = ref(false)
const newAnswerData = ref(null)
const opennewanswermodal = (id) => {
    if (flag2.value) {
        // close new answer modal
        newAnswerData.value = null
        userId.value = null
        flowerId.value = null
        flowerName.value = null
        plantImagesName.value = null
        health.value = null
        symptoms.value = null
        solution.value = null
        education.value = null
        medicine.value = null
        medicineFiles.value = null
        imagesFileNames.value = null
        reqmodal.value.classList.replace("flex", "hidden")
        document.querySelector("#newAnswer").classList.replace("flex", "hidden")
        flag2.value = false
    }
    else {
        for (let i = 0; i < dbData.value.length; i++) {
            if (dbData.value[i]._id === id) {
                newAnswerData.value = dbData.value[i]
            }
        }
        reqmodal.value.classList.replace("hidden", "flex")
        document.querySelector("#newAnswer").classList.replace("hidden", "flex")
        flag2.value = true
    }
}

const copyInnerText = (text) => {
    navigator.clipboard.writeText(text)
        .then(() => alert('کپی شد'))
        .catch((error) => console.error(`Error copying text: ${error}`));
}

const flag = ref(true)
const handleRequestTable = () => {
    if (flag.value) {
        document.querySelector("i[name=openReq]").classList.replace('hidden', 'block')
        document.querySelector("i[name=closeReq]").classList.replace('block', 'hidden')
        document.querySelector("#reqTable").classList.replace('block', 'hidden')
        flag.value = false
    }
    else {
        document.querySelector("i[name=openReq]").classList.replace('block', 'hidden')
        document.querySelector("i[name=closeReq]").classList.replace('hidden', 'block')
        document.querySelector("#reqTable").classList.replace('hidden', 'block')
        flag.value = true
    }
}

</script>
<template>
    <main class="flex flex-col py-[20px] justify-center items-center">
        <!-- <button class="text-[24px] flex justify-center items-center gap-5" @click="handleRequestTable"> -->
        <span class="text-[24px] flex justify-center items-center gap-5">درخواست ها</span>
        <div class="block border w-[90%] m-10" id="reqTable">
            <div class="py-[10px] px-[20px]">
                <!-- <button class="p-2 px-3 bg-blue-500 rounded text-white hover:bg-blue-600"
                    @click="opennewrequestmodal">افزودن درخواست جدید</button> -->
            </div>
            <!-- modal -->
            <div class="hidden w-full" ref="reqmodal">
                <div class="hidden flex-col gap-1 p-[20px]" id="newRequest">
                    <span>فرم افزودن درخواست جدید</span>
                    <input type="text" class="border" placeholder="آیدی کاربر" v-model="userId">
                    <input type="text" class="border" placeholder="آیدی گیاه" v-model="flowerId">
                    <input type="text" class="border" placeholder="نام گیاه" v-model="flowerName">
                    <input type="text" class="border" placeholder="نوع نور" v-model="light">
                    <input type="text" class="border" placeholder="دمای محیط" v-model="temprature">
                    <input type="text" class="border" placeholder="وضعیت خاک هنگام آبیاری" v-model="soil">
                    <input type="text" class="border" placeholder="علائم بیماری" v-model="symptoms">
                    <input type="text" class="border" placeholder="توضیحات" v-model="description">
                    <input type="file" class="border" id="files" name="files" placeholder="عکس های گیاه"
                        @input="setNewImage" multiple>
                    <div class="w-full flex justify-between items-center">
                        <button @click="opennewrequestmodal"
                            class="border bg-red-500 hover:bg-red-600 text-white p-1 px-20 rounded">لغو</button>
                        <button @click="postData"
                            class="border bg-blue-500 hover:bg-blue-600 text-white p-1 px-20 rounded">ذخیره</button>
                    </div>
                </div>

                <div class="hidden flex-col gap-1 p-[20px] w-full" id="newAnswer">
                    <span>فرم افزودن نسخه جدید</span>
                    <input type="text" class="border" placeholder="آیدی کاربر" ref="userId" v-model="newAnswerData.userId"
                        v-if="newAnswerData != null">
                    <input type="text" class="border" placeholder="آیدی گیاه" ref="flowerId" v-model="newAnswerData._id"
                        v-if="newAnswerData != null">
                    <input type="text" class="border" placeholder=" نام گل" ref="flowerName" v-model="newAnswerData.name"
                        v-if="newAnswerData != null">
                    <input type="number" min="1" max="100" class="border" placeholder="درصد سلامتی" v-model="health">
                    <textarea class="border outline-none input resize-none" cols="30" rows="3" placeholder="لینک های عکس گل"
                        ref="plantImagesName" v-model="newAnswerData.fileName" v-if="newAnswerData != null"></textarea>
                    <input type="text" class="border" placeholder="علائم" v-model="symptoms">
                    <input type="text" class="border" placeholder="روش درمان" v-model="solution">
                    <input type="text" class="border" placeholder="اموزش های لازم" v-model="education">
                    <input type="text" class="border" placeholder="دارو" v-model="medicine">
                    <input type="file" class="border" id="files" name="files" placeholder=" عکس دارو ها"
                        @input="setNewImage" multiple>
                    <div class="w-full flex justify-between items-center">
                        <button @click="opennewanswermodal"
                            class="border bg-red-500 hover:bg-red-600 text-white p-1 px-20 rounded">لغو</button>
                        <button @click="postData"
                            class="border bg-blue-500 hover:bg-blue-600 text-white p-1 px-20 rounded">ذخیره</button>
                    </div>
                </div>
            </div>
            <!-- header -->
            <div class="grid grid-flow-col grid-cols-12 border-b border-t">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1" title="آیدی کاربر">
                    آیدی گیاه</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="آیدی کاربر">آیدی کاربر</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="نام گیاه">نام گیاه</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="وضعیت">وضعیت</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="نوع نور">نوع نور</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="دما">دما</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="وضعیت خاک زمان آبیاری">وضعیت خاک زمان آبیاری</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="علائم بیماری">علائم بیماری</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="توضیحات">توضیحات</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="نام فایل عکس ها">نام فایل عکس ها</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    title="تاریخ">تاریخ</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"></div>
            </div>
            <!-- content -->
            <div v-for="data in dbData" class="grid grid-flow-col grid-cols-12 border-b" v-if="dbData != null">
                <div class="py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1" :title="data._id"
                    @click="copyInnerText(data._id)">کپی کردن آیدی گل</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1"
                    :title="data.userId" @click="copyInnerText(data.userId)">کپی کردن آیدی کاربر</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">{{
                    data.name }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">{{
                    data.status }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">{{
                    data.light }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">{{
                    data.temprature }}</div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1">{{
                    data.soil }}</div>
                <div class="border-r py-2 px-3 flex flex-col justify-center items-center text-[12px] truncate col-span-1">
                    <span v-for="item in data.symptom.split(',')">{{ item }}</span>
                </div>
                <div class="border-r py-2 px-3 flex flex-wrap justify-center items-center text-[12px] truncate col-span-1">
                    {{ data.description }}</div>
                <div
                    class="border-r py-2 px-3 flex flex-col justify-center items-center text-[12px] truncate col-span-1 cursor-pointer relative">
                    <a class="underline" v-for="(item, index) in data.fileName.split(',')" :href="item" target="_blank">Link
                        file {{ index + 1 }}</a>
                </div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 flex-col">
                    <span>{{ data.createdAt.split('.')[0].split('T')[0] }}</span>
                    <span>{{ data.createdAt.split('.')[0].split('T')[1] }}</span>
                </div>
                <div class="border-r py-2 px-3 flex justify-center items-center text-[12px] truncate col-span-1 bg-blue-500 hover:bg-blue-600 cursor-pointer text-white"
                    @click="opennewanswermodal(data._id)">
                    صدور نسخه
                </div>
            </div>

        </div>
    </main>
</template>
