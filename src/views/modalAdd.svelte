<script>
let listArticles = [];
let categories = [];
let modalIsOpen = false;
let article = {
    id: undefined,  
    title: '',
    description: '',
    category: '',
    status: '',
    created:undefined,
    updated: undefined,
}

if(localStorage.getItem("articles")){
    listArticles = JSON.parse(localStorage.getItem("articles"));
    categories = JSON.parse(localStorage.getItem("categories"));
    modalIsOpen = JSON.parse(localStorage.getItem("modalIsOpen"));
}
    
$: localStorage.setItem("articles", JSON.stringify(listArticles))
$: localStorage.setItem("categories", JSON.stringify(categories))
$: localStorage.setItem("modalIsOpen", JSON.stringify(modalIsOpen))


const createArticle = () => {
        if(!article.title.trim()) {
            article.title = '';
            return
        }

        article.description != '' || !article.description.trim() ? article.status = 'brouillon' : article.status = 'archive';
        article.id = Date.now();
        article.created = new Date().toLocaleDateString("fr");

        
        listArticles = [...listArticles, article];
        
        categories = [...categories, article.category];
        categories = [...new Set (categories)]
        
        console.log(listArticles);
        console.log(categories);
        article = {
        id: undefined,
        title: '',
        description: '',
        category: '',
        status: '',
        }
    }
</script>
<form class="modal modal-add" >
    <div class="modal-bg close-modal" ></div>
    <div class="modal-inner">
        <div class="modal-wrap">
            <div class="icon-close close-modal" on:click={() => {modalIsOpen = false; console.log('fermeture')}}></div>
            <h2 class="modal-h2">Ajouter une note</h2>
            <h3 class="modal-h3">Le titre:</h3>
            <span class="text">
                <input type="text" >
            </span>
            <h3 class="modal-h3">Le texte:</h3>
            <span class="text">
                <textarea name="note" required></textarea>
            </span>
            <h3 class="modal-h3">Ajouter les mots-clefs:</h3>
            <span class="text text-small">
                <textarea name="keywords" placeholder="Ecrire les mots clefs séparés par une vigurle" ></textarea>
            </span>
            <h3 class="modal-h3">Ajouter une catégorie:</h3>
            <span class="categories">
                
            </span>
            <div class="modal-foot">
                <button class="button button-large">Annuler</button>
                <button type="submit" class="button button-large" on:click={createArticle}>Ajouter</button>
            </div>
        </div>
    </div>   
</form> 