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
								rounded
								class="pa-2">							
								
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
												accept="image/jpeg, image/png, image/gif, image/bmp"
												placeholder="Selecciona una imagen"
												prepend-icon="mdi-camera"
												label="Rostro">
											</v-file-input>

											<v-img :src="imgUrl" v-if="validation.fillImage"
												class="rounded-lg" 
												width="350" 
												height="320">												
											</v-img>

											<!--
											<v-card-text v-if="validation.fillImage">

												<h3>Test</h3>

											</v-card-text>
											-->

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
															v-model="checkboxes">															
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
												:disabled="blockSubmitButton"
												@click="submit()">
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
								class="pa-2"
								:class="largeSize ? 'scroll' : 'normal-height'"
								rounded>
					
								<v-card-title style="background: #6ED860; color: white">
									Lista de recomendaciones

									<v-spacer></v-spacer>

									<v-btn v-if="suggestedSongsReady"
										color="white"
										small
										fab
										@click="clearSuggestedSongs()">
										<v-icon title="Limpiar lista de sugerencias">mdi-delete</v-icon>										
									</v-btn>
								</v-card-title>
																						

								<v-container fluid>										

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
		
								<v-container fluid v-if="progressCircular" style="margin: 250px 300px;">
														
									<v-progress-circular
										:size="50"
										indeterminate
										color="#6ED860">									
									</v-progress-circular>
								</v-container>

								<v-list subheader v-if="suggestedSongsReady">																		

									<v-list-item
										v-for="suggestSong in suggestedSongs"
										:key="suggestSong.id">																					

										<v-list-item-content>
											<v-list-item-title v-text="suggestSong.name"></v-list-item-title>
										</v-list-item-content>							

										<v-list-item-content>
												<iframe :src="suggestSong.link"
													width="500" height="80" frameborder="0" allowtransparency="true" allow="encrypted-media">
												</iframe>
										</v-list-item-content>

										<!-- https://open.spotify.com/embed/track/6KtheYP777tm0guxcbMqdR no existe! -->
																	
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
			largeSize: false,
			progressCircular: false,
			blockSubmitButton: false,
			validation: {

				fillImage: false,			
			},
			rules: [

				//Valida el contenido del archivo de la imagen, si no es verdadero 
				//ejecuta la función "displayErrorSize()".
				imageFile => this.validateContent(imageFile) || this.displayErrorSize()			
			],
			genders: [
				
				//Géneros de música aceptados por el API de Spotify.
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
			suggestedSongs: [] //Arreglo para las canciones sugeridas por el API de Spotify.
		}
	},
	methods: {

		/**
		 * Valida si la variable contiene un archivo
		 * y si el archivo pesa menos de 6 MB, para 
		 * establecer las variables de validación en verdadero,
		 * de lo contrario se colocan en falso.
		 * 
		 * 
		 * @param1 archivo de la imagen del rostro del usuario.
		 */
		validateContent(imageFile) {

			if (imageFile && imageFile.size < 6000000) {

				this.validation.fillImage = true;;
				
				this.blockSubmitButton = false;

				this.largeSize = true; //cambia el tamaño de la lista de recomendaciones.

				return imageFile;

			} else {

				this.validation.fillImage = false;

				this.blockSubmitButton = true;
				
				this.largeSize = false; //regresa el tamaño de la lista de recomendaciones a su estado normal.

				return !imageFile;
			}
		},
		/**
		 * Establece la variable "fillImage" en falso
		 * para indicar que no hay una imagen válida 
		 * disponible.
		 * 
		 * @return mensaje que indica el tamaño que debe
		 * 			contener el archivo de la imagen.
		 * 
		 */
		displayErrorSize() {

			this.validation.fillImage = false;

			return "La imagen no puede pesar más de 6MB";
		},
		/**
		 * Una vez que el usuario suba una
		 * imagen, se ejecutará esta funcion
		 * para crear una URL y ser usada
		 * por la etiqueta <v-img :src="URL">.
		 * 
		 */
		onFileChange() {

			this.imgUrl = URL.createObjectURL(this.file);
		},
		/**
		 * Cuando el usuario dé clic al botón de
		 * "Limpiar búsqueda", se ejecutará esta
		 * función para establecer el valor de la
		 * variable "file" en nulo, y el arreglo de
		 * checkboxes en un arreglo vacío.
		 * 
		 */
		clear() {

			this.checkboxes = [];

			this.file = null;

			this.largeSize = false;
		},
		/**
		 * Si el usuario da clic al botón
		 * "Limpiar sugerencias", se ejecutará
		 * está función para limpiar la variable
		 * que contiene el arreglo de las canciones
		 * de sugerencia.
		 * 
		 */
		clearSuggestedSongs() {

			this.suggestedSongs = [];

			this.suggestedSongsReady = false;
		},
		/**
		 * Inicializa un objeto con el archivo
		 * de la imagen del usuario y los checkboxes
		 * que seleccionó, para después ejecutar a la función que
		 * hará las peticiones a las respectivas APIs.
		 * 
		 */
		submit() {			

			let dataForm = {
				
				imgFile: this.file, 
				genders: this.checkboxes
			};

			this.blockSubmitButton = true;

			this.progressCircular = true;
			
			setTimeout(() => this.doAPICalls(dataForm), 1000);				
		},
		/**
		 * 
		 * 
		 * @param1 objeto con el archivo de la imagen y los checkboxes
		 * 			del género de música que eligió el usuario.
		 */
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

				limit: 10,
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

			} catch (error) {

				console.log(error);			
			}	

			this.progressCircular = false;

			this.blockSubmitButton = false;
		},
		/**
		 * A partir de las emociones y los géneros de música
		 * del usuario, se determinarán las variables para
		 * que el API de Spotify recomiende una serie de
		 * canciones. 
		 * 
		 * @param1 emociones encontradas en la imagen
		 * 			subida por el usuario.
		 * @param2 géneros de música elegidos por el usuario.
		 * 
		 * @return lista de variables para obtener recomendaciones
		 *			por parte de Spotify-
		 */
		getDataRecommendation(emotions, genders) {

			const arrayObject = Object.entries(emotions);

			// eslint-disable-next-line no-unused-vars
			const arrayEmotions = arrayObject.filter(([key, value]) => value > 0);			

			const emotionsAvailable = Object.fromEntries(arrayEmotions);

			console.log(emotionsAvailable);


			//algorithm to determine the tempo base on emotions.
			
			return {

				limit: 10,
				seed_genres: genders, 
				min_danceability: 0.0,
				max_danceability: 1.0,
				min_energy: 0.0,
				max_energy: 1.0,
				min_liveness: 0.0,
				max_liveness: 1.0,
				//max_popularity: 1.0,
				minTempo: 0.0,
				maxTempo: 0.30
			};
		},
		/**
		 * Realiza la petición al API de Microsoft
		 * Azure para obtener las emociones del
		 * rostro del usuario.
		 * 
		 * @param1 archivo que contiene la imagen
		 * 			subida por el usuario.
		 * 
		 * @return respuesta de la llamada al API
		 * 			de reconocimiento facial de 
		 * 			Microsoft Azure.
		 */
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
		/**
		 * Realiza una petición al API de Spotify
		 * con el método GET, para obtener las
		 * recomendaciones que vienen en los parámetros.
		 *
		 * @param1 conjunto de variables para obtener las
		 * 			recomendaciones de Spotify.
		 * 
		 * @return respuesta de la llamada al
		 *			API de recomendación de canciones
		 *			de Spotify.
		 */
		requestAPISpotify(dataRecommendationParams) {

			const AUTH_STR = 'Bearer '.concat('BQD1TFI-cpRJPSJmJVwgc9DA7CC2QWFm6Y3QqES-M89p84TZ7r3WOb1MtR2k_Sk_6xG-KEHKqHokfALLKag');

			const config = {

				headers: {

					'Content-Type': 'application/json',
					'Accept':       'application/json',
					'Authorization': AUTH_STR
				},
				params: dataRecommendationParams
			};

			return axios.get('https://api.spotify.com/v1/recommendations', config);
		}
	}
}

</script>


<style scoped>

.normal-height {

	height: 502.5px;
	overflow-y: auto;
}

.scroll {

	height: 822px;
	overflow-y: auto;	
}

</style>