<template>
    <div class="panel panel-default">
        <div class="panel-heading">
            <span class="panel-title">{{model.title}}</span>
            <div>
                <router-link :to="'/invoice/' + model.id + '/edit'" class="btn btn-primary btn-sm">Редактировать</router-link>
                <button class="btn btn-danger btn-sm" @click="remove">Удалить</button>
            </div>
        </div>
        <div class="panel-body">
            <div class="row">
                <div class="col-sm-4">
                    <label>Клиент</label>
                    <p>{{model.customer.company}} / {{model.customer.name}}</p>
                    <label>Сумма без скидки</label>
                    <p>{{model.sub_total}}</p>
                    <label>Скидка</label>
                    <p>{{model.discount}}</p>
                    <label>Сумма с учётом скидки</label>
                    <p>{{model.total}}</p>
                </div>
                <div class="col-sm-4">
                    <label>Название</label>
                    <p>{{model.email}}</p>
                    <label>Дата</label>
                    <p>{{model.date}}</p>
                    <label>Due Date</label>
                    <p>{{model.due_date}}</p>
                </div>
                <div class="col-sm-4">
                    <label>Создано</label>
                    <p>{{model.created_at}}</p>
                </div>
            </div>
            <table class="table table-striped table-bordered">
                <thead>
                    <tr>
                        <th>Описание</th>
                        <th>Количество</th>
                        <th>Цена за 1 ед.</th>
                        <th>Общая сумма</th>
                    </tr>
                </thead>
                <thead>
                    <tr v-for="item in model.items">
                        <td>{{item.description}}</td>
                        <td>{{item.qty}}</td>
                        <td>{{item.unit_price}}</td>
                        <td>{{item.qty * item.unit_price}}</td>
                    </tr>
                </thead>
            </table>
        </div>
    </div>
</template>
<script>
    import Vue from 'vue'
    import axios from 'axios'
    export default {
        name: 'InvoiceShow',
        data() {
            return {
                model: {
                    customer: {},
                    items: []
                },
                resource: 'invoice',
                redirect: '/invoice'
            }
        },
        beforeMount() {
            this.fetchData()
        },
        watch: {
            '$route': 'fetchData'
        },
        methods: {
            remove() {
                var vm = this
                axios.delete(`api/${this.resource}/${this.$route.params.id}`)
                    .then(function(response) {
                        if(response.data.deleted) {
                            vm.$router.push(vm.redirect)
                        }
                    })
                    .catch(function(error) {
                        console.log(error)
                    })
            },
            fetchData() {
                var vm = this
                axios.get(`/api/${this.resource}/${this.$route.params.id}`)
                    .then(function(response) {
                        Vue.set(vm.$data, 'model', response.data.model)
                    })
                    .catch(function(error) {
                        console.log(error)
                    })
            }
        }
    }
</script>