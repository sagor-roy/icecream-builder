<template lang="">
    <div class="react">
        <Header />
        <div class="mainBody react" id="body">
            <div class="container">
                <form class="form" @submit.prevent="formSubmit()">
                    <label for="email"><b>Email</b></label>
                    <input
                        type="text"
                        placeholder="Enter Email"
                        v-model="email"
                        id="email"
                    />

                    <label for="psw"><b>Password</b></label>
                    <input
                        type="password"
                        placeholder="Enter Password"
                        v-model="password"
                        id="psw"
                    />

                    <button
                        :disabled="isLoading"
                        type="submit"
                        class="registerbtn"
                    >
                        <span v-if="!isLoading">Sign In</span>
                        <span v-else
                            ><i class="fas fa-spinner fa-spin"></i
                            >Loading....</span
                        >
                    </button>

                    <ul class="errors error-bg" v-if="errors.length !== 0">
                        <li v-for="(item, index) in errors">
                            {{ errors[index][0] }}
                        </li>
                    </ul>
                    <ul class="errors error-bg" v-if="resError">
                        <li>
                            {{ resError }}
                        </li>
                    </ul>

                    <p>
                        Already have an account?
                        <router-link :to="{ name: 'Register' }"
                            >Sign Up</router-link
                        >.
                    </p>
                </form>
            </div>
        </div>
        <Footer />
    </div>
</template>
<script>
import Header from "../Header.vue";
import Footer from "../Footer.vue";
export default {
    components: {
        Header,
        Footer,
    },
    data() {
        return {
            email: "",
            password: "",
            errors: [],
            resError: "",
            isLoading: false,
        };
    },
    methods: {
        formSubmit() {
            this.isLoading = true;
            const data = {
                email: this.email,
                password: this.password,
            };
            this.$store
                .dispatch("login", data)
                .then((res) => {
                    this.isLoading = false;
                    if (res.data.status) {
                        this.emitter.emit("notification", {
                            type: "success",
                            message: "Login Successfull",
                        });
                        const role = res.data.role;
                        if (role == 1) {
                            this.$router.push("/admin/dashboard");
                        } else if (role == 0) {
                            this.$router.push("/profile");
                        }
                    } else {
                        this.emitter.emit("notification", {
                            type: "warning",
                            message: res.data.error,
                        });
                        this.errors = [];
                        this.resError = res.data.error;
                    }
                })
                .catch((error) => {
                    this.emitter.emit("notification", {
                        type: "error",
                        message: "Something went wrong!!",
                    });
                    this.isLoading = false;
                    this.errors = error.response.data.errors;
                    this.resError = "";
                });
        },
    },
    computed: {},
};
</script>
<style scoped>
/* Full-width input fields */
input[type="text"],
input[type="password"] {
    width: 100%;
    padding: 15px;
    margin: 5px 0 22px 0;
    display: inline-block;
    border: none;
    background: #f1f1f1;
}

input[type="text"]:focus,
input[type="password"]:focus {
    background-color: #ddd;
    outline: none;
}

/* Set a style for the submit button */
.registerbtn {
    background-color: #04aa6d;
    color: white;
    padding: 16px 20px;
    margin: 8px 0;
    border: none;
    cursor: pointer;
    width: 100%;
    opacity: 0.9;
}

.registerbtn:hover {
    opacity: 1;
}

/* Add a blue text color to links */
a {
    color: dodgerblue;
}

/* Set a grey background color and center the text of the "sign in" section */
.signin {
    background-color: #f1f1f1;
    text-align: center;
}

form.form {
    width: 450px;
    margin: 50px auto;
    background-color: white;
    border-radius: 20px;
    padding: 30px;
}

p {
    margin-bottom: 0 !important;
}
</style>
