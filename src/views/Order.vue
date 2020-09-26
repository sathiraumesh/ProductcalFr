<template>
<div id="price-cart" class="conatiner">
    <div class="row">

        <div class="col-md-2">
        </div>
        <div class="col-md-8">

            <b-jumbotron>
                <h4>Price Cart</h4>
                <br>
                <div class="row ">
                    <div class="col-md-1">

                    </div>
                    <div class="col-md-1">

                    </div>
                </div>
                <div class="row ">

                    <div class="col-md-1">

                    </div>
                    <div class="col-md-3">
                        Select product
                        <b-form-select v-model="selectedProduct" :options="productOptions"></b-form-select>
                    </div>

                    <div class="col-md-3">
                        Select unit type
                        <b-form-select v-model="selectedQuantityOption" :options="quantityOptions"></b-form-select>
                    </div>
                    <div class="col-md-3">
                        Select No of units
                        <input v-model="quantity" type="number">

                    </div>

                    <div class="col-md-1">
                        Add
                        <b-button variant="success" @click="addProduct">+</b-button>

                    </div>

                    <div class="col-md-1">

                    </div>
                </div>

                <div class="row">
                    <div class="col-md-12">
                        <hr>
                    </div>
                </div>

                <div class=" row ">

                    <div class="col-md-3">

                    </div>
                    <div class="col-md-1">
                        <h6> No</h6>
                    </div>

                    <div class="col-md-2">
                        <h6> Product</h6>
                    </div>
                    <div class="col-md-2">
                        <h6> Quantity</h6>
                    </div>

                    <div class="col-md-2">

                    </div>

                </div>

                <div v-for=" (item,index) in order" :key="index" class="row">
                    <br>
                    <br>

                    <div class="col-md-3">

                    </div>
                    <div class="col-md-1 product-info">
                        {{index+1}}
                    </div>

                    <div class="col-md-2 product-info">
                        {{item.name}}
                    </div>
                    <div class="col-md-2 product-info">
                        {{item.quantity +" "+ item.quantityType}}
                    </div>

                    <div class="col-md-3">
                        <b-button variant="danger" @click="removeProductFromOrder(index)">-</b-button>

                    </div>

                </div>

                <div v-if="priceCalculated" class="row">

                    <div class="col-md-8">

                    </div>
                    <div class="col-md-4">
                        <h5> Total Price {{price +"$"}}</h5>
                    </div>
                </div>

                <b-button style="remove-btn" v-if="order.length>0" variant="primary" @click=" calculatePriceOfOrder">Calculate Price</b-button>
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
            axios.get("product")
                .then(response => {
                    this.products = response.data;

                    this.productOptions = this.products.map(product => {

                        return {
                            value: product.id,
                            text: product.name
                        }
                    });

                })
                .catch(err => {
                    if (err) {
                        this.$toasted.show("Error", this.toastOptions);
                    }

                })
        },

        getPriceOfOrder(order) {
            axios.post("product/price", order)
                .then(response => {
                    this.price = response.data;

                })
                .catch(err => {
                    if (err) {
                        this.$toasted.show("Error", this.toastOptions);
                    }

                })
        },

        addProduct() {

            if (this.selectedProduct !== null && this.quantity > 0) {

                let isProductInOrder = false;
                if (this.order.length > 0) {

                    this.order.forEach(item => {
                        isProductInOrder = this.selectedProduct == item.id ? true : false;
                    });

                    if (isProductInOrder) {

                        this.$toasted.show("product is already in the order", this.toastOptions);
                        return;
                    }
                }

                this.order.push({
                    id: this.selectedProduct,
                    quantityType: this.selectedQuantityOption == 0 ? "cartons" : "units",
                    quantity: this.quantity,
                    name: this.getProductName(this.selectedProduct),
                    units: this.calculateUnits(this.selectedQuantityOption, this.quantity, this.selectedProduct)

                });

                this.priceCalculated = false;
                this.quantity = 0;

            } else {

                this.$toasted.show("Product Should be selcted and quantity cannot be 0", this.toastOptions);
            }

        },

        getProductName(id) {

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
        },

        removeProductFromOrder(index) {

            this.order.splice(index, 1);

            this.priceCalculated = false;
        },

        calculatePriceOfOrder() {

            let orders = this.order.map((product) => {
                return {
                    productId: product.id,
                    units: product.units
                }
            })

            let finalOrder = {
                id: 0,
                order: orders
            }

            this.getPriceOfOrder(finalOrder);
            this.priceCalculated = true;

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
            price: 0,
            priceCalculated: false,
            quantityOptions: [{
                    value: 0,
                    text: "cartons"
                },
                {
                    value: 1,
                    text: "units"
                }
            ],
            selectedQuantityOption: 0,
            toastOptions: {
                type: 'error',
                duration: 1000
            },
        }
    }
}
</script>

<style scoped>
.product-info {
    padding-top: 10px;
}
</style>
