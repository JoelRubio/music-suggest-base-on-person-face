<template>
	<div class="app">

		<v-app>

			<v-toolbar
				color="#78909C"
				dark>															

				<v-toolbar-title>Recomendaciones de música a partir del rostro de una persona</v-toolbar-title>

			</v-toolbar>
			<v-main>
				
				<v-container fluid class="grey lighten-5">
					<v-row :justify="center">
						
						<v-col md="6">
							<v-card
								class="pa-2"
								tile>							

								<v-toolbar
									color="purple"
									dark>															

									<v-toolbar-title>Selecciona una imagen</v-toolbar-title>

								</v-toolbar>

								<form>

									<v-card flat>
										<v-container fluid>
											<v-file-input
												:rules="rules"
												counter
												show-size
												accept="image/png, image/jpeg, image/jpg, image/bmp"
												placeholder="Selecciona una imagen"
												prepend-icon="mdi-camera"
												label="Rostro">
											</v-file-input>
										</v-container>
									</v-card>

									<v-card flat>
										<v-card-text>

											<h3>Género de música</h3>

											<v-divider></v-divider>

											<v-container fluid>
												
												<v-layout row wrap>
													<v-flex v-for="gender in genders" :key="gender" xs4>
														<v-checkbox light :label=gender v-model="checkbox">
														</v-checkbox>
													</v-flex>
												</v-layout>						
											
											</v-container>
										</v-card-text>
									</v-card>
								
						
									<v-card flat>
										<v-container fluid>

											<v-btn
												:loading="loading3"
												rounded
												elevation="4"
												color="#3E68D6"
												class="ma-2 white--text"
												type="submit">							
												Realizar búsqueda											
											</v-btn>

											<v-btn
												rounded												
												elevation="4"
												class="ma-2"					
												>
												Limpiar búsqueda
											</v-btn>

										</v-container>
									</v-card>
								
								</form>

							</v-card>
						</v-col>

						<v-col md="6">
							<v-card
								class="pa-2"								
								tile>

								<v-toolbar
									color="#6ED860"
									dark>															

									<v-toolbar-title>Lista de recomendaciones</v-toolbar-title>

								</v-toolbar>

								<v-list subheader>
									
									<v-subheader left>Artistas</v-subheader>				

									<v-list-item
										v-for="person in people"
										:key="person.id">
											
										<v-list-item-avatar>
											<v-img
												:alt="`${person.name} avatar`"
												:src="person.img">
											</v-img>									
										</v-list-item-avatar>

										<v-list-item-content>
											<v-list-item-title v-text="person.name"></v-list-item-title>
										</v-list-item-content>

										<!--
										<v-list-item-content>
											<v-list-item-title v-text="person.song"></v-list-item-title>
										</v-list-item-content>-->

										<v-list-item-content>
												<iframe src="https://open.spotify.com/embed/album/7FTxFwUINuVrG6ntIaR6Qq?highlight=spotify:track:6DonFmvUg9f2aPWIx39JIL" 
													width="300" height="200" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe>
										</v-list-item-content>

									
										<!--
										<v-list-item-icon>
											<v-btn class="mx-2" fab small color="#81C784" :href="person.link" target="_blank">
												<v-icon>
													mdi-music-circle
												</v-icon>
											</v-btn>											
										</v-list-item-icon>-->

										
										<!--
										<v-list-item-icon>
											<v-icon :color="person.active ? 'deep-purple accent-4' : 'grey'">
												mdi-message-outline
											</v-icon>
										</v-list-item-icon>-->
									</v-list-item>
								</v-list>								

							</v-card>
						</v-col>
					</v-row>
				</v-container>
			</v-main>

			<v-footer padless>
				<v-col
					class="text-center"
					cols="12">

					{{ new Date().getFullYear() }} — <strong>Proyecto de inteligencia artificial</strong>
				</v-col>
			</v-footer>
			

		</v-app>

	</div>
</template>

<script>

export default {

	name: 'app',
	data() {

		return {

			rules: [

				value => !value || value.size < 5000000 || "La imagen no puede pesar más de 5MB"
			],
			genders: [

				'pop',
				'indie-pop',
				'electronic',
				'rock'
			],
			people: [
				{
					id: 1,
					name: 'CFCF',
					song: 'Test',
					link: 'https://open.spotify.com/album/7FTxFwUINuVrG6ntIaR6Qq?highlight=spotify:track:6DonFmvUg9f2aPWIx39JIL',
					img: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
					active: true
				},
				{
					id: 1,
					name: 'CFCF',
					song: 'Test',
					link: 'https://open.spotify.com/album/7FTxFwUINuVrG6ntIaR6Qq?highlight=spotify:track:6DonFmvUg9f2aPWIx39JIL',
					img: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
					active: true
				},
				{
					id: 1,
					name: 'CFCF',
					song: 'Test',
					link: 'https://open.spotify.com/album/7FTxFwUINuVrG6ntIaR6Qq?highlight=spotify:track:6DonFmvUg9f2aPWIx39JIL',
					img: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
					active: true
				}
			]
		}
	}
}

</script>


<style scoped>
</style>