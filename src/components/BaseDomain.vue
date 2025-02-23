<template>
    <div class="container py-5">
        <div class="row">
            <div class="col-12 mb-4">
                <h1>Выбор домена</h1>
            </div>
            <div class="col-12 mb-5">
                <div class="mb-3">
                    <label class="form-label">Опишите свою компанию:</label>
                    <textarea class="form-control" v-model="company"></textarea>
                </div>
                <div class="mb-3">
                    <select class="form-select" v-model="zone">
                        <option value="">Доменная зона</option>
                        <option>.com</option>
                        <option>.kz</option>
                        <option>.ru</option>
                    </select>
                </div>
                <div class="mb-3">
                    <select class="form-select" v-model="length">
                        <option value="">Длина домена</option>
                        <option>Короткий (3-5 символов)</option>
                        <option>Средний (5-8 символов)</option>
                        <option>Длинный (более 8 символов)</option>
                    </select>
                </div>
                <div class="form-check mb-3">
                    <input class="form-check-input" type="checkbox" v-model="dash" id="dash">
                    <label class="form-check-label" for="dash">
                        Использовать тире
                    </label>
                </div>
                <p>{{ prompt }}</p>
                <div class="mt-4" v-if="domain">
                    <h5>Как вам?</h5>
                    <ul class="list-group mt-3">
                        <li class="list-group-item d-flex justify-content-between">
                            <div class="fs-4">{{ domain }}</div>
                            <div>
                                <button
                                    class="btn btn-success"
                                    @click="addVariant"
                                >Сохранить вариант</button>
                            </div>
                        </li>
                    </ul>
                </div>
                <div class="mt-4">
                    <button
                        class="btn btn-primary"
                        @click="getDomain"
                        :disabled="loading || !company"
                    >{{ buttonTitle }}</button>
                </div>
            </div>
            <div class="col-12 mb-4" v-if="history.length">
                <h3>Сохраненные варианты:</h3>
                <ul class="list-group mt-3">
                    <li
                        class="list-group-item d-flex justify-content-between"
                        v-for="(one, index) in history"
                        :key="index"
                    >
                        <div class="fs-4">{{ one }}</div>
                        <div>
                            <button
                                class="btn btn-outline-danger"
                                @click="removeVariant(index)"
                            >Удалить</button>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    data: function () {
        return {
            company: '',
            zone: '',
            length: '',
            dash: false,
            loading: false,
            domain: '',
            history: []
        };
    },
    computed: {
        buttonTitle: function () {
            return this.domain ? 'Получить другой вариант' : 'Получить вариант';
        },
        prompt: function () {
            let temp = `Сгенерируй `;

            if (this.length) {
                temp += this.length;
            }

            temp += ' домен ';

            if (this.zone) {
                temp += ` в доменной зоне ${this.zone} `;
            } else {
                temp += ` в любой доменной зоне `;
            }


            if (this.company) {
                temp += ` для компании "${this.company}"`;
            }

            temp += '. ';

            if (this.dash) {
                temp += ' Можешь использовать тире в названии. ';
            } else {
                temp += ' Не используй тире в названии. ';
            }

            return temp + 'Напиши только один домен, без пояснений.';
        }
    },
    methods: {
        addVariant: function () {
            this.history.unshift(this.domain);
            this.domain = '';
        },
        removeVariant: function (index) {
            this.history.splice(index, 1);
        },
        getDomain: async function () {
            this.loading = true;
            let result = await axios.post("https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=AIzaSyB3i7skspriAuUzCnFwdClKhENGvQhVZe8", {
            contents: [
                {
                    parts: [
                        {
                            text: this.prompt // промпт из вычисляемого свойства
                        }
                    ]
                }
            ] 
            });
            this.domain = result.data.candidates[0].content.parts[0].text;
            this.loading = false;
        }
    },
};
</script>