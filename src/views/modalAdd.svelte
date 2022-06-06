<script>
import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();
let listArticles = [];
let categories = [];
let article = {
        id: undefined,  
        title: '',
        description: '',
        categories: [],
        lock: undefined,
        mots_clefs : [],
        created:undefined,
        updated: undefined,
    }

if(localStorage.getItem("articles")){
    listArticles = JSON.parse(localStorage.getItem("articles"));
    categories = JSON.parse(localStorage.getItem("categories"));
}
    
$: localStorage.setItem("articles", JSON.stringify(listArticles))
$: localStorage.setItem("categories", JSON.stringify(categories))

    function closeModal() {
        dispatch('isClosed', false);
    }

    const createArticle = () => {
        dispatch('newArticle', article);
        article = {
        id: undefined,
        title: '',
        description: '',
        categories: [],
        lock: false,
        mots_clefs: [],
        }
        closeModal();
    }
</script>
<form class="modal modal-add" on:submit|preventDefault={createArticle} >
    <div class="modal-bg close-modal" ></div>
    <div class="modal-inner">
        <div class="modal-wrap">
            <div class="icon-close close-modal" on:click={() => closeModal()}></div>
            <h2 class="modal-h2">Ajouter une note</h2>
            <h3 class="modal-h3">Le titre:</h3>
            <span class="text">
                <input type="text" bind:value={article.title} required>
            </span>
            <h3 class="modal-h3">Le texte:</h3>
            <span class="text">
                <textarea name="note" bind:value={article.description}></textarea>
            </span>
            <h3 class="modal-h3">Ajouter les mots-clefs:</h3>
            <span class="text text-small">
                <textarea name="keywords" placeholder="Ecrire les mots clefs séparés par une vigurle"bind:value={article.mots_clefs} ></textarea>
            </span>
            <h3 class="modal-h3">Ajouter une catégorie:</h3>
            <span class="categories">
                <input type="text" placeholder="Ecrire les catégories séparés par une vigurle" bind:value={article.categories}>
            </span>
            <div class="modal-foot">
                <button type="reset" class="button button-large" on:click={() => closeModal()}>Annuler</button>
                <button type="submit" class="button button-large">Ajouter</button>
            </div>
        </div>
    </div>   
</form> 