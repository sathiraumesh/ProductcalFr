<template>
<div class="product-list">
    <ProductList />
</div>
</template>

<script>
// @ is an alias to /src
import ProductList from '@/components/ProductList.vue'

export default {
    name: 'Home',
    components: {
        ProductList
    },

    data() {
        return {

        }

    },

}
</script>
