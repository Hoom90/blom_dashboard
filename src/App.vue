<script setup>
import { ref } from 'vue'
import requestVue from './components/request.vue';
import userVue from './components/user.vue';
import answerVue from './components/answer.vue';
import libraryVue from './components/library.vue';

const handleSection = (number) => {
  const sections = document.querySelectorAll("#section")
  const routers = document.querySelectorAll("#router")
  if (number === 0) {
    sections[0].classList.replace("hidden", "block")
    routers[0].classList.add("bg-blue-500")
    sections[1].classList.replace("block", "hidden")
    routers[1].classList.remove("bg-blue-500")
    sections[2].classList.replace("block", "hidden")
    routers[2].classList.remove("bg-blue-500")
    sections[3].classList.replace("block", "hidden")
    routers[3].classList.remove("bg-blue-500")

    routers[4].classList.add("bg-blue-500")
    routers[5].classList.remove("bg-blue-500")
    routers[6].classList.remove("bg-blue-500")
    routers[7].classList.remove("bg-blue-500")
  }
  if (number === 1) {
    sections[1].classList.replace("hidden", "block")
    routers[1].classList.add("bg-blue-500")
    sections[0].classList.replace("block", "hidden")
    routers[0].classList.remove("bg-blue-500")
    sections[2].classList.replace("block", "hidden")
    routers[2].classList.remove("bg-blue-500")
    sections[3].classList.replace("block", "hidden")
    routers[3].classList.remove("bg-blue-500")

    routers[5].classList.add("bg-blue-500")
    routers[4].classList.remove("bg-blue-500")
    routers[6].classList.remove("bg-blue-500")
    routers[7].classList.remove("bg-blue-500")
  }
  if (number === 2) {
    sections[2].classList.replace("hidden", "block")
    routers[2].classList.add("bg-blue-500")
    sections[1].classList.replace("block", "hidden")
    routers[1].classList.remove("bg-blue-500")
    sections[0].classList.replace("block", "hidden")
    routers[0].classList.remove("bg-blue-500")
    sections[3].classList.replace("block", "hidden")
    routers[3].classList.remove("bg-blue-500")

    routers[6].classList.add("bg-blue-500")
    routers[4].classList.remove("bg-blue-500")
    routers[5].classList.remove("bg-blue-500")
    routers[7].classList.remove("bg-blue-500")
  }
  if (number === 3) {
    sections[3].classList.replace("hidden", "block")
    routers[3].classList.add("bg-blue-500")
    sections[1].classList.replace("block", "hidden")
    routers[1].classList.remove("bg-blue-500")
    sections[2].classList.replace("block", "hidden")
    routers[2].classList.remove("bg-blue-500")
    sections[0].classList.replace("block", "hidden")
    routers[0].classList.remove("bg-blue-500")

    routers[7].classList.add("bg-blue-500")
    routers[4].classList.remove("bg-blue-500")
    routers[5].classList.remove("bg-blue-500")
    routers[6].classList.remove("bg-blue-500")
  }
}

const flag = ref(false)
const handleDropdown = () => {
  if (flag.value) {
    document.querySelector("div[name=menu]").classList.replace('flex', 'hidden')
    flag.value = false
  }
  else {
    document.querySelector("div[name=menu]").classList.replace('hidden', 'flex')
    flag.value = true
  }
}

</script>
<template>
  <div class="overflow-hidden">
    <div class="fixed top-0 bg-[#49b779bd] w-full h-[10svh] border-b flex items-center px-[20px]">
      <div class="md:hidden">
        <button @click="handleDropdown" class="p-2 border rounded">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M4 6H20M4 12H20M4 18H20" stroke="#000000" stroke-width="2" stroke-linecap="round"
              stroke-linejoin="round" />
          </svg>
        </button>
        <div class="hidden absolute top-[10svh] py-[10px] bg-white w-full" name="menu">
          <button @click="handleSection(0)" id="router"
            class="p-2 px-3 border-b bg-blue-500 active:bg-blue-500">درخواست</button>
          <button @click="handleSection(1)" id="router" class="p-2 px-3 border-b active:bg-blue-500">نسخه</button>
          <button @click="handleSection(2)" id="router" class="p-2 px-3 border-b active:bg-blue-500">کاربران</button>
          <button @click="handleSection(3)" id="router" class="p-2 px-3 border-b active:bg-blue-500">کتابخانه</button>
        </div>
      </div>
    </div>
    <div class="mt-[10svh] w-full flex h-[80svh]">
      <div class="bg-white h-full shadow-[0px_10px_10px_#c9c9c9] z-10 flex flex-col">
        <div class="hidden md:flex bg-white h-full flex-col">
          <button @click="handleSection(0)" id="router"
            class="p-2 px-3 border-b bg-blue-500 active:bg-blue-500">درخواست</button>
          <button @click="handleSection(1)" id="router" class="p-2 px-3 border-b active:bg-blue-500">نسخه</button>
          <button @click="handleSection(2)" id="router" class="p-2 px-3 border-b active:bg-blue-500">کاربران</button>
          <button @click="handleSection(3)" id="router" class="p-2 px-3 border-b active:bg-blue-500">کتابخانه</button>
        </div>
      </div>
      <div class="bg-white w-11/12 h-full overflow-auto">
        <requestVue class="block" id="section"></requestVue>
        <answerVue class="hidden" id="section"></answerVue>
        <userVue class="hidden" id="section"></userVue>
        <libraryVue class="hidden" id="section"></libraryVue>
      </div>
    </div>
    <div class="fixed bottom-0 w-full h-[10svh] border-t flex gap-3 justify-center md:justify-end items-center px-[20px]">
      <a href="http://blom.land/" class="border rounded p-1 px-3 border-[#49b779] bg-white active:bg-[#49b779]">برو به
        سایت</a>
      <a href="http://app.blom.land/" class="border rounded p-1 px-3 border-[#49b779] bg-white active:bg-[#49b779]">برو به
        فرم</a>
    </div>
  </div>
</template>
