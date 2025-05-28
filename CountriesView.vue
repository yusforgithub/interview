<!--Задание:-->
<!--В проекте используется i18n для мультиязычности.-->
<!--1. Необходимо по апи получить список стран.-->
<!-- - Если страны найдены, то нужно вывести заголовок "Выберите одну из найденных стран:", а сами страны выводить по 4 элемента на странице. Под странами должна быть пагинацией. Первые найденные 4 страны должны быть сгенерированы и выведены в html разметке страницы (при просмотре кода страницы, нажав ctrl-U - необходмио для SEO).-->
<!-- - Иначе, если стран не найдено, то выводится текст из i18.n по ключу noCountries.-->
<!--2. При открытии страницы каждые 3 секунды должна происходить проверка наличия обновления.-->
<!--3. Также должна быть возможность перейти на страницу заказа по адресу page2.-->
<!--4. При нажатии на кнопку toggleMap должна либо отображаться либо скрываться карта.-->

<template lang="pug">
    h1 {{ $t('countries') }}

    .subtitle(v-if="countries") Выберите одну из найденных стран:

    .country(
        v-if="countries"
        v-for="country in activePageCountries"
    ) {{ country.title }}

    span.page(
        v-if="countries"
        v-for="page in countriesByPage.length"
        @click="activePage = page"
    ) {{ page }}

    .no-country(v-if="!countries") {{ $t('noCountries') }}

    nuxt-link(to="orderPage") {{ $t('orderPage') }}

    button.toggle-map(@click="isVisibleMap = !isVisibleMap") {{ $t('toggleMap') }}

    store-map(v-if="isVisibleMap")

</template>

<script setup>
import StoreMap from '~/components/StoreMap';

let countries = ref([]);
const activePage = ref(1);
const isVisibleMap = ref(false);

const countriesByPage = computed(() => chunkArray(countries.value, 3));
const activePageCountries = computed(() => countriesByPage.value[activePage.value - 1]);

countries.value = await $fetch('https://marketplace-stage-api.ecom.fix-price.ru/buyer/v1/location/country');

onMounted(() => {
    initCheckingUpdate();
});

// Эмуляция i18n.$t
const $t = (text) => text;

function initCheckingUpdate() {
    const TWO_SECONDS = 3000;

    setInterval(() => {
        console.log('Проверка обновления');
    }, TWO_SECONDS);
}

function selectRegionByCountry(country) {
    const REGIONS = {
        RU: [
            'Ставропольский край',
            'Краснодарский край',
        ],
        BY: [
            'Брестская область'
        ],
    };

    return REGIONS[country];
}

function chunkArray(array, size) {
    const result = [];

    for (let i = 0; i < array.length; i += size) {
        result.push(array.slice(i, i + size));
    }

    return result;
}
</script>

<style scoped>
.country {
    margin-bottom: 10px;
}

.page {
    cursor: pointer;
    margin-right: 10px;
}

.toggle-map {
    display: block;
}
</style>
