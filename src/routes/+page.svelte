<script>	
	let query = "";
	let placeholder = "Describe a restaurant...";
	let isSearching = false;
	let isError = false;
	let results = [];
	let showInfo = false;
	let loadingObj = {
		name: "Loading...",
		display_phone: "",
		url: ""
	};

	let ROOT_URL = "https://xgboost-restaurant.uw.r.appspot.com/?q=";
	
	function handleKeyPress(e, q){
		let key=e.keyCode || e.which;
			if (key==13){
				search(q);
			}
		}

	async function search(q) {
		if(q.length === 0) return;
		isSearching = true;
		isError = false;
		results = [];
		for(let i = 0; i < 10; i++) {
			results.push(loadingObj)
		}
		try {
			let res = await fetch(ROOT_URL + q);
			if(res.ok) {
				isError = false;
				res = await res.json();
				results = res.restaurants.map(e => {
					let body = JSON.parse(e)
					let { rating } = body;
					console.log(body.name, body.rating)
					if([0.5, 1.5, 2.5, 3.5, 4.5].includes(rating)) {
						body.half_star = true;
					} else {
						body.half_star = false;
					}
					return body;
				});
			} else {
				results = [];
				isError = true;
			}
			isSearching = false;
		} catch(err) {
			results = [];
			isSearching = false;
			isError = true;
		}
	}

</script>

<svelte:head>
	<title>Probabilistic Restaurant Search Engine</title>
	<meta name="description" content="Restaurant Recommendation" />
</svelte:head>

<section>
	<div style="width: 100%">
		{#if showInfo}
			<div class="infopanel">
				This is a search engine that Oliver Melgrove wrote for the final project of Statistics 101C taught by Shirong Xu at UCLA. It uses a dataset of Yelp reviews from Santa Barbara to make a prediction of what restaurants someone would like to see given a search query. The reviews have been encoded using GloVe pre-trained twitter embeddings, and the model has been trained using XGBoost.
			</div>
			<div on:click={() => showInfo = false} class="showinfo">
				HIDE INFO
			</div>
		{:else}
			<div on:click={() => showInfo = true} class="showinfo">
				SHOW INFO
			</div>
		{/if}
	</div>
	<div class="title">
		Probabilistic Restaurant Search Engine
	</div>
	<div class="search-bar">
		<input type="text" bind:value={query} {placeholder} on:keypress={e => handleKeyPress(e, query)}>
		<button disabled={isSearching} on:click={() => search(query)}>Search</button>
	</div>
	{#if results.length > 0 && !isError}
			{#each results as row}
				<div class="result">
					<div class="name">
						{row.name}
					</div>
					<div class="info">
						{row.display_phone}
						{#if row.url?.length > 0}
							<a target="_blank" href={row.url}>Website</a>
						{/if}
						{#if row?.rating}
							{#each {length: Math.floor(row.rating)} as _}
							<svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" fill="white" viewBox="0 0 24 24"><path d="M12 .587l3.668 7.568 8.332 1.151-6.064 5.828 1.48 8.279-7.416-3.967-7.417 3.967 1.481-8.279-6.064-5.828 8.332-1.151z"/></svg>
							{/each}
							{#if row.half_star}
							<svg style="margin-left: -2px" xmlns="http://www.w3.org/2000/svg" width="12" height="12" fill="white" viewBox="0 0 24 24"><path d="M12 .587l-3.668 7.568-8.332 1.151 6.064 5.828 -1.48 8.279 7.416-3.967z"/></svg>
							{/if}
						{/if}
					</div>
				</div>
			{/each} 
	{/if}

	{#if isError}
		<div class="result" style="height: 80px">
			<div class="name" style="height: 60px">
			Not getting a response from the API. You may have not included enough words in the query for the engine to generate a prediction. Otherwise, Oliver probably shut it off because Google Cloud costs way too much money. Au revoir!
			</div>
		</div>
	{/if}

</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		margin: 0 0 50px 0;
	}

	h1 {
		width: 100%;
	}

	input {
		width: 100%;
		border: solid 2px black;
		font-size: 14px;
		padding-left: 4px;
	}

	input:focus {
		border-color: blue;
		outline: none;
		color: blue
	}

	.search-bar {
		width: 70%;
		display: flex;
		flex-direction: row;
		height: 40px;
	}

	.result {
		width: 70%;
		display: flex;
		flex-direction: column;
		height: 40px;
		border: solid 1px blue;
		margin-top: 8px;
		background-color: blue
	}

	button {
		margin-left: 5px;
		padding: 0 10px 0 10px;
		cursor: pointer;
		border: solid 2px black;
		background-color: white;
		font-size: 14px;
		font-weight: 600;
	}

	button:hover {
		background-color: blue;
		border-color: blue;
		color: white;
	}

	.title {
		font-size: 24px;
		margin-bottom: 20px;
		font-weight: 400;
		text-align:center;
		padding: 20px 0px;
	}

	.title:hover {
		color: blue;
	}
	
	.name {
		margin-top: 2px;
		font-weight: 600;
		font-size: 12px;
		height: 17px;
		overflow: hidden;
		color: white;
		padding-left: 4px;
	}

	.info {
		font-size: 12px;
		color: white;
		padding-left: 4px;
	}

	a {
		color: white
	}

	.showinfo {
		color: blue;
		font-size: 10px;
		font-weight: 800;
		cursor: pointer;
		width: 100%;
		margin-bottom: 50px;
	}

	.showinfo:hover {
		background-color: blue;
		color: white;
	}

	.infopanel {
		color: blue;
		font-size: 12px;
		font-weight: 600;
		padding: 10px 16px;
	}

</style>
