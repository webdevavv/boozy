<script setup>
import SettingsIcon from '@/components/icons/SettingsIcon.vue'
import LoginInput from '@/components/ui/LoginInput.vue'
import CommentIcon from '@/components/icons/CommentIcon.vue'
import RepostIcon from '@/components/icons/RepostIcon.vue'
import HeartIcon from '@/components/icons/HeartIcon.vue'
</script>
<template>
  <div class="wrapper">
    <div class="content">
      <div class="sidebar shadow-xl bg-white rounded sticky top-1 py-2 px-3">
        <div class="flex items-center justify-between mt-2 mb-5">
          <div class="flex items-center gap-2">
            <span
              ><img class="w-[30px] h-[30px] rounded-4xl" src="/avatar.png" alt="avatar"
            /></span>
            <span class="text-lg font-bold">Иван</span>
          </div>
          <span class="w-[20px] h-[20px] cursor-pointer"><SettingsIcon /></span>
        </div>
        <LoginInput placeholder="Поиск" type="text" id="search" />
        <div class="flex flex-col gap-2 mt-5">
          <span class="font-bold">Категории:</span>
          <span class="bg-[#ecf3ff] cursor-pointer px-2 py-1 rounded">Все</span>
          <span class="cursor-pointer px-2 py-1 rounded transition hover:bg-[#ecf3ff]">Бизнес</span>
          <span class="cursor-pointer px-2 py-1 rounded transition hover:bg-[#ecf3ff]"
            >Анекдоты</span
          >
          <span class="cursor-pointer px-2 py-1 rounded transition hover:bg-[#ecf3ff]"
            >Технологии</span
          >
        </div>
      </div>
      <div class="bg-white p-5 rounded flex-1 shadow-xl flex flex-col gap-5">
        <div class="border-b pb-3 border-[#ecf3ff]">
          <div class="flex items-center gap-2 h-[30px] mb-3">
            <img src="/avatar.png" alt="Иван" class="w-[30px] h-[30px] rounded-4xl" />
            <span>Иван</span>
          </div>
          <img
            src="/1647870175_1-sportishka-com-p-piknik-s-vinom-na-prirode-turizm-krasivo-f-1.jpg"
            alt="Это первый пост добавленный в Bozzy"
            class="rounded"
          />
          <h3 class="text-lg font-bold text-gray-800">Это первый пост добавленный в Bozzy</h3>
          <p class="text-md text-gray-700">
            Bozzy - это мир алкоголиков, сеть, где собраны люди с общими интересами
          </p>
          <div class="flex items-center gap-3 my-3">
            <span
              class="bg-[#ecf3ff] p-1 rounded-xl flex items-center justify-center cursor-pointer"
            >
              <HeartIcon class="w-[20px] h-[20px]" />
            </span>
            <span
              class="bg-[#ecf3ff] p-1 rounded-xl flex items-center justify-center cursor-pointer"
            >
              <CommentIcon class="w-[20px] h-[20px]" />
            </span>
            <span
              class="bg-[#ecf3ff] p-1 rounded-xl flex items-center justify-center cursor-pointer"
            >
              <RepostIcon class="w-[20px] h-[20px]" />
            </span>
          </div>
          <span class="text-[#609aff] bg-[#ecf3ff] text-xs px-2 py-1 rounded">Анекдот</span>
        </div>
      </div>
      <div class="sidebar shadow-xl bg-white rounded sticky top-1">dfasd</div>
    </div>
    <p class="fixed bottom-2 right-4 text-[#93bafd] text-xs">
      Национальный проект по борьбе с алкоголизмом
    </p>
  </div>
</template>

<style scoped>
.wrapper {
  min-height: 100dvh;
  width: 100vw;
  display: flex;
  align-items: flex-start;
  padding: 0.25rem 0.25rem 1rem;
  gap: 0.5rem;
}
.content {
  max-width: 1200px;
  margin: 0 auto;

  display: flex;
  gap: 0.5rem;
  align-items: flex-start;
}
.sidebar {
  max-width: 300px;
  width: 100%;
}
</style>
