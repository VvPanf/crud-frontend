<template>
    <section class="content-section">
        <div v-if="isEmpty === true">
            Список пуст
        </div>
        <table v-else class="table">
            <tr>
                <td v-for="header in headers" class="table__header">{{ header }}</td>
                <td></td>
            </tr>
            <tr v-for="item in list">
                <td v-for="field in item" class="table__item">{{ field }}</td>
                <td>
                    <div v-on:click="deleteItem(Object.values(item)[0])">
                        <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" fill="currentColor" class="bi bi-trash" viewBox="0 0 16 16">
                            <path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0z"/>
                            <path d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4zM2.5 3h11V2h-11z"/>
                        </svg>
                    </div>
                </td>
            </tr>
        </table>
        <div class="pagination">
            <div v-for="(n, page) in totalPages">
                <a v-if="page === currentPage" v-on:click="getPage(page)" class="current-page">{{ page }}</a>
                <a v-else v-on:click="getPage(page)">{{ page }}</a>
            </div>
        </div>
        <div class="pagination-limit">
            <label for="limit">Показывать по: </label>
            <input class="pagination-limit__input" type="text" v-model="countOnPage">
        </div>
    </section>
</template>

<script>
    import axios from 'axios';

    export default {
        name: 'TableComponent',
        data() {
            return {
                headers: [],
                list: [],
                totalPages: 0,
                currentPage: 0,
                countOnPage: 10,
                isEmpty: false,
            }
        },
        created() {
            let baseUrl = new URL(sessionStorage.getItem('baseUrl'));
            axios.get(baseUrl)
            .then((resp) => {
                const data = resp.data.content;
                this.isEmpty = false;
                this.list = data;
                this.totalPages = resp.data.totalPages;
                this.currentPage = resp.data.number;
                this.headers = Object.keys(data[0]);
            })
            .catch((resp) => {
                this.isEmpty = true;
            })
        },
        methods: {
            getPage(page) {
                let baseUrl = sessionStorage.getItem('baseUrl');
                axios.get(baseUrl, {
                    params: {
                        offset: page,
                        limit: this.countOnPage
                    }
                })
                .then((resp) => {
                    const data = resp.data.content;
                    this.isEmpty = false;
                    this.list = data;
                    this.totalPages = resp.data.totalPages;
                    this.currentPage = resp.data.number;
                    this.headers = Object.keys(data[0]);
                })
                .catch((resp) => {
                    this.isEmpty = true;
                })
            },
            deleteItem(id) {
                let deleteUrl = new URL(id, sessionStorage.getItem('baseUrl'));
                axios.delete(deleteUrl)
                .then(
                    this.list = this.list.filter((item) => {
                        return Object.values(item)[0] !== id;
                    })
                );
            }
        }
    };
</script>