<script>
	var data;
	
	async function getData(url = '') {
		// https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
  		const response = await fetch(url, {
  		  cache: 'default' // *default, no-cache, reload, force-cache, only-if-cached
  		});
  		return response.json();
	}
	
	async function getMap(id = '', hash='') {
		// https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch

		if (id) {
  			const response = await fetch('https://api.beatsaver.com/maps/id/' + id, {cache: 'default'});
			return response.json();
		} else if (hash) {
			const response = await fetch('https://api.beatsaver.com/maps/hash/' + hash, {cache: 'default'});
			return response.json();
		} else {
			return 'null'
		}
	}
	
	getMap('', 'ed876b42a223d1fc0a141c82c916d785a9a7f55f').then(response => data = response)
</script>

<h1>Id: {data?.id}</h1>
<h1>Name: {data?.name}</h1>
<h1>Hash: {data?.versions[0]?.hash}</h1>