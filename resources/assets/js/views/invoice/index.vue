<template>
    <data-viewer :source="source" :thead="thead" :filter="filter" :create="create" :title="title">
        <template scope="props">
            <tr @click="$router.push('/invoice/' + props.item.id)">
                <td>{{props.item.id}}</td>
                <td>{{props.item.date}}</td>
                <td>{{props.item.customer.name}}</td>
                <td>{{props.item.title}}</td>
                <td>{{props.item.total}}</td>
                <td>{{props.item.created_at}}</td>
            </tr>
        </template>
    </data-viewer>
</template>
<script>
    import DataViewer from '../../components/DataViewer.vue'

    export default {
        name: 'InvoiceIndex',
        data() {
            return {
                title: 'Счета для клиентов',
                source: '/api/invoice',
                create: '/invoice/create',
                thead: [
                    {title: 'ID', key: 'id', sort: true},
                    {title: 'Дата', key: 'date', sort: true},
                    {title: 'Клиент', key: 'customer', sort: false},
                    {title: 'Название', key: 'title', sort: true},
                    {title: 'Сумма', key: 'total', sort: true},
                    {title: 'Создано', key: 'created_at', sort: true}
                ],
                filter: [
                    'id', 'customer_id', 'title', 'date', 'due_date', 'discount',
                    'sub_total', 'total', 'created_at',
                    'customer.id', 'customer.company', 'customer.email', 'customer.name',
                    'customer.phone', 'customer.address', 'customer.created_at'
                ]
            }
        },
        components: {
            DataViewer
        }
    }
</script>