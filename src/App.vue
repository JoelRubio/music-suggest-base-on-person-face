<!-- 
MIT License

Copyright (c) 2021 Joel Rubio y Juan Díaz.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
-->

<template>
	<div class="app">

		<v-app>
			
			<v-toolbar>
				<v-toolbar-title class="flex text-center font-weight-black font-title">
					Recomendación de música a partir del rostro de una persona
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

											<h3>Géneros de música</h3>										

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
								
								<v-container fluid>
									<v-alert
										v-model="alert"
										dismissible
										color="#7CE495"
										border="left"
										elevation="2"
										colored-border>										
										{{ this.emptyTracksMessage }}
									</v-alert>
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
			alert: false,
			emptyTracksMessage: '',
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
				'hip-hop',
				'metal',							
				'french',
				'spanish',
				'british',
				'country',
				'chill',
				'jazz',
				'dance',
			],
			suggestedSongs: [], //Arreglo para las canciones sugeridas por el API de Spotify.
			attrSongs: {

				/* Atributos de las canciones para 
				obtener recomendaciones por parte del API de Spotify. */

				limit: 10,       //(0-100)
				seed_genres: '',
				min_valence: 0.0,    //(0.0-1.0)
				max_valence: 1.0,				
			}
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

			if (this.emotionsDetected.length != 0) {

				this.emotionsDetected = [];
			}
		},
		/**
		 * Cuando el usuario dé clic al botón de
		 * "Limpiar búsqueda", se ejecutará esta
		 * función para restablecer ciertos valores.
		 * 
		 */
		clear() {

			this.file             = null;
			this.checkboxes       = [];
			this.emotionsDetected = [];
			this.largeSize        = false;
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

			this.suggestedSongs      = [];
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

			this.alert             = false;
			this.blockSubmitButton = true;
			this.progressCircular  = true;

			if (this.emotionsDetected.length != 0) {

				this.emotionsDetected  = [];
			}
			
			setTimeout(() => this.doAPICalls(dataForm), 50);
		},
		/**
		 * Realiza las llamadas a las APIs de Microsoft Azure
		 * y Spotify.
		 * 
		 * @param1 objeto con el archivo de la imagen y los checkboxes
		 * 			del género de música que eligió el usuario.
		 */
		async doAPICalls(data) {

			let emotions;

			try {

				let responseAzure = await this.requestAPIAzure(data.imgFile);

				if (responseAzure.data.length === 0) {

					throw 'No se encontraron coincidencias con el rostro de la persona';
				}

				emotions = responseAzure.data[0].faceAttributes.emotion;					

			} catch (error) {

				console.log(error);

				this.progressCircular = false;

				return;
			}

			
			this.setDataRecommendation(emotions, data.genders.toString());

			console.log(this.attrSongs);

			try {

				let responseSpotify = await this.requestAPISpotify(this.attrSongs);
				
				if (responseSpotify.data.tracks.length == 0) {

					throw 'No se encontraron coincidencias con la búsqueda';
				}
				
				this.addSongsToSuggestedList(responseSpotify.data.tracks);
				
				this.suggestedSongsReady = true;

			} catch (error) {

				console.log(error);
				
				this.emptyTracksMessage = error;

				this.alert = true;
			}	

			this.progressCircular = false;

			this.blockSubmitButton = false;
		},
		/**
		 * Crea un objeto para cada canción, que contiene
		 * el id, el nombre del artista y el link de la
		 * canción en Spotify, para después agregarlo a un
		 * arreglo de canciones sugeridas.
		 * 
		 * 
		 * @param1 arreglo con las canciones y sus atributos.
		 */
		addSongsToSuggestedList(tracks) {			

			for (let i = 0; i < tracks.length; i++) {					

				let urlSong = tracks[i].external_urls.spotify.slice(0, 25) + 'embed/' + tracks[i].external_urls.spotify.slice(25);

				//Coloca cada objeto al inicio del arreglo.
				this.suggestedSongs.unshift({

					id: i,
					name: tracks[i].artists[0].name,
					link: urlSong
				});
			}
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
		 *			por parte de Spotify.
		 */
		setDataRecommendation(emotions, gendersSelected) {

			const arrayObject = Object.entries(emotions);

			// eslint-disable-next-line no-unused-vars
			const arrayEmotions = arrayObject.filter(([key, value]) => value > 0.0);			

			const emotionsAvailable = Object.fromEntries(arrayEmotions);
			

			//Establece el objeto con las propiedades de su representación y valor en porcentaje.
			this.setEmotionsObject(emotionsAvailable);			

			//Asigna los géneros de música que eligió el usuario.
			this.attrSongs.seed_genres = gendersSelected;

			//Establece los atributos que tendrán las canciones de acuerdo a los porcentajes
			//de las emociones.
			this.setAttrSongs(emotionsAvailable);
		},
		/**
		 * Establece los atributos que tendrán las canciones
		 * de acuerdo a los porcentajes de las emociones
		 * del rostro de la persona. Los atributos son:
		 * valencia mínima y máxima.
		 * 
		 * @param1 emociones del rostro de la persona
		 */
		setAttrSongs(emotions) {

			if (Object.prototype.hasOwnProperty.call(emotions, 'neutral'))
				delete emotions.neutral;

			if (Object.prototype.hasOwnProperty.call(emotions, 'surprise'))
				delete emotions.surprise;


			if (Object.keys(emotions).length === 0)
				return;			

	
			if (Object.keys(emotions).length === 1 && 
				Object.keys(emotions)[0].toString() === 'happiness') {

				this.attrSongs.min_valence = 0.5;
				this.attrSongs.max_valence = 1.0;

				return;
			}
			else if (Object.keys(emotions).length === 1 && 
						(Object.keys(emotions)[0].toString() === 'sadness' ||
						Object.keys(emotions)[0].toString() === 'angry'    || 
						Object.keys(emotions)[0].toString() === 'contempt' ||
						Object.keys(emotions)[0].toString() === 'disgust'  ||
						Object.keys(emotions)[0].toString() === 'fear')) {

				let emotion = Object.values(emotions)[0];

				let emotionValue = (emotion < 0.5) ? emotion : 1.0 - emotion;

				this.attrSongs.min_valence = emotionValue;
				this.attrSongs.max_valence = 0.5;

				return;
			}

			this.setAttrSongsWithMultipleEmotions(emotions);				
		},
		/**
		 * Verifica si dentro de las emociones almacenadas
		 * se encuentra 'happiness', si está, entonces 
		 * se ejecutará la función para determinar los atributos
		 * con emociones positivas y negativas, si no, se
		 * ejecutará la función para emociones negativas.
		 * 
		 * 
		 * @param1 emociones del rostro de la persona.
		 */
		setAttrSongsWithMultipleEmotions(emotions) {

			if (Object.prototype.hasOwnProperty.call(emotions, 'happiness')) {

				this.setAttrSongsWithPositiveAndNegativeEmotions(emotions);

			} else {

				this.setAttrSongsWithNegativeEmotions(emotions);
			}
		},
		/**
		 * Establece los atributos que tendrán las canciones
		 * de acuerdo a los porcentajes de las emociones
		 * del rostro de la persona. Estas emociones se
		 * clasificaron en positivas (felicidad) 
		 * y negativas (enojo, tristeza, desprecio, disgusto, y miedo).
		 * 
		 * @param1 emociones del rostro de la persona.
		 */
		setAttrSongsWithPositiveAndNegativeEmotions(emotions) {

			//Obtiene la propiedad 'happiness' del objeto 'emotions'.
			let positiveEmotion = ((({happiness}) => ({happiness}))(emotions)).happiness;

			//Elimina la propiedad 'happiness' del objeto 'emotions'.
			delete emotions.happiness;

			//Suma los valores del objeto "emotions".
			let negativeEmotions = Object.values(emotions).reduce((a, b) => a + b, 0);

			let result;

			if (positiveEmotion > negativeEmotions) {

				result = positiveEmotion - negativeEmotions;

				this.attrSongs.max_valence = 1.0;

			} else {

				result = negativeEmotions - positiveEmotion;

				this.attrSongs.max_valence = 0.5;
			}

			this.attrSongs.min_valence = result;
		},
		/**
		 * Establece los atributos que tendrán las canciones
		 * de acuerdo a los porcentajes de las emociones
		 * del rostro de la persona. Estas emociones se
		 * clasificaron en emociones negativas
		 * (enojo, tristeza, desprecio, disgusto, y miedo).
		 * 
		 * @param1 emociones del rostro de la persona.
		 */
		setAttrSongsWithNegativeEmotions(emotions) {

			//Suma los valores del objeto "emotions", sin la emoción 'happiness'.
			let negativeEmotions = Object.values(emotions).reduce((a, b) => a + b, 0);

			if (negativeEmotions > 0.5) {

				negativeEmotions = 1.0 - negativeEmotions;

				this.attrSongs.min_valence = 0.0;

				this.attrSongs.max_valence = negativeEmotions;

			} else {

				this.attrSongs.min_valence = negativeEmotions;

				this.attrSongs.max_valence = 0.5;
			}
		},
		/**
		 * Crea un objeto para cada emoción, el cual contiene
		 * su representación a través de un emoji, y el porcentaje
		 * de la emoción. Cada objeto se agregará una arreglo
		 * de emociones detectadas.
		 * 
		 * @param1 emociones detectadas en el rostro de la persona.
		 * 
		 * @return promesa sobre la ejecución de haber creado el arreglo
		 * 			de objetos conteniendo el emoji y el porcentaje de cada
		 * 			emoción.
		 */
		setEmotionsObject(emotionsAvailable) {
			
			this.emotions.forEach(emotion => {
				
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
			const SUB_KEY = '';

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
			const AUTH_STR = 'Bearer '.concat('');

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
