<template>
<div id="product-calculator" class="conatiner">
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
                        Product
                        <b-form-select v-model="selectedProduct" :options="productOptions"></b-form-select>
                    </div>

                    <div class="col-md-2">
                        Unit Type
                        <b-form-select v-model="selectedQuantityOption" :options="quantityOptions"></b-form-select>
                    </div>
                    <div class="col-md-3">
                        No of Units
                        <input v-model="quantity" type="number">

                    </div>

                    <div class="col-md-1">

                        Product
                        <b-button variant="success" v-on:click="addProduct">+</b-button>

                    </div>

                </div>

                <div class=" row ">
                    <div class=" col-md-3">

                    </div>
                    <div class="col-md-6">
                        <hr>
                    </div>

                    <div class="col-md-3">

                    </div>

                </div>

                <div v-for=" (item,index) in order" :key="index" class="row">

                    <div class="col-md-3">
                        {{index+1}}
                    </div>

                    <div class="col-md-1">
                        {{item.name}}
                    </div>
                    <div class="col-md-3">
                        {{item.quantity +" "+ item.quantityType}}
                    </div>

                    <div class="col-md-2">

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
    name: 'Order',

    methods: {
        getProductList() {
            axios.get("http://localhost:5000/api/product")
                .then(response => {
                    this.products = response.data;
                    console.log(this.products)
                    this.productOptions = this.products.map(product => {

                        return {
                            value: product.id,
                            text: product.name
                        }
                    });

                })
                .catch(err => {
                    if (err) {
                        this.$toasted.show("Error");
                    }

                })
        },

        addProduct() {

            if (this.selectedProduct !== null && this.quantity > 0) {

                this.order.push({
                    id: this.selectedProduct,
                    quantityType: this.selectedQuantityOption == 0 ? "cartons" : "units",
                    quantity: this.quantity,
                    name: this.getProductName(this.selectedProduct),

                });

            } else {

                this.$toasted.show("error");
            }

        },

        getProductName(id) {
            console.log(id)
            let product = this.products.filter(product => {
                return product.id == id
            });

            return product.pop().name;
        },
        calculateUnits(quantityOption, quantity, productId) {

            if (quantityOption == 0) {
                let product = this.products.filter(product => {
                    return product.id == productId
                });

                let unitsPerCarton = product.pop().unitsPerCarton
                return unitsPerCarton * quantity;
            }

            return quantity;
        }

    },

    mounted() {

        this.getProductList();
    },

    data() {
        return {
            products: [],
            productOptions: [],
            selectedProduct: null,
            order: [],
            quantity: 0,
            quantityOptions: [{
                    value: 0,
                    text: "cartons"
                },
                {
                    value: 1,
                    text: "units"
                }
            ],
            selectedQuantityOption: 0
        }
    }
}
</script>
