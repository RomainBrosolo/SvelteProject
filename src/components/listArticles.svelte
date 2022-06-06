<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/icon?family=Material+Icons"
/>
<script>
	import ModalAdd from '../views/modalAdd.svelte';
    import ModalEdit from '../views/modalEdit.svelte';
    import IconButton from '@smui/icon-button';
    
    let listArticles = [];
    let categories = [];
    let mots_clefs = [];
    let isNew = true;
    let updatedArticle = {};
    let modalIsOpen = false;
    let inputSearch = '';
    let listSearch = [];
    let listFiltreCategorie = [];
    let listFiltreMot = [];
    let article = {
        id: undefined,  
        title: '',
        description: '',
        category: '',
        lock: '',
        mots_clefs : [],
        created:undefined,
        updated: undefined,
    }
    
    if(localStorage.getItem("articles")){
        listArticles = JSON.parse(localStorage.getItem("articles"));
        categories = JSON.parse(localStorage.getItem("categories"));
        mots_clefs = JSON.parse(localStorage.getItem("mots_clefs"));
    }
    
    $: localStorage.setItem("articles", JSON.stringify(listArticles))
    $: localStorage.setItem("categories", JSON.stringify(categories))
    $: localStorage.setItem("mots_clefs", JSON.stringify(mots_clefs))

    
    const createArticle = (event) => {
        // if(!article.title.trim()) {
        //     article.title = '';
        //     return
        // }

        event.detail.description == '' || !event.detail.description.trim() ? event.detail.status = 'brouillon' : event.detail.status = 'archive';
        event.detail.id = Date.now();
        event.detail.created = new Date().toLocaleDateString("fr");
        if( event.detail.mots_clefs.length > 0){
            if(event.detail.mots_clefs.includes(',')){
                event.detail.mots_clefs = event.detail.mots_clefs.split(',');
            }
            else{
                event.detail.mots_clefs= event.detail.mots_clefs.split(" ");
            }
        }
        if( event.detail.categories.length > 0){
            if(event.detail.categories.includes(',')){
                event.detail.categories = event.detail.categories.split(',');
            }
            else{
                event.detail.categories= event.detail.categories.split(" ");
            }
        }
        
        listArticles = [...listArticles, event.detail];
        categories = [...categories, ...event.detail.categories];
        categories = [...new Set (categories)]
        mots_clefs = [...mots_clefs, ...event.detail.mots_clefs];
        mots_clefs = [...new Set (mots_clefs)]
        listFiltreMot = [];
        listFiltreCategorie = [];
        
    }

    const updateArticle = (event) => {
        console.log(event.detail)
        const productIndex = listArticles.findIndex(p => p.id === event.detail.id);
        event.detail.description == '' || !event.detail.description.trim() ? event.detail.status = 'brouillon' : event.detail.status = 'archive';
        if( event.detail.mots_clefs.length > 0){
            if(event.detail.mots_clefs.includes(',')){
                event.detail.mots_clefs = event.detail.mots_clefs.split(',');
            }
        }
        if( event.detail.categories.length > 0){
            if(event.detail.categories.includes(',')){
                event.detail.categories = event.detail.categories.split(',');
            }
        }
        event.detail.updated = new Date().toLocaleDateString("fr");
        listArticles[productIndex]= event.detail;
        categories = listArticles.map(data => data.categories).flat();
        categories = [...new Set (categories)]
        mots_clefs = listArticles.map(data => data.mots_clefs).flat();
        mots_clefs = [...new Set (mots_clefs)]
        listFiltreMot = [];
        listFiltreCategorie = [];
    }

    const deleteArticle = (id) => {
        listArticles = listArticles.filter(item => item.id !== id);
        categories = listArticles.map(data => data.categories).flat();
        categories = [...new Set (categories)]
        mots_clefs = listArticles.map(data => data.mots_clefs).flat();
        mots_clefs = [...new Set (mots_clefs)]
        listFiltreMot = [];
        listFiltreCategorie = [];
    }

    const searchArticle = () => {
        listSearch = listArticles.filter((item) => item.title.includes(inputSearch) || item.description.includes(inputSearch));
    }

    const closeModal = () => {
        modalIsOpen = false;
    }

    const filtreCategorie = (key) => {
        listFiltreMot = [];
        listFiltreCategorie = listArticles.filter((data) => data.categories.includes(key));
    }

    const filtreMot = (key) => {
        listFiltreCategorie = [];
        listFiltreMot = listArticles.filter((data) => data.mots_clefs.includes(key));
    }


</script>

<form on:submit|preventDefault={searchArticle}>
    <input type="text" placeholder="Rechercher" bind:value={inputSearch} >
    <button type="submit">Search</button>
</form>


