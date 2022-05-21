<template>
    <ClientOnly>
        <div class="cards_container">
            <div class="card" v-for="(item, index) in newRes.results" :key="item.id">
                <div class="card__header">
                    <img class="card__header--image" :src="item.user.profile_image.medium" alt="Человек">
                    <div class="card__author">
                        <a
                            class="author_name"
                            target="_blank"
                            :href="item.user.links.html"
                        >
                            {{item.user.name}}
                        </a>
                        <a
                            class="author_link"
                            target="_blank"
                            :href="item.user.links.html"
                        >
                            {{'@'+item.user.username}}
                        </a>
                    </div>
                </div>

                <div class="card__content">
                    <img :src="item.urls.small" alt="Content">
                </div>

                <div class="card__footer">
                    <div class="card__footer__buy">
                        <button
                            @click="(event) => cartInteraction(event, index, addToCart = true)"
                            class="card__footer__button plus"
                            :price="makePrice(index).price"
                        >
                        </button>

                        <button
                            @click="(event) => cartInteraction(event, index)"
                            class="card__footer__button minus"
                            :price="makePrice(index).price"
                        >
                        </button>
                        <p class="card__footer__buy--amount">{{priceArray[index].amount}}</p>
                    </div>
                    <div class="card__footer__price">{{makePrice(index).price}} руб.</div>
                </div>
            </div>
        </div>
        <Pagination
            @changePageInParent="currentPage = $event"
            :currentPage="currentPage"
            :pages="newRes.total_pages"
            :sum="sum"
        />
    </ClientOnly>
</template>

<script lang="ts" setup>
    const priceArray = ref<Array<PriceArrInterface>>([]);
    const total = reactive<Cart>({ sum: 0 });
    let addToCart = false;
    let currentPage = ref<number>(1);

    const { data: results, pending, error } = await useFetch<{results: any[], total_pages: number}>(() => (
        `https://api.unsplash.com/search/photos?query=london&per_page=10&page=${currentPage.value}&client_id=xLiIUyDxPANnAnsUR7zue-FeCu6bJVuySk3tpTq7i_Q`
        ));
    let newRes = results;

    // Генератор случаной цены в пределах от 1.000 до 60.000
    const randomPriceGenerator = () : number => {
        let number = Math.floor(Math.random() * 60000)
        return number < 1000 ? number = 1000 : number;
    }

    // Вовзращает случайную цену из массива цен
    function makePrice(index) : PriceArrInterface {
        if (priceArray.value[index]) return priceArray.value[index];

        let newPrice = randomPriceGenerator();
        priceArray.value.push({ price: newPrice, id: index + 1, amount: 0 });
        return priceArray.value[index];
    }

    // Добавление товара (цены) в корзину
    function cartInteraction(event, index, addToCart = false) : void {
        const price = event.target.getAttribute('price');

        if (addToCart) {
            priceArray.value[index].amount += 1
            total.sum += Number(price);
            return;
        }

        if (priceArray.value[index].amount > 0) {
            priceArray.value[index].amount -= 1;
            total.sum -= Number(price);
        }
        if (priceArray.value[index].amount < 0) priceArray.value[index].amount = 0;
        if (total.sum < 0) total.sum = 0;
    }

    const { sum } = toRefs(total)

</script>

<style scoped>
    .cards_container {
        padding-bottom: 80px;
    }
    @media (min-width: 480px) {
        .cards_container {
            padding: 10px 0 80px;
            max-width: 440px;
            margin: 0 auto;
        }
        .card {
            margin-bottom: 10px;
        }
    }
    @media (min-width: 767px) {
        .cards_container {
            display: grid;
            grid-template-columns: calc(50% - 10px) calc(50% - 10px);
            grid-column-gap: 20px;
            max-width: 660px;
            margin: 0 auto;
        }
        .card {
            height: 312px;
            margin: 0;
        }
    }

    .card__header {
        display: flex;
        padding: 10px;
    }
    @media (min-width: 480px) {
        .card__header {
            padding: 0 0 10px 0;
        }
    }
    @media (min-width: 767px) {
        .card__header {
            padding: 10px;
        }
    }
    .card__header--image {
        width: 30px;
        height: 30px;
        border-radius: 50%;
        object-fit: contain;
        margin-right: 10px;
    }
    .card__author {
        display: flex;
        flex-direction: column;
    }
    .author_name,
    .card__footer__price,
    .card__footer__buy--amount {
        font-weight: 700;
        font-size: 12px;
        line-height: 14px;
        font-family: 'Roboto Condensed';
        color: #333333;
        text-decoration: none;
    }
    .author_link {
        font-family: 'Roboto Condensed';
        font-style: normal;
        font-weight: 400;
        font-size: 12px;
        line-height: 14px;
        color: #8D8D8D;
        text-decoration: none;
    }

    .card__content {
        display: flex;
        max-height: 230px;
    }
    @media (min-width: 480px) {
        .card__content {
            max-height: 300px;
            width: 100% !important;
        }
        .card__content img {
            min-width: 100%;
        }
    }
    @media (min-width: 767px) {
        .card__content {
            max-height: 230px;
            height: 100%;
        }
    }
    .card__content img {
        min-width: 100%;
        max-height: 100%;
        object-fit: cover;
    }

    .card__footer {
        display: flex;
        justify-content: space-between;
        padding: 10px;
    }
    @media (min-width: 480px) {
        .card__footer {
            padding: 8px 5px;
        }
    }
    .card__footer__buy {
        display: flex;
        align-items: center;
    }
    .card__footer__button {
        border: none;
        cursor: pointer;
    }
    .plus {
        background: url('@/static/plus.svg');
        height: 17px;
        width: 17px;
        margin-right: 5px;
    }
    .minus {
        background: url('@/static/minus.svg');
        height: 9px;
        width: 9px;
        margin-right: 10px;
    }

</style>