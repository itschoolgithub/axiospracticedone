<template>
    <div class="container py-5">
        <div class="row">
            <div class="col-12 mb-4">
                <h1>Выбор подарков</h1>
            </div>
            <div class="col-12 mb-4">
                <div class="mb-3">
                    <select class="form-select" v-model="forHoliday">
                        <option value="">Что за праздник?</option>
                        <option>Новый Год</option>
                        <option>День Рождения</option>
                        <option>8 Марта</option>
                        <option>23 Февраля</option>
                        <option>День Святого Валентина</option>
                        <option>День Защитника Отечества</option>
                        <option>Рождество</option>
                        <option>Пасха</option>
                        <option>День Матери</option>
                        <option>День Отца</option>
                        <option>День Знаний (1 сентября)</option>
                        <option>День Свадьбы</option>
                        <option>Новоселье</option>
                        <option>День Рождения Компании</option>
                    </select>
                </div>
                <div class="mb-3">
                    <select class="form-select" v-model="forWho">
                        <option value="">Кому?</option>
                        <option>Друг</option>
                        <option>Подруга</option>
                        <option>Родители</option>
                        <option>Родители мужа/жены</option>
                        <option>Дети</option>
                        <option>Братья и сестры</option>
                        <option>Любимый человек</option>
                        <option>Коллеги</option>
                        <option>Начальник</option>
                        <option>Учителя/Преподаватели</option>
                        <option>Врачи</option>
                        <option>Соседи</option>
                        <option>Домашние животные</option>
                    </select>
                </div>
                <div class="mb-3">
                    <select class="form-select" v-model="money">
                        <option value="">Какой бюджет?</option>
                        <option>До 1000 тенге</option>
                        <option>До 5000 тенге</option>
                        <option>До 10000 тенге</option>
                        <option>До 50000 тенге</option>
                        <option>До 100000 тенге</option>
                    </select>
                </div>
                <div class="mb-2">
                    <label class="form-label">Что ему/ей нравится?</label>
                    <textarea class="form-control" v-model="hobby"></textarea>
                </div>
                <div class="mt-4" v-if="gift">
                    <h5>Можно подарить</h5>
                    <ul class="list-group mt-3">
                        <li class="list-group-item fs-4">
                            {{ gift }}
                        </li>
                    </ul>
                </div>
                <div class="mt-4">
                    <button
                        class="btn btn-primary"
                        @click="getGift"
                        :disabled="loaded"
                    >
                        {{ buttonTitle }}
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
export default {
    data: function () {
        return {
            forHoliday: '',
            forWho: '',
            money: '',
            hobby: '',
            gift: '',
            loaded: false,
            except: '',
        };
    },
    computed: {
        buttonTitle: function () {
            if (this.gift)
                return 'Другой подарок';
            else
                return 'Подобрать подарок';
        },
        prompt: function () {
            let temp = '';
            if (this.forHoliday) {
               temp += 'на ' + this.forHoliday; 
            }
            if (this.forWho) {
               temp += ' для ' + this.forWho; 
            }
            if (this.money) {
               temp += ' в бюджете ' + this.money; 
            }
            if (this.hobby) {
               temp += ' ему/ей нравится ' + this.hobby; 
            }
            if (this.except) {
               temp += ' кроме ' + this.except; 
            }
            return `Напиши что можно подарить ${temp}. Напиши только одно название подарка, без объяснений.`;
        }
    },
    methods: {
        getGift: async function () {
            this.loaded = true;
            this.gift = '...';
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
            this.loaded = false;
            this.gift = result.data.candidates[0].content.parts[0].text;
            this.except += this.gift + ', ';
        }
    }
};
</script>