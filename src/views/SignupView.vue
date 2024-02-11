<template>
    <div class="max-w-7xl mx-auto grid grid-cols-2 gap-4">
        <div class="main-left">
            <div class="p-12 bg-white border border-gray-200 rounded-lg">
                <h1 class="mb-6 text-2xl">Sign up</h1>

                <p class="mb-6 text-gray-500">
                    Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate.
                    Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate.
                </p>

                <p class="font-bold">
                    Already have an account? <RouterLink :to="{'name': 'login'}" class="underline">Click here</RouterLink> to log in!
                </p>
            </div>
        </div>

        <div class="main-right">
            <div class="p-12 bg-white border border-gray-200 rounded-lg">
                <form class="space-y-6" v-on:submit.prevent="submitForm">
                    <div>
                        <label>First Name</label><br>
                        <input type="text" v-model="form.first_name" placeholder="Your fast name" class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
                    </div>
                    <div>
                        <label>Last Name</label><br>
                        <input type="text" v-model="form.last_name" placeholder="Your full name" class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
                    </div>

                    <div>
                        <label>E-mail</label><br>
                        <input type="email" v-model="form.email" placeholder="Your e-mail address" class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
                    </div>

                    <div>
                        <label>Password</label><br>
                        <input type="password" v-model="form.password" placeholder="Your password" class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
                    </div>

                    <div>
                        <label>Repeat password</label><br>
                        <input type="password" v-model="form.confirmPassword" placeholder="Repeat your password" class="w-full mt-2 py-4 px-6 border border-gray-200 rounded-lg">
                    </div>

                    <template v-if="errors.length > 0">
                        <div class="bg-red-300 text-white rounded-lg p-6">
                            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
                        </div>
                    </template>

                    <div>
                        <button class="py-4 px-6 bg-purple-600 text-white rounded-lg">Sign up</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { useToastStore } from '@/stores/toast'

export default {
    setup() {
        const toastStore = useToastStore()
        return { toastStore };
    },
    data() {
        return {
            form: {
                first_name: '',
                last_name: '',
                email: '',
                password: '',
                confirmPassword: ''
            },
            errors: [],
        };
    },
    methods: {
        async submitForm() {
            this.errors = [];

            if (this.form.first_name === '') {
                this.errors.push('Your first name is missing');
            }

            if (this.form.last_name === '') {
                this.errors.push('Your last name is missing');
            }

            if (this.form.email === '') {
                this.errors.push('Your e-mail is missing');
            }

            if (this.form.password === '') {
                this.errors.push('Your password is missing');
            }

            if (this.form.password !== this.form.confirmPassword) {
                this.errors.push('The password does not match');
            }

            if (this.errors.length === 0) {
                try {
                    const response = await axios.post('/users', {
                        first_name: this.form.first_name,
                        last_name: this.form.last_name,
                        email: this.form.email,
                        password: this.form.password,
                    });

                    console.log('Response data:', response.data); // Log the response data
                    console.log('Response Message:', response.status);


                    if (response.status === 201) {
                            this.toastStore.showToast(5000, 'The user is registered. Please activate your account by clicking your email link.', 'bg-emerald-500')

                            this.form.first_name = '';
                            this.form.last_name = '';
                            this.form.email = '';
                            this.form.password = '';
                            this.form.confirmPassword = '';

                             // Redirect to the login page (update with your actual route name)
                             this.$router.push({ name: 'login' });

        
                    }
                }  catch (error) {
                        console.error('Error:', error);
                        if (error.response) {
                            // The request was made and the server responded with a status code
                            this.toastStore.showToast(5000,  error.response.data, 'bg-red-300');
                            console.error('Server responded with:', error.response.status, error.response.data);

                    
                        } else if (error.request) {
                            // The request was made but no response was received
                            this.toastStore.showToast(5000,  error.request, 'bg-red-300');
                            console.error('No response received:', error.request);

                        } else {
                            // Something happened in setting up the request that triggered an Error
                            console.error('Error setting up the request:', error.message);
                            this.toastStore.showToast(5000,  error.message, 'bg-red-300');
                        }
                     }

            }
        }
    }
}
</script>
