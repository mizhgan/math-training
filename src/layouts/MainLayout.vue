<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title> Дашины тренировки </q-toolbar-title>

        <!-- <div>Quasar v{{ $q.version }}</div> -->
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header> Меню </q-item-label>
        <q-item clickable tag="router-link to" to="/">
          <q-item-section avatar>
            <q-icon name="school" />
          </q-item-section>

          <q-item-section>
            <q-item-label>Главная</q-item-label>
          </q-item-section>
        </q-item>

        <q-item clickable tag="router-link to" to="multiply">
          <q-item-section avatar>
            <q-icon name="school" />
          </q-item-section>

          <q-item-section>
            <q-item-label>Умножение</q-item-label>
            <q-item-label caption>Проверка таблицы умножение</q-item-label>
          </q-item-section>
        </q-item>

        <q-item clickable tag="router-link to" to="divide">
          <q-item-section avatar>
            <q-icon name="school" />
          </q-item-section>

          <q-item-section>
            <q-item-label>Деление</q-item-label>
            <q-item-label caption>Проверка знаний деления</q-item-label>
          </q-item-section>
        </q-item>

        <!-- <q-item tag="router-link to" to="divide">
          <q-item-section>
            <q-item-label>{{ $q.platform.is }}</q-item-label>
          </q-item-section>
        </q-item> -->
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref, provide } from "vue";

const leftDrawerOpen = ref(false);
const toggleLeftDrawer = () => {
  leftDrawerOpen.value = !leftDrawerOpen.value;
};
</script>
