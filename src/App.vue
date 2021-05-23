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

								<form @submit.prevent="submit">

									<v-card flat>
										<v-container fluid>
											<v-file-input
												@change="onFileChange"
												v-model="file"
												:rules="rules"
												counter
												show-size
												accept="image/png, image/jpeg, image/jpg, image/bmp"
												placeholder="Selecciona una imagen"
												prepend-icon="mdi-camera"
												label="Rostro">
											</v-file-input>
										</v-container>

										<img :src="image" max-width="250" max-height="120">
									</v-card>

									<v-card flat>
										<v-card-text>

											<h3>Género de música</h3>

											<v-divider></v-divider>

											<v-container fluid>
												
												<v-layout row wrap>
													<v-flex v-for="gender in genders" :key="gender" xs4>
														<v-checkbox light :label=gender :value=gender v-model="checkboxes">
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
												type="submit"
												:disabled="!isValid"
												>							
												Realizar búsqueda											
											</v-btn>

											<v-btn
												rounded												
												elevation="4"
												class="ma-2"
												@click="clear()"				
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
													width="500" height="200" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe>
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

//import axios from 'axios';

export default {

	name: 'app',
	data() {

		return {

			file: null,
			checkboxes: [],
			isValid: false,
			image: '',
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
					id: 2,
					name: 'CFCF',
					song: 'Test',
					link: 'https://open.spotify.com/album/7FTxFwUINuVrG6ntIaR6Qq?highlight=spotify:track:6DonFmvUg9f2aPWIx39JIL',
					img: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
					active: true
				}
			]
		}
	},
	methods: {

		onFileChange(event) {

			let files = event.target.files || event.dataTransfer.result;

			if (!files.length) 
				return;

			this.createImage(files[0]);
		},
		createImage(file) {

			let reader = new FileReader();

			reader.onload = (event) => {

				this.image = event.target.result;
			};

			reader.readAsDataURL(file);
		},
		clear() {

			this.checkboxes = [];

			this.file = null;
		},
		submit() {

			//let data = {imgFile: this.img, genders: this.gender};
		
			//validation for data form.

			//doAPICalls(data);			
		}/*,
		async doAPICalls(data) {

			try {

				let responseAzure = await requestAPIAzure(data.imgFile);

			} catch (error) {

				console.log(error);
			}

			let dataRecommendation = getDataRecommendation(responseAzure, data.genders);

			try {

				let responseSpotify = await requestAPISpotify(dataRecommendation);

			} catch (error) {

				console.log(error);
			}
			
		},
		getDataRecommendation(responseAzure, genders) {

			let emotionsAvailable = responseAzure.emotion.map(emotion => emotion > 0.0);

			/*
			"emotion": {
				"anger": 0.0,
				"contempt": 0.0,
				"disgust": 0.0,
				"fear": 0.0,
				"happiness": 1.0,
				"neutral": 0.0,
				"sadness": 0.0,
				"surprise": 0.0
			}*/

			//algorithm to determine the tempo base on emotions.
			/*
			return dataRecommendation = {

				limit: 1,
				seed_genres: genders, 
				minTempo: 0.50,
				maxTempo: 1.0
			};
		},*/
		/*requestAPIAzure(imgFile) {

			const SUB_KEY = '5a1b891888e1413ca3340a187c2f6f91';

			return axios({

				method: 'post',
				url: 'https://development-faceapi.cognitiveservices.azure.com/face/v1.0/detect?overload=stream&returnFaceId=true&returnFaceAttributes=emotion&recognitionModel=recognition_01&detectionModel=detection_01',
				data: data.imgFile,
				headers: {

					'Content-Type': 'application/octet-stream',
					'Ocp-Apim-Subscription-Key': SUB_KEY
				}
			});
			/*.then(response => console.log(response))
			.catch(error => console.log(error));*/
		/*},
		requestAPISpotify(dataRecommendationParams) {

			const AUTH_STR = 'Bearer '.concat('USER-TOKEN');

			let config = {

				headers: {

					'Content-Type': 'application/json',
					'Accept': 'application/json',
					'Authorization': AUTH_STR
				},
				params: dataRecommendationParams
			};

			return axios.get('https://api.spotify.com/v1/recommendations', config);
				/*.then(response => console.log(response))
				.catch(error => console.log(error));*/
		//}
	}
}

/*
[
	{
		"faceId": "4f688646-1515-4194-9b04-074f3b3eb3d0",
		"faceRectangle": {
			"top": 73,
			"left": 343,
			"width": 114,
			"height": 114
		},
		"faceAttributes": {
			"emotion": {
				"anger": 0.0,
				"contempt": 0.0,
				"disgust": 0.0,
				"fear": 0.0,
				"happiness": 1.0,
				"neutral": 0.0,
				"sadness": 0.0,
				"surprise": 0.0
			}
		}
	}
]
*/

</script>


<style scoped>
</style>