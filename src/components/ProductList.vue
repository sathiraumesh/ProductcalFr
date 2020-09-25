<template>
<div id="product-list" class="conatiner">
    <div class="row">

        <div class="col-md-2">
        </div>
        <div class="col-md-8">

            <b-jumbotron>
                <h4>Product List</h4>
                <br>
                <div class="row ">
                    <div class="col-md-1">

                    </div>
                    <div class="col-md-1">

                    </div>
                </div>
                <div class="row ">
                    <div class="col-md-3">

                    </div>
                    <div class="col-md-1">
                        <h6> No</h6>
                    </div>
                    <div class="col-md-3">
                        <h6> Product Name</h6>

                    </div>

                    <div class="col-md-1">
                        <h6> Price </h6>

                    </div>

                    <div class="col-md-1">
                        Units
                        <b-form-select class="unit-box" :value="selected" @input="setSelected" :options="units"></b-form-select>
                    </div>
                    <div class="col-md-1">

                    </div>

                </div>

                <div class="row ">
                    <div class="col-md-3">

                    </div>
                    <div class="col-md-6">
                        <hr>
                    </div>

                    <div class="col-md-3">

                    </div>

                </div>

                <div v-for=" (product,index) in products" :key="index" class="row">

                    <div class="col-md-3">

                    </div>

                    <div class="col-md-1">
                        {{index+1}}
                    </div>
                    <div class="col-md-3">
                        {{product.name}}
                    </div>

                    <div class="col-md-2">
                        {{product.price}}$
                    </div>

                    <div class="col-md-3">

                    </div>

                </div>
            </b-jumbotron>
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios'
export default {

    name: 'ProductList',

    methods: {

        setUnitCounter() {
            for (let i = 1; i <= 50; i++) {
                this.units.push({
                    value: i,
                    text: i
                });
            }
        },

        setSelected(value) {

            axios.get(`http://localhost:5000/api/product/price/${value}`)
                .then(response => {
                    this.products = response.data

                })
                .catch(err => {
                    if (err) {
                        this.$toasted.show('Error Loading');
                    }

                })

        }

    },
    mounted() {
        this.setUnitCounter()
        this.setSelected(20)

    },

    watch: {

    },
    data() {
        return {
            selected: 1,
            units: [],
            products: []
        }
    },

}
</script>

<style scoped>
.unit-box {
    width: 60px;
    height: 30px;
    padding-top: 0px;
    padding-bottom: 0px;
}
</style>

<!-- Add "scoped" attribute to limit CSS to this component only -->
