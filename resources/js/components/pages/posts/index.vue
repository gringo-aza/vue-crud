<template>
    <div>
        <h1>Все записи</h1>

        <div class="row">
            <div class="col-sm-6">

                <validation-errors
                    v-if="validationErrors"
                    :errors="validationErrors"
                />

                <form @submit.prevent="addPost()" class="mb-4">
                    <div class="form-group">
                        <label for="title">Название</label>
                        <input v-model="post.title" type="text" class="form-control" id="title">
                    </div>
                    <div class="form-group">
                        <label for="description">Описание</label>
                        <textarea v-model="post.description" class="form-control" id="description" rows="3"></textarea>
                    </div>
                    <button type="submit" class="btn btn-primary">Сохранить</button>
                    <button v-if="edit" @click="edit = false" type="submit" class="btn btn-light">Отмена</button>
                </form>
            </div>
        </div>

        <div v-if="errored" class="alert alert-danger" role="alert">
            Записи не загрузились, попробуйте позже :(
        </div>

        <table v-else class="table table-striped">
            <div v-if="loading">loading...</div>
            <thead>
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">First</th>
                    <th scope="col">Last</th>
                    <th scope="col">Handle</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in posts" :key="post.id">
                        <th>{{ post.id }}</th>
                        <td>{{ post.title }}</td>
                        <td>{{ post.description }}</td>
                        <td>
                            <button @click="editPost(post)" class="btn btn-success">
                                <i class="fa fa-pencil"></i>
                            </button>
                            <button @click="deletePost(post.id)" class="btn btn-danger">
                                <i class="fa fa-trash">
                            </i></button>
                        </td>
                </tr>
            </tbody>
        </table>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li :class="{ disabled: !pagination.prev_page_url }"
                    @click.prevent="getPosts(pagination.prev_page_url)"
                    class="page-item"
                ><a class="page-link" href="#">Назад</a></li>
                <li class="page-item disabled"><a class="page-link" href="#">
                    Страница {{ pagination.current_page }} из {{ pagination.last_page }}
                </a></li>
                <li :class="{ disabled: !pagination.next_page_url }"
                    @click.prevent="getPosts(pagination.next_page_url)"
                    class="page-item"
                ><a class="page-link" href="#">Следующая</a></li>
            </ul>
        </nav>
    </div>
</template>

<!--by Aziz Rozmetov => Gringo 2021-02-25, Thu, 3:51-->

<script>
    export default {
        name: '',
        components: {},
        data(){
            return {
                posts: [],
                post: {
                    id:'',
                    title:'',
                    description: '',
                },
                pagination:{},
                edit: false,
                loading: false,
                errored: false,
                validationErrors: '',
            }
        },
        mounted () {
            this.getPosts()
        },
        methods: {
            getPosts(page_url){
               page_url = page_url || '/api/posts'
                axios
                .get(page_url)
                .then(response => {
                    this.posts = response.data.data
                    this.makePagination(response.data)
                })
                .catch(error => {
                    console.log(error)
                    this.errored = true;
                })
                .finally(() => this.loading= false);
            },
            makePagination(response) {
                this.pagination = {
                    current_page: response.current_page,
                    last_page: response.last_page,
                    prev_page_url: response.prev_page_url,
                    next_page_url: response.next_page_url
                }
            },
            deletePost(id){
                axios
                    .delete(`/api/posts/${id}`)
                    .then(response => {
                        this.getPosts()
                        console.log(response)
                })
                    .catch(error => {
                    console.log(error);
                })
            },
            addPost(){
                if (this.edit === false){
                    axios
                        .post('/api/posts', {
                            title: this.post.title,
                            description: this.post.description
                    })
                    .then(response => {
                            this.post.title = ''
                            this.post.description = ''
                            this.getPosts()

                            console.log(response)
                    })
                    .catch(error => {
                        if (error.response.status === 422) {
                            this.validationErrors = error.response.data.errors
                        }
                        console.log(error)
                    })
                } else {
                    axios
                    .put(`/api/posts/${this.post.id}`, {
                        title: this.post.title,
                        description: this.post.description
                    })
                    .then(response => {
                        this.post.title = ''
                        this.post.description = ''
                        this.edit = false
                        this.getPosts()

                        alert('Пост добавлен!')
                        console.log(response)
                    })
                    .catch(error => {
                        if(error.response.status === 422){
                            this.validationErrors = error.response.data.errors
                        }
                        console.log(error);
                    })
                }
            },
            editPost(post){
                this.edit = true
                this.post.id = post.id
                this.post.title = post.title
                this.post.description = post.description
            }

        }
    }

</script>

<style scoped>

</style>

<!--fileName => index.vue path => resources\js\components\pages\posts\index.vue -->
