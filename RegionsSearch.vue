<template lang="pug">
    .regions-search
        h1.header Регионы

        input(
            v-model="region"
            @input="search"
        )

        .regions(
            v-if="regions"
            v-for="(region, index) in regions"
        )
            .region(
                v-if="index % 2 === 0"
                style="font-size: 18px; font-weight: bold;"
            ) {{ region.name }}
            .region(v-else) {{ region.name }}

        h2 Разделы
        .section(v-for="section in $t('sections')") {{ section }}
        .time {{ timeLimit }}

</template>

<script>
export default {
    name: 'RegionsSearch',

    data() {
        return {
            timer: null,
            region: '',
            regions: [],
            timeLimit: 60,
        };
    },

    methods: {
        countDown() {
            this.timeLimit--;

            if (!this.timeLimit) {
                clearInterval(this.timer);
                this.$router.push('/');
            }
        },

        /**
         * @returns {Promise<void>}
         */
        async search() {
            this.regions = await this.$api.FIAS.getAddress(this.region);
        },
    },

    mounted() {
        this.timer = setInterval(this.countDown, 1000);
    },
}
</script>

<style lang="sass">
.header
    font-size: 1.4rem

</style>
