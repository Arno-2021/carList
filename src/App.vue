<template>
    <div id="app">
        <!-- 卡片区域 -->
        <div class="card">
            <div class="card-header">
                添加品牌
            </div>
            <div class="card-body">
                <!-- 行内的表单 -->
                <form class="form-inline">
                    <div class="col-auto">
                        <label
                            class="sr-only"
                            for="inlineFormInputGroup"
                        ></label>
                        <div class="input-group mb-2">
                            <div class="input-group-prepend">
                                <div class="input-group-text">品牌名称</div>
                            </div>
                            <input
                                type="text"
                                class="form-control"
                                v-model="brand"
                            />
                        </div>
                    </div>
                    <div class="col-auto">
                        <label
                            class="sr-only"
                            for="inlineFormInputGroup"
                        ></label>
                        <div class="input-group mb-2">
                            <div class="input-group-prepend">
                                <div class="input-group-text">品牌价格</div>
                            </div>
                            <input
                                type="number"
                                class="form-control"
                                v-model="price"
                            />
                        </div>
                    </div>
                    <button
                        type="button"
                        class="btn btn-primary mb-2"
                        @click="add"
                    >
                        添加
                    </button>
                </form>
            </div>
        </div>

        <!-- 表格区域 -->
        <table class="table table-bordered mt-2">
            <thead>
                <tr>
                    <th>#</th>
                    <th>车辆名称</th>
                    <th>车辆价格</th>
                    <th>创建时间</th>
                    <th>操作</th>
                </tr>
            </thead>
            <tbody v-if="list.length > 0">
                <tr v-for="(item, index) in list" :key="item.id">
                    <td>{{ index + 1 }}</td>
                    <td>{{ item.brand }}</td>
                    <td :style="{ color: item.price > 50 ? 'red' : '' }">
                        {{ item.price | twoFloats }}
                    </td>
                    <td>{{ item.time | timeFomatter }}</td>
                    <td>
                        <a href="#" @click="del(item.id)">删除</a>
                    </td>
                </tr>
                <tr class="footer">
                    <td>统计</td>
                    <td colspan="2">总价钱：{{ total | twoFloats }}</td>
                    <td colspan="2">平均价：{{ avg | twoFloats }}</td>
                </tr>
            </tbody>
            <tbody v-else>
                <tr>
                    <td colspan="5" class="text-center">没有更多数据</td>
                </tr>
            </tbody>
        </table>
        <div></div>
    </div>
</template>

<script>
import moment from 'moment'
export default {
    data() {
        return {
            list: JSON.parse(localStorage.getItem('carList')) || [],
            brand: '',
            price: 0,
        }
    },
    filters: {
        twoFloats(num) {
            return num.toFixed(2) + '万'
        },
        timeFomatter(time) {
            return moment(time).format('YYYY-MM-DD')
        },
    },
    methods: {
        del(id) {
            this.list = this.list.filter(v => {
                return v.id !== id
            })
        },
        add() {
            if (!this.brand || !this.price) {
                return
            }
            this.list.unshift({
                id: +new Date(),
                brand: this.brand,
                price: +this.price,
                time: new Date(),
            })
            this.brand = ''
            this.price = 0
        },
    },
    computed: {
        total() {
            return this.list.reduce((sum, v) => {
                return (sum += v.price)
            }, 0)
        },
        avg() {
            return this.list.length > 0 ? this.total / this.list.length : 0
        },
    },
    watch: {
        list: {
            deep: true,
            handler(newVal) {
                localStorage.setItem('carList', JSON.stringify(newVal))
            },
        },
    },
}
</script>

<style lang="less">
#app {
    width: 60%;
    margin: 50px auto;
    a {
        margin-right: 5px;
    }
    .footer {
        background-color: rgba(0, 0, 0, 0.05);
    }
}
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    // chrome
    -webkit-appearance: none;
    appearance: none;
    margin: 0;
}
</style>
