<template>
    <div class="col-md-8 m-auto mt-5">
        <div class="card card-container">
            <img src="//ssl.gstatic.com/accounts/ui/avatar_2x.png" alt="profile-img" class="profile-card-img">
            <Form @submit="handleLogin" :validation-schema="schema">
                <div class="mb-3">
                    <label for="username">Username</label>
                    <Field name="username" type="text" class="form-control"/>
                    <ErrorMessage name="username" class="text-danger"/>
                </div>

                <div class="mb-3">
                    <label for="password">Password</label>
                    <Field name="password" type="password" class="form-control"/>
                    <ErrorMessage name="password" class="text-danger"/>
                </div>

                <div class="mb-3">
                    <button class="btn btn-primary btn-block" :disabled="loading">
                        <span v-show="loading" class="spinner-border spinner-border-sm"></span>
                        <span>Login</span>
                    </button>
                </div>

                <div class="form-group">
                    <div v-if="message" class="alert alert-danger" role="alert">
                        {{ message }}
                    </div>
                </div>
            </Form>
        </div>
    </div>
</template>

<script>
import { Form, Field, ErrorMessage } from 'vee-validate'
import * as yup from 'yup'

export default {
    name: 'Login',
    components: {
        Form, Field, ErrorMessage
    },
    data() {
        const schema = yup.object().shape({
            username: yup.string().required('Username is required!').label('Username'),
            password: yup.string().required('Password is required!').label('Password'),
        });
        return {
            loading: false,
            message: '',
            schema,
        }
    },
    computed: {
        loggedIn() {
            return this.$store.state.auth.status.loggedIn;
        },
    },
    created() {
        if(this.loggedIn) {
            this.$router.push("/profile");
        }
    },
    methods: {
        handleLogin(user) {
            this.loading = true;

            this.$store.dispatch('auth/login', user).then(
                () => {
                    this.$router.push('/profile');
                },
                (error) => {
                    this.loading = false;
                    this.message = (error.response && 
                                    error.response.data && 
                                    error.response.data.message ) || 
                                    error.message || 
                                    error.toString();
                }
            );
        },
    },
}
</script>

<style>
</style>