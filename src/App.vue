<template>
	<div class="app">

		<v-app>
			
			<v-toolbar>
				<v-toolbar-title class="flex text-center font-weight-black font-title">
					Recomendaciones de música a partir del rostro de una persona
				</v-toolbar-title>
			</v-toolbar>

			<v-main>								

				<v-container fluid class="grey lighten-5">

					<br>				

					<v-row>
						
						<v-col md="6">
							<v-card
								rounded
								class="pa-2">							
								
								<v-toolbar
									color="#5EA6FF"																		
									dark>															

									<v-toolbar-title class="font-title font-weight-black">
										Selecciona una imagen y el género de música
									</v-toolbar-title>

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
												placeholder="Seleccionar imagen"
												prepend-icon="mdi-camera"
												label="Rostro de una persona">
											</v-file-input>

											<v-row>
												<v-col>
													<v-img :src="imgUrl" v-if="validation.fillImage"
														class="rounded-lg" 
														width="350" 
														height="320">												
													</v-img>
												</v-col>
												
												<v-col>																					
													<v-list v-if="validation.fillImage" class="scroll-emotions">
														<v-list-item  v-for="emotion in emotionsDetected" :key="emotion.percent">																												
															<v-list-item-action>
																<v-list-item-action-text v-text="emotion.emoji" class="emoji-font-size"></v-list-item-action-text>
															</v-list-item-action>

															<v-list-item-content>
																<v-list-item-title v-text="'% ' + emotion.percent"></v-list-item-title>
															</v-list-item-content>
														</v-list-item>
													</v-list>								
												</v-col>
											</v-row>

										</v-container>
										
									</v-card>

									<v-card flat>
										<v-card-text>

											<h3>Género de música</h3>										

											<v-alert outlined color="#5EA6FF">
												<v-container fluid class="scroll-card">													
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
											</v-alert>
										</v-card-text>
									</v-card>
								
						
									<v-card flat>
										<v-container fluid>

											<v-btn												
												rounded
												elevation="4"
												color="#5EA6FF"
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
								rounded>
					
								<v-toolbar color="#7CE495" dark>
									<v-toolbar-title class="font-title font-weight-black">									
										Lista de recomendaciones																												
									</v-toolbar-title>

									<v-spacer></v-spacer>

									<v-tooltip bottom v-if="suggestedSongsReady">
										<template v-slot:activator="{ on, attrs }">
										
											<v-btn 
												color="white"										
												icon
												@click="clearSuggestedSongs()"
												v-bind="attrs"
												v-on="on">

												<v-icon>mdi-delete</v-icon>											
											</v-btn>
										</template>

										<span>Limpiar lista de sugerencias</span>
									</v-tooltip>

								</v-toolbar>											

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
		
								<v-container fluid v-if="progressCircular" class="progress-circular">
														
									<v-progress-circular
										:size="50"
										indeterminate
										color="#7CE495">									
									</v-progress-circular>
								</v-container>

								<v-list subheader v-if="suggestedSongsReady" :class="largeSize ? 'scroll-height' : 'normal-height'">																		

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
																	
									</v-list-item>
								</v-list>								

							</v-card>
						</v-col>
					</v-row>

					<br>
					<br>

				</v-container>				
			</v-main>

		

			<v-footer padless dark class="indigo lighten-1">
				<v-card 
					class="indigo lighten-1 white--text flex text-center"
					flat
					tile>
					
					<v-card-text>						
						<v-tooltip bottom>

							<template v-slot:activator="{ on, attrs }">
							
								<v-btn							
									class="mx-4 white-text"
									icon
									href="https://github.com/JoelRubio/music-suggest-base-on-person-face/tree/master"
									target="_blank"
									v-bind="attrs"
									v-on="on">

									<v-icon>fab fa-github</v-icon>
								</v-btn>								
							</template>

							<span>Código en Github</span>

						</v-tooltip>						
					</v-card-text>

					<v-card-text>
						<a  style="text-decoration: none;"
							class="white--text"
							href="https://github.com/JoelRubio/music-suggest-base-on-person-face/blob/main/LICENSE" 
							target="_blank">
							Free & Open Source (MIT)
						</a>
					</v-card-text>
			
					<v-card-text class="white-text">
						<strong>{{ new Date().getFullYear() }}</strong> — <strong>Sistemas de Inteligencia Artificial para la Toma de Decisiones</strong>
					</v-card-text>

				</v-card>				
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
			emotions: [
				
				//Conjunto de emociones y su representación a través de emojis.

				{
					happiness: String.fromCodePoint('0x1F600')					
				},
				{
					anger: String.fromCodePoint('0x1F620')			
				},
				{
					sadness: String.fromCodePoint('0x1F641')
				},
				{
					surprise: String.fromCodePoint('0x1F62F')
				},
				{
					disgust: String.fromCodePoint('0x1F92E')
				},
				{
					contempt: String.fromCodePoint('0x1F644')
				},
				{
					fear: String.fromCodePoint('0x1F628')
				},
				{
					neutral: String.fromCodePoint('0x1F610')
				}
			],
			emotionsDetected: [], //Conjunto de emociones detectadas en el rostro de una persona.
			genders: [
				
				//Géneros de música aceptados por el API de Spotify.
				'pop',
				'indie-pop',
				'rock',
				'electronic',
				'progressive-house',
				'deep-house',							
				'french',
				'spanish',
				'british',
				'metal',
				'jazz',
				'dance',
				'chill',				
				'salsa',
				'country'
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

				this.validation.fillImage = true;
				
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

			return "La imagen no puede pesar más de 6 MB";
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

			this.emotionsDetected = [];
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

			this.emotionsDetected = [];

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
			
			setTimeout(() => this.doAPICalls(dataForm), 50);		
								

			//console.log(this.emotionsDetected);
		},
		/**
		 * 
		 * 
		 * @param1 objeto con el archivo de la imagen y los checkboxes
		 * 			del género de música que eligió el usuario.
		 */
		async doAPICalls(data) {

			let emotions;

			try {

				let responseAzure = await this.requestAPIAzure(data.imgFile);

				if (responseAzure.data.length === 0) {

					throw 'The response of Microsoft Azure is empty';
				}

				console.log(responseAzure);

				emotions = responseAzure.data[0].faceAttributes.emotion;					

			} catch (error) {

				console.log(error);

				this.progressCircular = false;

				return;
			}

			
			let dataRecommendation = this.getDataRecommendation(emotions, data.genders);

			
			try {

				let responseSpotify = await this.requestAPISpotify(dataRecommendation);

				//tracks is an array.
				//let artistName = responseSpotify.data.tracks[0].artists[0].name;
				//let albumName  = responseSpotify.data.tracks[0].album.name;
				//let uriSong    = responseSpotify.data.tracks[0].uri;
				//let songName   = responseSpotify.data.tracks[0].name;
				//let preUrl = responseSpotify.data.tracks[0].external_urls.spotify;
				
				//https://open.spotify.com/track/...				

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
		getDataRecommendation(emotions, gendersSelected) {

			const arrayObject = Object.entries(emotions);

			// eslint-disable-next-line no-unused-vars
			const arrayEmotions = arrayObject.filter(([key, value]) => value > 0.0);			

			const emotionsAvailable = Object.fromEntries(arrayEmotions);
									
			
			this.setEmotionsObject(emotionsAvailable);			

		
			//Determinar la valencia de la canción (si es más o menos positiva).
			let valance = calculateValance(emotionsAvailable);

			//Determinar el tempo de la canción.
			let tempo = calculateTempo(emotionsAvailable);
			
			return {

				limit: 10, //(0-100)
				seed_genres: gendersSelected.length > 0 ? gendersSelected.toString() : this.genders.toString(), 

				/*min_danceability: 0.0, //(0.0-1.0)
				max_danceability: 1.0, //(0.0-1.0)*/
				min_energy: 0.0, //(0.0-1.0)
				max_energy: 1.0, //(0.0-1.0)
				//loudness: 0.0, //(0.0-1.0)
				valance: valance, //(0.0-1.0)
				minTempo: tempo.min, //(40-200)
				maxTempo: tempo.max //(40-200)
			};
		},
		calculateValance(emotionsAvailable) {

			//Valencia de emoción neutral.			
			if (Object.keys(emotionsAvailable).length === 1 && 
				Object.keys(emotionsAvailable)[0].toString() === 'neutral' ||
				Object.keys(emotionsAvailable)[0].toString() === 'surprise') {

				return 0.5;
			}

			//Valencia de emoción felicidad.	
			if (Object.keys(emotionsAvailable).length === 1 && 
				Object.keys(emotionsAvailable)[0].toString() === 'happiness') {

				return Object.values(emotionsAvailable)[0];
			}

			//Valencia de emoción tristeza, enojo, desprecio, disgusto, y miedo.	
			if (Object.keys(emotionsAvailable).length === 1 && 
				(Object.keys(emotionsAvailable)[0].toString() === 'sadness'  ||
				 Object.keys(emotionsAvailable)[0].toString() === 'angry'    || 
				 Object.keys(emotionsAvailable)[0].toString() === 'contempt' ||
				 Object.keys(emotionsAvailable)[0].toString() === 'disgust'  ||
				 Object.keys(emotionsAvailable)[0].toString() === 'fear')) {

				let emotion = Object.values(emotionsAvailable)[0];

				return (emotion < 0.5) ? emotion : 1.0 - emotion;
			}

			return getValenceOfCombinationEmotion(emotionsAvailable);			
		},
		getValenceOfCombinationEmotion(emotionsAvailable) {

			//Regresa un arreglo de las llaves del objecto "emotionsAvailable".
			let arrayEmotionsKeys   = Objects.keys(emotionsAvailable);

			//Regresa un arreglo de los valores del objeto "emotionsAvailable".
			let arrayEmotionsValues = Objects.values(emotionsAvailable); 


			if (arrayEmotionsKeys.length === 3 && 
				arrayEmotionsValues[0] > arrayEmotionsValues[1] ||
				arrayEmotionsValues[0] > arrayEmotionsValues[2]) {
				

				if (arrayEmotionsValues[1] > 0.0 &&
					arrayEmotionsValues[2] > 0.0) {

					let combinationEmotion = arrayEmotionsValues[1] + arrayEmotionsValues[2];

					if (combinationEmotion > arrayEmotionsValues[0]){

						return arrayEmotionsValues[0] - (combinationEmotion / 2.0);
					} 
					else {

						return arrayEmotionsValues[0] - combinationEmotion;
					}					

				} else if (arrayEmotionsValues[1] > 0.0) {

					let combinationEmotion = arrayEmotionsValues[0] - arrayEmotionsValues[1];

					return combinationEmotion > 0.0 ? combinationEmotion : 0.0;

				} else if (arrayEmotionsValues[2] > 0.0) {

					let combinationEmotion = arrayEmotionsValues[0] - arrayEmotionsValues[2];

					return combinationEmotion > 0.0 ? combinationEmotion : 0.0;
				}
			}
			
			return 0.5;

			/*
			//Valencia de enojo.
			if (emotionsAvailable.angry > emotionsAvailable.happiness ||
				emotionsAvailable.angry > emotionsAvailable.sadness) {

				if (emotionsAvailable.hapiness > 0.0 &&
					emotionsAvailable.sadness > 0.0) {

					return emotionsAvailable.angry + emotionsAvailable.sadness - emotionsAvailable.happiness;

				} else if (emotionsAvailable.sadness > 0.0) {

					let combinationEmotion = emotionsAvailable.angry + emotionsAvailable.sadness; 

					return combinationEmotion < 99.99 ? combinationEmotion : 99.99;

				} else if (emotionsAvailable.happiness > 0.0) {

					let combinationEmotion = emotionsAvailable.angry - emotionsAvailable.happiness; 

					return combinationEmotion > 0.0 ? combinationEmotion : 0.0;
				}
			}

			//Valencia tristeza
			if (emotionsAvailable.sandess > emotionsAvailable.happiness ||
				emotionsAvailable.sadness > emotionsAvailable.angry) {

				if (emotionsAvailable.hapiness > 0.0 &&
					emotionsAvailable.angry > 0.0) {

					return emotionsAvailable.sadness + emotionsAvailable.angry - emotionsAvailable.happiness;

				} else if (emotionsAvailable.angry > 0.0) {

					let combinationEmotion = emotionsAvailable.sadness + emotionsAvailable.angry; 

					return combinationEmotion < 99.99 ? combinationEmotion : 99.99;

				} else if (emotionsAvailable.happiness > 0.0) {

					let combinationEmotion = emotionsAvailable.sadness - emotionsAvailable.happiness; 

					return combinationEmotion > 0.0 ? combinationEmotion : 0.0;
				}
			}*/
		
		},
		calculateTempo(emotionsAvailable) {

			/*
				Largo (very slow) is 40–60 BPM.		   (maybe sadness)
				Larghetto (less slow) is 60–66 BPM.	   (maybe sadness)
				Adagio (moderately slow) is 66–76 BPM. 
				Andante (walking speed) is 76–108 BPM.  (maybe happy)
				Moderato (moderate) is 108–120 BPM.	   (neutral)
				Allegro (fast) is 120–168 BPM.		   (maybe happy depending the percent)
				Presto (faster) is 168–200 BPM.		   (maybe happy, angry and disgust depending the percent)
				Prestissimo (even faster) is 200+ BPM. (maybe angry and disgust depending the percent)
			*/

			let tempo = {

				min: 0.0,
				max: 1.0
			};
			
			if (emotionsAvailable.neutral > 0.0) {

				return tempo;
			}
		},
		/**
		 * 
		 * 
		 * @param1 emociones detectadas en el rostro de la persona.
		 * 
		 * @return promesa sobre la ejecución de haber creado el arreglo
		 * 			de objetos conteniendo el emoji y el porcentaje de cada
		 * 			emoción.
		 */
		setEmotionsObject(emotionsAvailable) {
			

			return new Promise((resolve) => {

				resolve(this.emotions.forEach(emotion => {
				
					for (let [emotionKey, emotionValue] of Object.entries(emotion)) {							

						for (let [emotionAvailableKey, emotionAvailableValue] of Object.entries(emotionsAvailable)) {									

							if (emotionKey.toString() === emotionAvailableKey.toString()) {
																											
								this.emotionsDetected.push({
																	
									emoji: emotionValue,
									percent: (emotionAvailableValue * 100).toFixed(2)
								});							
							}
						}				
					}
				}));
			});
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

			//Clave de suscripción con Microsoft Azure.
			const SUB_KEY = '5a1b891888e1413ca3340a187c2f6f91';

			return axios({

				method: 'post',
				url: 'https://development-faceapi.cognitiveservices.azure.com/face/v1.0/detect?overload=stream&returnFaceId=true&returnFaceAttributes=emotion&detectionModel=detection_01',
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

			//Token de autenticación con Spotify. Se reinicia cada 1 hora.
			const AUTH_STR = 'Bearer '.concat('BQDe9dd6nRG0aOoek7mju_2ho2pC_QdoN6Lil7uKcKFQXu0ptJuHJrnM_O3yNt9G_H0B8P3Gz0sDDt3237o');

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

.font-title {

	font-family: 'Poppins'
}

.scroll-emotions {

	height: 320px;
	overflow-y: auto;
}

.emoji-font-size {

	font-size: 24px;
}

.scroll-card {

	height: 200px;
	overflow-y: auto;	
}

.normal-height {

	height: 400.5px;
	overflow-y: auto;
}

.scroll-height {

	height: 720.5px;
	overflow-y: auto;	
}

.progress-circular {

	margin: 163.5px 280px;
}

</style>