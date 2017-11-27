<template>
    <div class="panel panel-default">
        <div class="panel-heading">
            {{title}}
        </div>
        <div class="panel-body">
            <form class="form" @submit.prevent="save">
                <div class="row">
                    <div class="col-sm-4">
                        <div class="form-group">
                            <label>Клиент</label>
                            <select class="form-control" v-model="form.customer_id">
                                <option>Выбрать</option>
                                <option v-for="customer in option.customers" :value="customer.id">
                                    {{customer.company}} / {{customer.name}}
                                </option>
                            </select>
                            <small class="text-danger" v-if="errors.customer_id">{{errors.customer_id[0]}}</small>
                        </div>
                    </div>
                    <div class="col-sm-4">
                        <div class="form-group">
                            <label>Дата</label>
                            <input type="date" class="form-control" v-model="form.date">
                            <small class="text-danger" v-if="errors.date">{{errors.date[0]}}</small>
                        </div>
                        <div class="form-group">
                            <label>Срок оплаты</label>
                            <input type="date" class="form-control" v-model="form.due_date">
                            <small class="text-danger" v-if="errors.due_date">{{errors.due_date[0]}}</small>
                        </div>
                    </div>
                    <div class="col-sm-4">
                        <div class="form-group">
                            <label>Название</label>
                            <textarea class="form-control" v-model="form.title"></textarea>
                            <small class="text-danger" v-if="errors.title">{{errors.title[0]}}</small>
                        </div>
                    </div>
                </div>
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th>Описание</th>
                            <th>Цена за 1 ед.</th>
                            <th>Количество</th>
                            <th>Общая сумма</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item, index) in form.items">
                            <td><input type="text" v-model="item.description" class="form-control"></td>
                            <td><input type="text" v-model="item.unit_price" class="form-control"></td>
                            <td><input type="text" v-model="item.qty" class="form-control"></td>
                            <td>
                                {{parseFloat(item.qty) * parseFloat(item.unit_price)}}
                            </td>
                            <td>
                                <button type="button" @click="form.items.splice(index, 1)">&times;</button>
                            </td>
                        </tr>
                    </tbody>
                    <tfoot>
                        <tr>
                            <td colspan="2">
                                <a @click.stop="addLine">Добавить</a>
                            </td>
                            <td>Сумма без скидки</td>
                            <td>{{subTotal}}</td>
                        </tr>
                        <tr>
                            <td colspan="2">
                            </td>
                            <td>Скидка</td>
                            <td>
                                <input type="text" v-model="form.discount">
                            </td>
                        </tr>
                        <tr>
                            <td colspan="2">

                            </td>
                            <td>Сумма с учётом скидки</td>
                            <td>{{total}}</td>
                        </tr>
                    </tfoot>
                </table>
                <button class="btn btn-success">Сохранить</button>
            </form>
        </div>
    </div>
</template>
<script>
    import Vue from 'vue'
    import axios from 'axios'
    export default {
        name: 'InvoiceForm',
        data() {
            return {
                form: {
                    items: []
                },
                errors: {},
                option: {
                    customers: []
                },
                title: 'Создать',
                initialize: '/api/invoice/create',
                redirect: '/invoice',
                store: '/api/invoice',
                method: 'post'
            }
        },
        beforeMount() {
            if(this.$route.meta.mode === 'edit') {
                this.title = 'Редактировать'
                this.initialize = '/api/invoice/' + this.$route.params.id + '/edit'
                this.store = '/api/invoice/' + this.$route.params.id
                this.method = 'put'
            }
            this.fetchData()
        },
        watch: {
            '$route': 'fetchData'
        },
        computed: {
            subTotal() {
                return this.form.items.reduce(function(carry, item) {
                    return carry + parseFloat(item.qty) * parseFloat(item.unit_price)
                }, 0)
            },
            total() {
                return this.subTotal - parseFloat(this.form.discount)
            }
        },
        methods: {
            addLine() {
                this.form.items.push({
                    description: '',
                    unit_price: 0,
                    qty: 1
                })
            },
            fetchData() {
                var vm = this
                axios.get(this.initialize)
                    .then(function(response) {
                        Vue.set(vm.$data, 'form', response.data.form)
                        Vue.set(vm.$data, 'option', response.data.option)
                    })
                    .catch(function(error) {
                        console.log(error)
                    })
            },
            save() {
                var vm = this
                axios[this.method](this.store, this.form)
                    .then(function(response) {
                        if(response.data.saved) {
                            vm.$router.push(vm.redirect)
                        }
                    })
                    .catch(function(error) {
                        Vue.set(vm.$data, 'errors', error.response.data)
                    })
            }
        }
    }
</script>