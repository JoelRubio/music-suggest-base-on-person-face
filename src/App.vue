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
					<v-row>
						
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
												v-model="file"
												:rules="rules"
												@change="onFileChange"
												counter
												show-size
												accept="image/png, image/jpeg, image/jpg, image/bmp"
												placeholder="Selecciona una imagen"
												prepend-icon="mdi-camera"
												label="Rostro">
											</v-file-input>

											<v-img :src="imgUrl" 
												v-if="validation.fillImage"
												class="rounded-lg" 
												width="350" 
												height="320">												
											</v-img>

										</v-container>
										
									</v-card>

									<v-card flat>
										<v-card-text>

											<h3>Género de música</h3>

											<v-divider></v-divider>

											<v-container fluid>
												
												<v-layout row wrap>
													<v-flex v-for="gender in genders" :key="gender" xs4>
														<v-checkbox light 
															:label=gender 
															:value=gender 
															v-model="checkboxes"
															>
														</v-checkbox>
													</v-flex>
												</v-layout>						
											
											</v-container>
										</v-card-text>
									</v-card>
								
						
									<v-card flat>
										<v-container fluid>

											<v-btn
												
												rounded
												elevation="4"
												color="#3E68D6"
												class="ma-2 white--text"
												@click="submit()"
												:disabled="!validation.imageValid"
												>							
												Realizar búsqueda											
											</v-btn>

											<v-btn
												rounded												
												elevation="4"
												class="ma-2"
												@click="clear()">												
												Limpiar búsqueda
											</v-btn>

										</v-container>
									</v-card>
								
								</form>

							</v-card>
						</v-col>

						<v-col md="6">
							<v-card
								class="pa-2 scroll"								
								tile>

								<v-toolbar
									color="#6ED860"
									dark>															

									<v-toolbar-title>Lista de recomendaciones</v-toolbar-title>

								</v-toolbar>				

								<v-list subheader v-if="suggestedSongsReady">
									
									<v-container fluid>										

										<v-row>
											<v-col>
												<v-btn @click="clearSuggestedSongs()">Limpiar lista de sugerencias</v-btn>
											</v-col>
										</v-row>

										<v-row>
											<v-col>
												<v-subheader><h3>Artistas</h3></v-subheader>
											</v-col>
											<v-col>
												<v-subheader><h3>Canciones</h3></v-subheader>
											</v-col>
										</v-row>	

										<v-divider></v-divider>																			
									</v-container>

									<v-list-item
										v-for="suggestSong in suggestedSongs"
										:key="suggestSong.id">
											
										<!--
										<v-list-item-avatar>
											<v-img
												:alt="`${suggestedSongs.name} avatar`"
												:src="suggestedSongs.img">
											</v-img>									
										</v-list-item-avatar>-->

										<v-list-item-content>
											<v-list-item-title v-text="suggestSong.name"></v-list-item-title>
										</v-list-item-content>

										<!--
										<v-list-item-content>
											<v-list-item-title v-text="person.song"></v-list-item-title>
										</v-list-item-content>-->

										<v-list-item-content>
												<iframe :src="suggestSong.link"
													width="500" height="80" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe>
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

import axios from 'axios';

