<template>
    <div class="container py-5">
        <div class="row">
            <div class="col-12 mb-4">
                <h1>Emoji по фильму</h1>
            </div>
            <div class="col-12 mb-5">
                <div class="mb-2">
                    <label class="form-label">Введите название фильма:</label>
                    <input type="text" class="form-control" v-model="film">
                </div>
                <div class="mb-2">
                    <label class="form-label">Выберите количество Emoji:</label>
                    <input type="number" class="form-control" v-model="count">
                </div>
                <div class="mt-4">
                    <button
                        class="btn btn-primary"
                        @click="getEmoji"
                        :disabled="loading || !film"
                    >Получить Emoji</button>
                </div>
            </div>
            <div class="col-12 mb-4" v-if="history.length > 0">
                <h3>Результаты:</h3>
                <ul class="list-group mt-3">
                    <li
                        class="list-group-item"
                        v-for="(one, index) in history"
                        :key="index"
                    >
                        <div class="fs-5">{{ one.film }}</div>
                        <div class="fs-3">{{ one.result }}</div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
export default {
    data: function () {
        return {
            film: '',
            count: 5,
            loading: false,
            history: []
        };
    },
    computed: {
        prompt: function () {
            return `Напиши ${this.count} emoji, которые ассоциируются с сюжетом фильма '${this.film}'. Напиши только emoji без пояснений.`;
        }
    },
    methods: {
        getEmoji: async function () {
            this.loading = true;
            let result = await axios.post("https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=AIzaSyB3i7skspriAuUzCnFwdClKhENGvQhVZe8", {
               contents: [
                {
                    parts: [
                        {
                            text: this.prompt
                        }
                    ]
                }
               ] 
            });
            this.loading = false;
            this.history.unshift({
                film: this.film,
                result: result.data.candidates[0].content.parts[0].text
            });
            this.film = '';
        }
    }
};
</script>