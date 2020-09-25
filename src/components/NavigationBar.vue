<template>
<div>
    <b-nav>

        <b-nav-item active>
            <router-link to="/">Products </router-link> |
        </b-nav-item>
        <b-nav-item active>
            <router-link to="/order">Price Calculator</router-link>
        </b-nav-item>
    </b-nav>
</div>
</template>

<script>
export default {
    name: "NavigationBar"
}
</script>
