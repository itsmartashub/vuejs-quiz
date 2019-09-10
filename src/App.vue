<template>
  <div id="app">
    <Header
		:brTacnih="numCorrect"
		:brUkupno="numTotal"	
	/>

	<b-container class="bv-example-row">
		<b-row>
			<b-col sm="6" offset="3">
				<QuestionBox
					v-if="arrQuestions.length"
					:currQuestion="arrQuestions[index]"
					:nextQuestion="next"
					:inkrement="increment"
				/>
				<!-- v-if="arrQuestions.length" dakle ovo se renderuje samo ako ima nesto u arrQuestions, ako nema tj ako je arr prazan, nece se prikazati QuestionBox. Ovo je resilo onaj error da je question u QuestionBox.vue undefined, zapravo jeste undefined dok ne dobijemo odg od servera tj dok se ne odradi rikvest i ne vrati ovaj niz pitanja u arrQuestions -->
			</b-col>
		</b-row>
	</b-container>

  </div>
</template>

<script>
import Header from './components/Header.vue'
import QuestionBox from './components/QuestionBox.vue'

export default {
	name: 'app',
	components: {
		Header,
		QuestionBox
	},
	data() {
		return {
			arrQuestions: [],
			index: 0,
			numCorrect: 0,
			numTotal: 0
		}
	},

	methods: {
		next() {
			// this.index++
			if(this.index < (this.arrQuestions.length - 1)) {
				this.index++
			} else {
				confirm(`Game over! Your number of correct answers is ${this.numCorrect}. Want to try again?`)
				this.numCorrect = 0
				this.numTotal = 0
				this.index = 0
			}
		},

		increment(isCorrect) {
			if (isCorrect) {
				this.numCorrect++ // ove vrednosti treba da prosledimo u header
			}
			this.numTotal++ // ove vrednosti treba da prosledimo u header
		}
	},

	mounted() {
		fetch('https://opentdb.com/api.php?amount=10&category=27&type=multiple', {
			method: 'get'

		}).then((response) => {
			// console.log(response.json())
			// return response.json() // Failed to execute 'json' on 'Response': body stream is locked at eval

			console.log(response.clone().json()) // brate ovo sa clone() radi ali ne znam sto, pre toga je pisalo da je locked tj: Failed to execute 'json' on 'Response': body stream is locked at eval
			return response.clone().json()


		}).then((jsonData) => {
			this.arrQuestions = jsonData.results // jer su ovi podaci tj ovaj niz pitanja u response nizu sto je deo apija
		})

	}
}
</script>

<style>
	#app {
		font-family: 'Avenir', Helvetica, Arial;
		text-align: center;
		color: #2c3e50;
		margin-top: 60px;
	}
</style>