<h1>Liste des Articles</h1>
{#if listArticles.length == 0}
<p>Liste vide, veuillez créer un article</p>
{/if}
{#if listFiltreCategorie.length == 0 && listFiltreMot.length == 0}
<div class="list" style="display:flex; gap:20px; overflow-x: auto">
{#each listArticles as item}
<div class="article" style="border: 1px solid black;heigth:100px; min-width:200px;padding:10px; ">
    <p>titre : {item.title}</p>
    <p>{item.description != '' ? 'description : ' + item.description : ''}</p>
    <p>{item.categories != undefined ? 'categories : ' + item.categories : ''}</p>
    <p>{item.mots_clefs != undefined ? 'mots clefs : ' + item.mots_clefs : ''}</p>
    <p>statut : <span style={item.status =='brouillon' ? "color:red;" : ""}>{item.status}</span></p>
    <p>Crée le {item.created}</p>
    <p>{item.updated != undefined ? 'Dernière Modification le ' + item.updated : ''}</p>
    <IconButton class="material-icons" on:click={deleteArticle(item.id)}>delete</IconButton>
    <IconButton class="material-icons" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}} >edit</IconButton>
</div>
{/each}
</div>
{:else if listFiltreMot.length > 0}
<div class="list" style="display:flex; gap:20px; overflow-x: auto">
    {#each listFiltreMot as item}
    <div class="article" style="border: 1px solid black;heigth:100px; min-width:200px;padding:10px; ">
        <p>titre : {item.title}</p>
        <p>{item.description != '' ? 'description : ' + item.description : ''}</p>
        <p>{item.categories != undefined ? 'categories : ' + item.categories : ''}</p>
        <p>{item.mots_clefs != undefined ? 'mots clefs : ' + item.mots_clefs : ''}</p>
        <p>statut : <span style={item.status =='brouillon' ? "color:red;" : ""}>{item.status}</span></p>
        <p>Crée le {item.created}</p>
        <p>{item.updated != undefined ? 'Dernière Modification le ' + item.updated : ''}</p>
        <IconButton class="material-icons" on:click={deleteArticle(item.id)}>delete</IconButton>
        <IconButton class="material-icons" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}} >edit</IconButton>
    </div>
    {/each}
    </div>
{:else}
<div class="list" style="display:flex; gap:20px; overflow-x: auto">
    {#each listFiltreCategorie as item}
    <div class="article" style="border: 1px solid black;heigth:100px; min-width:200px;padding:10px; ">
        <p>titre : {item.title}</p>
        <p>{item.description != '' ? 'description : ' + item.description : ''}</p>
        <p>{item.categories != undefined ? 'categories : ' + item.categories : ''}</p>
        <p>{item.mots_clefs != undefined ? 'mots clefs : ' + item.mots_clefs : ''}</p>
        <p>statut : <span style={item.status =='brouillon' ? "color:red;" : ""}>{item.status}</span></p>
        <p>Crée le {item.created}</p>
        <p>{item.updated != undefined ? 'Dernière Modification le ' + item.updated : ''}</p>
        <IconButton class="material-icons" on:click={deleteArticle(item.id)}>delete</IconButton>
        <IconButton class="material-icons" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}} >edit</IconButton>
    </div>
    {/each}
    </div>
{/if}

<h1>Créer un article :</h1>
<button on:click={() => {modalIsOpen = true;isNew = true}}>Créer un article</button>

<h1 style="margin-top: 40px;">Liste des Categories</h1>
<button on:click={() => listFiltreCategorie = []}>Retirer filtre</button>
{#each categories as item}
<div class="categories" on:click={filtreCategorie(item)}>{item}</div>
{/each}
<h1 style="margin-top: 40px;">Liste des Mots clefs</h1>
<button on:click={() => listFiltreMot = []}>Retirer filtre</button>
{#each mots_clefs as item}
<div class="mots" on:click={filtreMot(item)}>{item}</div>
{/each}
<h1 style="margin-top: 40px;">Articles Recherchés</h1>
<div class="searchData">
{#each listSearch as item}
<div class="article" style="border: 1px solid black;heigth:100px; min-width:200px;padding:10px; ">
    <p>titre : {item.title}</p>
    <p>{item.description != '' ? 'description : ' + item.description : ''}</p>
    <p>{item.category != '' ? 'categorie : ' + item.category : ''}</p>
    <p>statut : <span style={item.status =='brouillon' ? "color:red;" : ""}>{item.status}</span></p>
    <p>Crée le {item.created}</p>
    <p>{item.updated != undefined ? 'Dernière Modification le ' + item.updated : ''}</p>
    <IconButton class="material-icons" on:click={deleteArticle(item.id)}>delete</IconButton>
    <IconButton class="material-icons" on:click={() => {isNew = false; updatedArticle=item}} >edit</IconButton>
</div>
{/each}
</div>
{#if modalIsOpen == true && isNew == true}
<ModalAdd on:isClosed={closeModal} on:newArticle={createArticle}/>
{/if}
{#if modalIsOpen == true && isNew == false}
<ModalEdit updatedArticle={updatedArticle} categories={categories} on:isClosed={closeModal} on:newUpdatedArticle={updateArticle}/>
{/if}