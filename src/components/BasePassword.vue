<template>
    <div class="container py-5">
        <div class="row">
            <div class="col-12 mb-4">
                <h1>Генератор паролей</h1>
            </div>
            <div class="col-12 mb-5">
                <!-- Если есть ошибки, то показываем блок ошибок и выводим в них ошибку -->
                <div class="alert alert-danger" v-if="error">
                    {{ error }}
                </div>
                <div class="mb-2">
                    <label class="form-label">Укажите количество символов:</label>
                    <input type="number" class="form-control" v-model="count">
                    <!-- Двусторонняя связь с переменной count -->
                </div>
                <div class="form-check mb-2">
                    <input class="form-check-input" type="checkbox" v-model="uppercase" id="uppercase">
                    <!-- Двусторонняя связь с переменной uppercase -->
                    <label class="form-check-label" for="uppercase">
                        Использовать заглавные буквы
                    </label>
                </div>
                <div class="form-check mb-2">
                    <input class="form-check-input" type="checkbox" v-model="lowercase" id="lowercase">
                    <!-- Двусторонняя связь с переменной lowercase -->
                    <label class="form-check-label" for="lowercase">
                        Использовать прописные буквы
                    </label>
                </div>
                <div class="form-check mb-2">
                    <input class="form-check-input" type="checkbox" v-model="special" id="special">
                    <!-- Двусторонняя связь с переменной special -->
                    <label class="form-check-label" for="special">
                        Использовать спецсимволы
                    </label>
                </div>
                <div class="form-check mb-2">
                    <input class="form-check-input" type="checkbox" v-model="numbers" id="numbers">
                    <!-- Двусторонняя связь с переменной numbers -->
                    <label class="form-check-label" for="numbers">
                        Использовать цифры
                    </label>
                </div>
                <div class="mt-4">
                    <button
                        class="btn btn-primary"
                        :disabled="loading || count <= 0"
                        @click="getPassword"
                    >Сгенерировать пароль</button>
                    <!-- При клике вызываем функцию getPassword -->
                    <!-- Если происходит ожидание ответа или количество символов меньше 1, то кнопку отключаем -->
                </div>
                <!-- Если еще нет сгенерированного пароль, не показываем блок -->
                <div class="mt-4" v-if="password">
                    <label class="form-label">Результат:</label>
                    <input type="text" readonly class="form-control" v-model="password">
                </div>
            </div>
        </div>
    </div>
</template>

<script>
// Импортируем аксиос
import axios from 'axios';

export default {
    data: function () {
        return {
            count: 12, // количество символов пароля
            uppercase: true, // можно ли использовать заглавные
            lowercase: true, // можно ли использовать строчные
            special: true, // можно ли использовать спецсимволы
            numbers: true, // можно ли использовать числа
            password: '', // результат - сгенерированный пароль
            loading: false, // ожидаем ли сейчас ответа от gemini
            error: '' // текст ошибки
        };
    },
    computed: {
        // вычисляемое свойство - промпт для gemini
        prompt: function () {
            // собираем строку по кусочкам
            let temp = `Сгенерируй пароль из ${this.count} символов, `;

            if (!this.uppercase) {
                temp += 'не ';
            }
            temp += 'используй заглавные буквы, ';

            if (!this.lowercase) {
                temp += 'не ';
            }
            temp += 'используй строчные буквы, ';

            if (!this.special) {
                temp += 'не ';
            }
            temp += 'используй спецсимволы, ';

            if (!this.numbers) {
                temp += 'не ';
            }
            temp += 'используй числа. ';

            // возвращаем полученную строку
            return temp + 'Напиши только пароль, без пояснений.';
        }
    },
    methods: {
        // функция отправки запроса в gemini
        getPassword: async function () {
            // очищаем ранние ошибки и устанавливаем переменную ожидания ответа
            this.error = '';
            this.loading = true;
            // пытаемся отправить запрос
            try {
                // ожидаем ответа от сервера
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
                // пришел ответ - устанавливаем его в переменую 
                this.password = result.data.candidates[0].content.parts[0].text;
            } catch (error) {
                // если произошла ошибка, выводим ее в консоль
                console.log(error); 
                // и устанавливаем в переменную
                this.error = error.response.data.error.message;
            } finally {
                // в конце, в любом случае убираем ожидание ответа
                this.loading = false;
            }
        }
    }
};
</script>