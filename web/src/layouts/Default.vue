<template>
        <div>
            <transition name="fade" mode="out-in">
                <header-nav :key="headerKey"/>
            </transition>

            <main class="main">
                <transition name="fade" mode="out-in" v-on:before-leave="beforeLeave">
                    <slot />
                </transition>
            </main>
        </div>
</template>

<script>
    import HeaderNav from '~/components/HeaderNav'

    export default {
        data() {
            return {
                headerKey: 0
            }
        },
        components: {
            HeaderNav,
        },
        methods: {
            beforeLeave: function(el) {
                if (window.scrollY > 1) {
                    this.headerKey += 1;
                }
            }
        }
    }
</script>

<style>
    .fade-enter-active {
        transition: opacity .5s;
    }
    .fade-leave-active {
        transition: opacity .5s;
    }
    .fade-enter, .fade-leave-to {
        opacity: 0;
    }
</style>