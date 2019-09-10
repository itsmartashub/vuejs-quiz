<template>
	<div class="question-box-container">
		<b-jumbotron>

			<template v-slot:lead>
				<!-- Some question here? -->
				{{ currQuestion.question }}
			</template>

			<hr class="my-4">

			<b-list-group>
				<b-list-group-item
					v-for="(answer, index) in answers" :key="index"
					@click="selectAnswer(index)"
					:class="answerClass(index)"
					
				>
				<!-- :class="[
						!answered && selectedIndex === index ? 'selected' :
						answered && correctIndex === index ? 'correct' :
						answered && selectedIndex === index  && correctIndex !== index ? 'incorrect' : ''
					]"  ovo smo premestili da bude metod annswerClass(index) -->
					{{ answer }}
				</b-list-group-item>
			</b-list-group>

			<b-button variant="primary"
				@click.prevent="submitAnswer"
				:disabled="selectedIndex === null || answered"
				>Submit</b-button>

			<b-button @click="nextQuestion" :disabled="!answered" variant="success">Next</b-button>
		</b-jumbotron>
	</div>
</template>

<script>
import _ from 'lodash'

export default {
	props: {
		currQuestion: Object, // primamo ovo iz parenta (App.vue)
		nextQuestion: Function,
		inkrement: Function
	},

	data() {
		return {
			selectedIndex: null,
			correctIndex: null,
			arrShuffledAnswers: [],
			answered: false
		}
	},

	computed: {
		answers() {
			let arrAnswers = [...this.currQuestion.incorrect_answers] // kopija arraya umesto da referencujemo original array
			arrAnswers.push(this.currQuestion.correct_answer) // u ovaj gore array netacnih odgovora, dodajemo tacan odg. Malo pre smo ovo returnisali i imali smo samo brojeve
			return arrAnswers
		}
	},

	watch: { // mogu da posmatram promene u mojim props i da pokrenem neku f-ju kad se promene
		// currQuestion() {
		// 	this.selectedIndex = null
		// 	this.shuffleAnswers()
		// }
		//? 2.nacin
		currQuestion: {
			// the callback will be called immediately after the start of the observation
			immediate: true, // dakle ono sto sto ovo immediate: true radi je da umesto da se ovaj watch pokrene samo kada se neki props promeni, vec ce se pokrenuti cim se onaj currQuestion props jos prvi put uopste prosledi/stavi u watch f-ju/properrti, a onda i svaki x kada dodje do neke promene u tom propsu
			handler() {
				this.selectedIndex = null
				this.answered = false // resetujemo answered da ne bude true vise, jer na sledecem pitanju ne zelimo da se answered bude true 
				this.shuffleAnswers()
			}
		}
	},

	methods: {
		selectAnswer(i) {
			console.log(i)
			this.selectedIndex = i;
		},

		shuffleAnswers() {
			let arrAnswers = [...this.currQuestion.incorrect_answers, this.currQuestion.correct_answer]
			// kada je shuffling u pitanju, tj mesanje necega, lodash js biblioteka je dobra u tome npm i lodash

			this.arrShuffledAnswers = _.shuffle(arrAnswers)
			this.correctIndex = this.arrShuffledAnswers.indexOf(this.currQuestion.correct_answer) // zelim da ddojem do indexa tacnog odgovora sa indexOf
		},

		submitAnswer() {
			let isCorrect = false

			if(this.selectedIndex === this.correctIndex) {
				isCorrect = true
			}

			this.answered = true // da kad kliknemo na submit dugme da answered bude true, da ne bi mogli ponovo da klikcemo na odg jer smo za disabled za submit btn stavili da bude true ako je vec odg na pitanje

			this.inkrement(isCorrect) // isCorrect prosledjujemo u f-ju koja se nalazi u parentu tj App.vue
		},

		answerClass(i) {
			let answerClass = ''
			if (!this.answered && this.selectedIndex === i) {
				answerClass = 'selected'
			} else if (this.answered && this.correctIndex === i) {
				answerClass = 'correct'
			} else if (this.answered && this.selectedIndex === i && this.correctIndex !== i) {
				answerClass = 'incorrect'
			}

			return answerClass // obavezno!
		}
	},

	// mounted() {
	// 	console.log(this.currQuestion)
	// 	//? 1. nacin
	// 	// this.shuffleAnswers() // 1 nacin za sufflovanje pitanja odmah pri renderu, a ne samo na klik next() jer nam treba i prvo pitanje da bude shuffovano. A ima i bolji nacin, je da ono currQuestion() stavimo da bude objekat, i da setujemo neke options na taj opbjekat
	// }
}
</script>

<style scoped>
	.list-group {
		margin-bottom: 15px;
	}

	.list-group-item:hover {
		background: #eee;
		cursor: pointer;
	}

	.btn {
		margin: 0 5px;
	}

	.selected {
		background: lightblue;
	}

	.correct {
		background: lightgreen;
	}

	.incorrect {
		background: lightcoral;
	}
</style>