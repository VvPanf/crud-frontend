<template>
    <section class="content-section">
        <div v-for="header in headers" class="input-line">
            <label class="input-line__label">{{ header }}</label>
            <input type="text" v-model="values[header]">
        </div>
        <div>
            <button v-on:click="sendCreate()">Создать</button>
        </div>
    </section>
</template>

<script>
    import axios from 'axios';
    export default {
        data() {
            return {
                headers: [],
                values: {}
            }
        },
        created() {
            let baseUrl = sessionStorage.getItem('baseUrl');
            axios.get(baseUrl)
            .then((resp) => {
                const data = resp.data.content;
                this.headers = Object.keys(data[0]).slice(1);
                this.headers.forEach((item, index) => {
                    if (index === 0) return;
                    this.values[item] = "";
                });
            })
        },
        methods: {
            sendCreate() {
                let baseUrl = sessionStorage.getItem('baseUrl');
                axios.post(baseUrl, 
                    JSON.stringify(this.values), {
                    headers: {
                        'Content-Type': 'application/json',
                    },
                }).then((resp) => {
                    alert('Запись добавлена');
                }).catch((resp) => {
                    alert('Возникла ошибка');
                    console.log(resp.data);
                });
            }
        }
    };
</script>