<div class="app">

	<main>
		<div class="body">
			<slot />
		</div>
	</main>

</div>

<style>
	.app {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
		font-family: Helvetica, Arial, sans-serif;
		background-color: blue;
	}

	main {
		flex: 1;
		display: flex;
		flex-direction: column;
		padding: 2rem;
		width: 100%;
		height: 100%;
		max-width: 64rem;
		margin: 0 auto;
		box-sizing: border-box;
	}

	.body {
		background-color: white;
		height: 100%;
		flex: 1;
	}
</style>