export default {

	name: 'app',
	data() {

		return {

			file: null,
			checkboxes: [],
			imgUrl: null,
			suggestedSongsReady: false,
			validation: {

				fillImage: false,
				imageValid: false,
				checkboxValid: false,
			},
			rules: [

				imageFile => this.validateContent(imageFile) || this.displayErrorSize()			
			],
			genders: [

				'pop',
				'indie-pop',
				'electronic',
				'rock',
				'jazz',
				'french',
				'dance',
				'chill',
				'progressive-house'
			],
			suggestedSongs: []
		}
	},
	methods: {

		validateContent(imageFile) {

			if (imageFile && imageFile.size < 10000000) {

				this.validation.fillImage = true;

				this.validation.imageValid = true;

				return imageFile;

			} else {

				this.validation.fillImage = false;

				this.validation.imageValid = false;				

				return !imageFile;
			}
		},
		displayErrorSize() {

			this.validation.fillImage = false;

			return "La imagen no puede pesar más de 10MB";
		},
		/*validateCheckboxContent(checkbox) {

			let valid;

			if (checkbox) {

				this.validation.checkboxValid = true;

				valid = true;

			} else {

				this.validation.checkboxValid = false;

				valid = false;
			}

			return valid;
		},*/
		onFileChange() {

			this.imgUrl = URL.createObjectURL(this.file);
		},
		clear() {

			this.checkboxes = [];

			this.file = null;
		},
		clearSuggestedSongs() {

			this.suggestedSongs = [];

			//this.suggestedSongsReady = false;
		},
		submit() {			

			let data = {imgFile: this.file, genders: this.checkboxes};

			this.doAPICalls(data);					
		},
		async doAPICalls(data) {

			//let emotions;

			/*try {

				let responseAzure = await this.requestAPIAzure(data.imgFile);

				console.log(responseAzure);

				emotions = responseAzure.data[0].faceAttributes.emotion;

				console.log(emotions);			

			} catch (error) {

				console.log(error);
			}*/

			
			//let dataRecommendation = this.getDataRecommendation(emotions, data.genders);

			//console.log(dataRecommendation);

			const genders = data.genders.toString();

			let dataRecommendation = {

				limit: 8,
				seed_genres: genders, 
				min_danceability: 0.0,
				max_danceability: 1.0,
				min_energy: 0.0,
				max_energy: 1.0,
				min_liveness: 0.0,
				max_liveness: 1.0,
				//max_popularity: 1.0,
				minTempo: 0.50,
				maxTempo: 1.0
			};

			
			try {

				let responseSpotify = await this.requestAPISpotify(dataRecommendation);

				//tracks is an array.
				//let artistName = responseSpotify.data.tracks[0].artists[0].name;
				//let albumName  = responseSpotify.data.tracks[0].album.name;
				//let uriSong    = responseSpotify.data.tracks[0].uri;
				//let songName   = responseSpotify.data.tracks[0].name;
				//let preUrl = responseSpotify.data.tracks[0].external_urls.spotify;
				
				//https://open.spotify.com/track/...

				//console.log(responseSpotify.data.tracks);

				let tracks = responseSpotify.data.tracks;

				for (let i = 0; i < tracks.length; i++) {					

					let urlSong = tracks[i].external_urls.spotify.slice(0, 25) + 'embed/' + tracks[i].external_urls.spotify.slice(25);

					this.suggestedSongs.push({

						id: i,
						name: tracks[i].name,
						link: urlSong
					});
				}		
				
				this.suggestedSongsReady = true;

				//console.log(this.suggestedSongs);

			} catch (error) {

				console.log(error);			
			}
			
		},
		getDataRecommendation(emotions, genders) {

			const arrayObject = Object.entries(emotions);

			// eslint-disable-next-line no-unused-vars
			const arrayEmotions = arrayObject.filter(([key, value]) => value > 0);			

			const emotionsAvailable = Object.fromEntries(arrayEmotions);

			console.log(emotionsAvailable);


			//algorithm to determine the tempo base on emotions.
			
			return {

				limit: 1,
				seed_genres: genders, 
				minTempo: 0.50,
				maxTempo: 1.0
			};
		},
		async requestAPIAzure(imgFile) {

			const SUB_KEY = '5a1b891888e1413ca3340a187c2f6f91';

			return axios({

				method: 'post',
				url: 'https://development-faceapi.cognitiveservices.azure.com/face/v1.0/detect?overload=stream&returnFaceId=true&returnFaceAttributes=emotion&recognitionModel=recognition_01&detectionModel=detection_01',
				data: imgFile,
				headers: {

					'Content-Type': 'application/octet-stream',
					'Ocp-Apim-Subscription-Key': SUB_KEY
				}
			});
		},
		requestAPISpotify(dataRecommendationParams) {

			const AUTH_STR = 'Bearer '.concat('BQBoC_Lmp3D2ckArp_ezpAVXzMB0AmYrssMaPpYbQEQ4xqv3FsXOAfoUDjDsgUUwYeLJEF5o8VZnhnso1v0');

			const config = {

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
		}
	}
}

</script>


<style scoped>

.scroll {

	overflow-y: auto;
	height: 822px;
}

</style>