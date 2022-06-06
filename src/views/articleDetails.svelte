<script>
import { createEventDispatcher } from 'svelte';
let listArticles = [];
export let params;
if(localStorage.getItem("articles")){
    listArticles = JSON.parse(localStorage.getItem("articles"));
}

 const item = listArticles.find((data) => data.id = params.id);

</script>
<nav>
    <ul class="nav-links">
        <li>
            <a href="/"> Retour</a>
        </li>
    </ul>
</nav>
<main>
<ul class="notes">
<li class="note" id="{item.id}">
    <h2>{item.title}</h2>
    <p>{item.description}</p>
    <p>{item.categories ? item.categories : ""}</p>
    <p>Crée le {item.created ? item.created : ""}</p>
    <p>{item.updated != undefined ? 'Dernière Modification le ' + item.updated : ''}</p>
    <div class="note-foot">
        {#if item.mots_clefs}
            <ul class="keywords">
                {#each item.mots_clefs as keyword}
                    <li>{keyword}</li>
                {/each}
            </ul>
        {/if}
        
    </div>
    
</li>
</ul>
</main>