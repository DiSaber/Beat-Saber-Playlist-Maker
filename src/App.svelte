<svelte:head>
	<title>Playlist Maker</title>
</svelte:head>

<script>
	var data;
	var currentId;
	var searchMapName = false;
	var abortMapSearchController = null;
	
	async function getData(url = '') {
		// https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
  		const response = await fetch(url, {
  		  cache: 'default' // *default, no-cache, reload, force-cache, only-if-cached
  		});
  		return response.json();
	}
	
	async function getMap(input = '') {
		// https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch

		if (abortMapSearchController) {
			abortMapSearchController.abort();
		}
		
		abortMapSearchController = new AbortController();
		var { signal } = abortMapSearchController;

		if (input != '') {
			if (searchMapName) {
				const response = await fetch(`https://api.beatsaver.com/search/text/0?q=${encodeURIComponent(input)}&sortOrder=Relevance`, {signal, cache: 'default'});
				return response.json();
			} else {
				if (input.length < 40) {
  					const response = await fetch(`https://api.beatsaver.com/maps/id/${input}`, {signal, cache: 'default'});
					return response.json();
				} else {
					const response = await fetch(`https://api.beatsaver.com/maps/hash/${input}`, {signal, cache: 'default'});
					return response.json();
				}
			}
		}
	}
	
	getMap('ed876b42a223d1fc0a141c82c916d785a9a7f55f').then(response => data = response);
</script>

{#if !data?.hasOwnProperty('error') && (searchMapName ? data?.hasOwnProperty('docs') : data?.hasOwnProperty('id'))}
	{#if searchMapName}
		{#each data?.docs as map}
			<img src={map?.versions[0]?.coverURL} alt={map?.name} width="100" height="100">
			<h3>Id: {map?.id}</h3>
			<h3>Name: {map?.name}</h3>
			<h3>Hash: {map?.versions[0]?.hash}</h3>
		{/each}
	{:else}
		<img src={data?.versions[0]?.coverURL} alt={data?.name}>
		<h1>Id: {data?.id}</h1>
		<h1>Name: {data?.name}</h1>
		<h1>Hash: {data?.versions[0]?.hash}</h1>
	{/if}
{:else}
	<h1>{searchMapName ? 'No maps found' : 'No map found'}</h1>
{/if}
<input placeholder={searchMapName ? 'Enter a map name' : 'Enter a map id or hash'} bind:value={currentId} on:input={getMap(currentId).then(response => data = response)}>
<input type='checkbox' bind:checked={searchMapName} on:change={getMap(currentId).then(response => data = response)}>