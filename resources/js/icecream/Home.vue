<template lang="">
    <div class="react" id="layout">
        <Header />
        <div class="mainBody react" id="body">
            <div class="container react" id="iceCreamBuilder">
                <Icecream :variants="cart" />
                <Builder
                    v-on:itemIdPass="addItem($event)"
                    v-on:removeId="removeItem($event)"
                    :variants="variants"
                    :total="totalPrice"
                    :cart="cart"
                    :modal="showModal"
                    v-on:modalToggle="modalShow"
                    v-on:emptycart="cartRemoves"
                    v-on:parent="addOrder($event)"
                />
            </div>
        </div>
        <Footer />
    </div>
</template>
<script>
import Header from "./Header.vue";
import Builder from "./Builder.vue";
import Icecream from "./Icecream.vue";
import Footer from "./Footer.vue";
import OrderTable from "./Order.vue";
import axios from "axios";
export default {
    components: {
        Header,
        Builder,
        Icecream,
        Footer,
        OrderTable,
    },
    data() {
        return {
            variants: [],
            cart: JSON.parse(localStorage.getItem("items")) ?? [],
            totalPrice: 0,
            showModal: false,
        };
    },
    methods: {
        addItem(id) {
            const flavour = this.variants.find((variant) => variant.id === id);
            this.cart.push(flavour);
            this.total();
            this.emitter.emit("notification", {
                type: "success",
                message: "You Select to " + flavour.name + " Flavour",
            });
            localStorage.setItem("items", JSON.stringify(this.cart));
        },
        total() {
            this.totalPrice = this.cart.reduce(
                (t, variant) => t + variant.price,
                0
            );
        },
        removeItem(id) {
            let position = this.cart.find((variant) => variant.id === id);
            if (position) {
                this.cart.splice(position, 1);
                localStorage.setItem("items", JSON.stringify(this.cart));
                this.total();
                this.emitter.emit("notification", {
                    type: "warning",
                    message: "Item Remove",
                });
            }
        },
        modalShow() {
            this.showModal = !this.showModal;
        },
        cartRemoves() {
            this.cart = [];
            localStorage.removeItem("items");
            this.totalPrice = 0;
        },
        getAllData() {
            axios
                .get("api/all/items")
                .then((res) => {
                    this.variants = res.data
                })
                .catch((error) => {
                    console.log(error);
                });
        },
    },
    mounted() {
        this.getAllData();
    },
};
</script>
<style lang=""></style>
