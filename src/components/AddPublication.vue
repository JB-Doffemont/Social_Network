<template>
    <div class="addsContain">
        <div class="imgAddsContain">
            <img :src="require(`../assets/img/shopImg/${urlImg}`)" class="imageProduct" alt="image du produit vendu">
        </div>
        <h4 class="titleProduct">{{titleProduct}}</h4>
        <p class="priceProduct">Prix : <span class="priceDiscount">{{priceProduct}}€</span> - <span class="fullPrice">{{fullPriceProduct}}€</span> </p>
        <div class="salesProduct">
            <input type="number" class="quantityProduct">
            <button class="addBasket">Ajouter au panier</button>
        </div>
    </div>

</template>

<script>
export default {
    props: {
        urlImg: String,
        titleProduct: String,
        priceProduct: Number,
        fullPriceProduct: Number,
    },
}
</script>

<style scoped>
.addsContain{
    display: flex;
    flex-direction: column;
    width: 300px;
    height: 440px;
    border: 1px solid black;
    border-radius: 15px;
    box-shadow: 1px 1px 2px black;
    margin-right: 30px;
    margin-bottom: 70px;
}

.imageProduct{
    margin-top: 5px;
    border-radius: 20px;
    box-shadow: 1px 1px 2px #f1f0f1;
}

.titleProduct{
    font-size: 20px;
    margin: 20px 0;
}

.imgAddsContain {
    width: 100%;
    height: 280px;
    background-color: #403c39;
    border-radius: 15px 15px 0px 0px;
}
.priceDiscount{
    color: #e0a102;
    font-weight: 700;
}

.fullPrice{
    text-decoration: line-through;
    color: #960c0cc9;
}

.salesProduct{
    display: flex;
    justify-content: space-around;
}

.quantityProduct{
    width: 30px;
    height: 25px;
    outline: none;
}

.addBasket{
    height: 30px;
    background-color: #e0a102;
    color: #403c39;
    font-weight: 700;
    border: none;
    border-radius: 5px;
}

.addBasket:hover{
    color: #e0a102;
    background-color: #f1f0f1;
    transition: 0.4s;
    cursor: pointer;
}

.addBasket:active{
    cursor: wait;
}

@media screen and (max-width : 769px){
    .addsContain{
        margin-right: 15px;

    }
}



</style>