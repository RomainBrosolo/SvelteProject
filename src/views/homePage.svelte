<script>
	import ListArticles from '../components/ListArticles.svelte';
	import Details from '../views/articleDetails.svelte';
	import Router, {location, link} from 'svelte-spa-router';
</script>


 

<Router routes={{
	'/': ListArticles,
	'/article/:id': Details,
}} />