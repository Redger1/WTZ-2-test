<template>
    <div class="pagination">
        <div class="container">

        <ul id="currentPage>1" v-if="currentPage - 1 > 1 && currentPage != 1000" class="pagination__pages">
            
            <li class="first-page">
                <a :pageTo="1" @click="(event) => changePage(event)" href="#">1</a>
            </li>

            <li>...</li>

            <li>
                <a :pageTo="currentPage - 1" @click="(event) => changePage(event)" href="#">{{currentPage - 1}}</a>
            </li>
            <li>
                <a :pageTo="pageTo" @click="(event) => changePage(event)" href="#">{{currentPage}}</a>
            </li>
            <li>
                <a :pageTo="currentPage + 1" @click="(event) => changePage(event)" href="#">{{currentPage + 1}}</a>
            </li>

            <li>...</li>

            <li class="last-page">
                <a :pageTo="pages" @click="(event) => changePage(event)" href="#">{{pages}}</a>
            </li>
        </ul>

        <ul id="currentPage==1" v-else-if="currentPage - 1 == 1" class="pagination__pages">
            
            <li class="first-page">
                <a :pageTo="currentPage - 1" @click="(event) => changePage(event)" href="#">{{currentPage - 1}}</a>
            </li>

            <li>
                <a :pageTo="currentPage" @click="(event) => changePage(event)" href="#">{{currentPage}}</a>
            </li>
            <li>
                <a :pageTo="currentPage + 1" @click="(event) => changePage(event)" href="#">{{currentPage + 1}}</a>
            </li>
            <li>
                <a :pageTo="currentPage + 2" @click="(event) => changePage(event)" href="#">{{currentPage + 2}}</a>
            </li>

            <li>...</li>

            <li class="last-page">
                <a href="#">{{pages}}</a>
            </li>
        </ul>

        <ul id="last1000" v-else-if="currentPage == 1000" class="pagination__pages">
            <li class="first-page">
                <a :pageTo="1" @click="(event) => changePage(event)" href="#">1</a>
            </li>

            <li>...</li>

            <li>
                <a :pageTo="currentPage - 2" @click="(event) => changePage(event)" href="#">{{currentPage - 2}}</a>
            </li>
            <li>
                <a :pageTo="currentPage - 1" @click="(event) => changePage(event)" href="#">{{currentPage - 1}}</a>
            </li>
            <li>
                <a :pageTo="currentPage" @click="(event) => changePage(event)" href="#">{{currentPage}}</a>
            </li>
        </ul>

        <ul id="else" v-else class="pagination__pages">
            <li class="first-page">
                <a :pageTo="currentPage" @click="(event) => changePage(event)" href="#">{{currentPage}}</a>
            </li>

            <li>
                <a :pageTo="pageTo + 1" @click="(event) => changePage(event)" href="#">{{currentPage + 1}}</a>
            </li>
            <li>
                <a :pageTo="pageTo + 2" @click="(event) => changePage(event)" href="#">{{currentPage + 2}}</a>
            </li>

            <li>...</li>

            <li class="last-page">
                <a :pageTo="pages" @click="(event) => changePage(event)" href="#">{{pages}}</a>
            </li>
        </ul>

        <div class="pagination__cart">
            <div class="pagination__cart--price">{{sum}} руб.</div>
            <img class="pagination__cart--img" src="@/static/cart.svg" alt="Корзина">
        </div>

        </div>
    </div>
</template>

<script lang="ts">
    export default {
        props: {
            sum: { required: true },
            pages: { required: false },
            currentPage: {
                required: true,
                default: 1
            }
        },
        setup(props, {emit}) {
            let pageTo = ref(props.currentPage);

            const changePage = (event) : void => {
                const newPage = Number(event.target.getAttribute('pageTo'))

                pageTo.value = newPage
                emit('changePageInParent', newPage)
            }
            return { pageTo, changePage }
        }  
    }
</script>

<style scoped>
    .pagination {
        width: 100vw;
        padding: 20px;
        background-color: rgba(0, 0, 0, .8);
        color: #FFF;
        bottom: 0;
        position: fixed;
    }
    .container {
        display: grid;
        justify-content: space-between;
        grid-template-columns: 70% 30%;
    }   
    @media (min-width: 480px) {
        .pagination {
            padding: 18px 19px
        }
        .container {
            max-width: 440px;
            margin: 0 auto;
        }
    }
    @media (min-width: 767px) {
        .container {
            max-width: 660px;
            margin: 0 auto;
        }
    }
    .pagination__pages {
        display: flex;
        justify-content: space-evenly;
        align-items: center;
        list-style: none;
        font-size: 18px;
        font-weight: 400;
    }
    .pagination__pages .active {
        font-weight: 700;
        font-size: 25px;
    }
    .pagination__pages * {
        color: white;
        text-decoration: none;
    }
    .pagination__cart {
        display: flex;
        justify-self: flex-end;
        align-items: center;
    }
    .pagination__cart--img {
        margin-left: 6px;
    }
</style>