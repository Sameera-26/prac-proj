<template>
    
        
        <img class="h-[150px] mx-[38%] py-6" src="../assets/user.png" />
    <div class="bg-gray-600 h-[150%] mx-8 rounded-lg shadow-lg">

        
        <form @submit.prevent="submitForm" class=" flex flex-col gap-6 ">
            <br />
            <h2 class="text-left mx-[25%] font-bold text-white text-xl">Create User!</h2>
            
            <input
            v-model="formData.username"
            placeholder=" Username"
            class=" mx-[25%] text-black outline-purple-800 w-[55%] h-9 rounded-md shadow-gray-300 shadow-sm border-2  bg-white"
            /> 
            <span v-for="error in v$.username.$errors" :key="error.$uid" class="text-red-500 -my-5  text-sm">
            {{ error.$message }}</span>
            
            <input
            v-model="formData.email"
            placeholder=" Email"
            type='email'
            class="mx-[25%] text-black outline-purple-800 w-[55%] h-9 rounded-md shadow-gray-300 shadow-sm border-2  bg-white"
            />
            <span v-for="error in v$.email.$errors" :key="error.$uid" class="text-red-500 -my-5  text-sm">
            {{ error.$message }}</span>
            <input
            v-model="formData.password"
            placeholder=" Password"
            type='password'
            class="mx-[25%] text-black outline-purple-800 w-[55%] h-9 rounded-md border-2 shadow-gray-300 shadow-smshadow-lg bg-white"
            />
            <span v-for="error in v$.password.$errors" :key="error.$uid" class="text-red-500 -my-5  text-sm">
            {{ error.$message }}</span>
            <input
            v-model="formData.confirmPassword"
            placeholder=" Confirm password"
            type='password'
            class="mx-[25%] text-black outline-purple-800 w-[55%] h-9 rounded-md  border-2 shadow-gray-300 shadow-sm bg-white"
            />
            <span v-for="error in v$.confirmPassword.$errors" :key="error.$uid" class="text-red-500 -my-5  text-sm">
            {{ error.$message }}</span>
            
            <button type="submit" class="mx-[25%] bg-green-300/60 text-white w-[55%] h-8 font-bold shadow-green-500 shadow-md">SUBMIT
            </button>
            <br />
        </form>
    </div>
 
</template>

<script >

import {reactive, computed } from 'vue'
import { useVuelidate } from '@vuelidate/core'
import { required, email, maxLength, sameAs, helpers } from '@vuelidate/validators'

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name:'Input',
    setup () {
        const formData = reactive({
            username:'',
            email:'',
            password:'',
            confirmPassword: ''
        });
        const containUser = (value) => {
            return value.includes('user')
        }
        const rules = computed(() => {
            return {
                username: { required, 
                    maxLength: maxLength(10), 
                    containUser: helpers.withMessage('the field must contain user', containUser)
            },
            email: { required, email },
            password: { required },
            confirmPassword: { required, sameAs: sameAs(formData.password)}
        
            }
        }) 
        const v$ = useVuelidate(rules, formData) 


        const submitForm = async () => {
            const result = await v$.value.$validate()
            if (result) {
                alert ('Success, form submitted')
            } else {
                alert ('Unsuccess, form not submitted')
            }
            
        }
        return {
            formData,
            submitForm,
            rules,
            v$,
            containUser
        }
    }
}
</script>

<style scoped>

</style>