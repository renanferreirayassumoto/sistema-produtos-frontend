<template>
	<div class="d-flex align-center justify-center h-100 w-100">
		<v-container
			><v-row class="d-flex align-center justify-center">
				<v-col cols="6"><img src="../img/login.png" width="100%" /></v-col>
				<v-col cols="6">
					<v-container class="d-flex align-center justify-center flex-column">
						<h1>LOGIN</h1>
						<v-text-field
							label="Email"
							variant="solo"
							class="w-50"
							v-model="email"
							:rules="[rules.required]"
						></v-text-field>
						<v-text-field
							:type="marker ? 'password' : 'text'"
							label="Password"
							:append-inner-icon="marker ? 'mdi-eye-off' : 'mdi-eye'"
							@click:append-inner="revealPassword"
							variant="solo"
							class="w-50"
							v-model="password"
							@keydown.enter="login()"
							:rules="[rules.required]"
						></v-text-field>
						<v-row class="w-50">
							<v-col><v-checkbox label="Remember me"></v-checkbox></v-col>
							<v-col><v-btn variant="text">Esqueceu a senha</v-btn></v-col>
						</v-row>
						<v-btn
							class="w-50 py-6 d-flex align-center justify-center"
							color="blue"
							@click="login()"
							>Entrar</v-btn
						>
						<h4 class="my-4">
							Não Tem Uma Conta?
							<router-link to="/cadastro">Inscrever-se</router-link>
						</h4>
					</v-container></v-col
				>
			</v-row>
		</v-container>

		<AlertComponentVue
			id="alertComponent"
			v-model="showAlertErrorLogin"
			variant="outlined"
			type="error"
			title="Email ou senha incorretos!"
		/>
	</div>
</template>

<script>
import axios from 'axios';
import AlertComponentVue from './AlertComponent.vue';

export default {
	name: 'LoginComponent',
	components: {
		AlertComponentVue,
	},
	data() {
		return {
			email: '',
			password: '',
			marker: true,
			rules: {
				required: (value) => !!value || 'O campo é obrigatório',
			},
			showAlertErrorLogin: false,
		};
	},
	methods: {
		async login() {
			try {
				const response = await axios.post(
					'https://sistema-produtos-backend.onrender.com/login',
					{
						email: this.email,
						password: this.password,
					}
				);

				const accessToken = response.data.access_token;
				localStorage.setItem('access_token', accessToken);
				this.$router.push('/consulta');
			} catch (err) {
				if (err.response && err.response.status === 401) {
					this.showAlertErrorLogin = true;
					this.email = '';
					this.password = '';
					setTimeout(() => {
						this.showAlertErrorLogin = false;
					}, 2500);
				}
				console.error(err);
			}
		},
		revealPassword() {
			this.marker = !this.marker;
		},
	},
};
</script>

<style></style>
