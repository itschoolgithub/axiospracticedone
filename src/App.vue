<template>
    <div class="container pt-5">
        <div class="list-group list-group-horizontal">
            <a
                href="javascript:"
                class="list-group-item list-group-item-action"
                v-for="(title, name) in pages"
                :key="title"
                :class="{
                    'active': page == name
                }"
                @click="page = name"
            >
                {{ title }}
            </a>
        </div>
    </div>
    <base-gemini v-if="page == 'gemini'" />
    <base-emoji v-if="page == 'emoji'" />
    <base-gift v-if="page == 'gift'" />
    <base-password v-if="page == 'password'" />
    <base-domain v-if="page == 'domain'" />
</template>

<script>
import BaseDomain from './components/BaseDomain.vue';
import BaseEmoji from './components/BaseEmoji.vue';
import BaseGemini from './components/BaseGemini.vue';
import BaseGift from './components/BaseGift.vue';
import BasePassword from './components/BasePassword.vue';

export default {
  name: 'App',
  data: function () {
    return {
        page: 'gemini',
        pages: {
            gemini: 'Чат',
            emoji: 'Emoji по фильму',
            gift: 'Выбор подарка',
            password: 'Генерация пароля',
            domain: 'Выбор домена',
        },
    };
  },
  components: {
    BaseGemini,
    BasePassword,
    BaseEmoji,
    BaseGift,
    BaseDomain
  }
}
</script>

<style>
    @import url("https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css");
</style>
