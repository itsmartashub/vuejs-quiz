<template>
	<div>
		<b-nav tabs>
			<b-nav-item disabled><b>Fancy Quiz App</b></b-nav-item>
			<b-nav-item disabled>Couter: {{ brTacnih }}/{{ brUkupno }}</b-nav-item>
		</b-nav>
	</div>
</template>

<script>
export default {
	props: [
		'brTacnih',
		'brUkupno'
	]
}
</script>