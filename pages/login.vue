<script setup lang="ts">
interface LoginForm {
    email: string,
    password: string
}

const supabase = useSupabaseClient()

const form = ref<LoginForm>({
    email: '',
    password: ''
})

const error = ref<string | null>(null)

const handleLogin = async () => {
    error.value = null
    const { error: sbErrorLogin } = await supabase.auth.signInWithPassword(form.value)
    error.value = sbErrorLogin?.message || null

    // #TODO - this should be moved to signup page and modified.
    if (sbErrorLogin?.code === 'invalid_credentials') {
        const { error: sbErrorSignup } = await supabase.auth.signUp(form.value)

        if (sbErrorSignup) {
            error.value = sbErrorSignup.message
            return
        }

        await supabase.auth.signInWithPassword(form.value)
    }
}
</script>

<template>
    <div>
        <h1>Login user</h1>

        <form @submit.prevent="handleLogin">
            <input v-model="form.email" type="text" placeholder="Write email..." required />
            <input v-model="form.password" type="password" placeholder="****" required />
            <button type="submit">Login</button>
        </form>

        <div v-if="error" style="color: red;">
            {{ error }}
        </div>
    </div>
</template>