<template>
    <section>
        <input type="text" v-model="baseUrl">
        <button v-on:click="saveUrl()">Сохранить</button>
    </section>
</template>

<script>
    export default {
        name: 'UrlComponent',
        data() {
            return {
                baseUrl: '',
            }
        },
        created() {
            let url = sessionStorage.getItem('baseUrl');
            if (url) {
                this.baseUrl = url;
            } else {
                this.baseUrl = 'http://localhost:8080/';
                sessionStorage.setItem('baseUrl', this.baseUrl);
            }
        },
        methods: {
            saveUrl() {
                sessionStorage.setItem('baseUrl', this.baseUrl);
            }
        }
    }
</script>