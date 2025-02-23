<template>
    <div class="container py-5">
        <div class="mb-3">
            <input
                type="text"
                class="form-control"
                v-model="question"
                @keyup.enter="send"
            >
        </div>
        <div class="mb-4">
            <button
                class="btn btn-primary"
                :disabled="loading || !question"
                @click="send"
            >Отправить</button>
        </div>
        <p>{{ answer }}</p>
    </div>
</template>

<script>
import axios from "axios";
export default {
    data: function () {
        return {
            question: '',
            answer: '',
            loading: false
        };
    },
    methods: {
        send: async function () {
            this.loading = true;
            let result = await axios.post("https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=AIzaSyB3i7skspriAuUzCnFwdClKhENGvQhVZe8", {
                contents: [{
                    parts: [{
                        text: this.question
                    }]
                }]
            });
            this.answer = result.data.candidates[0].content.parts[0].text;
            this.loading = false;
            this.question = '';
        }
    }
};
</script>