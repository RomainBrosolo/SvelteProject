<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/icon?family=Material+Icons"
/>
<script>
    import IconButton from '@smui/icon-button';
    let listArticles = [];
    let categories = [];
    let isNew = true;
    let updatedArticle = {};
    let inputSearch = '';
    let listSearch = [];
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
    }
    
    $: localStorage.setItem("articles", JSON.stringify(listArticles))
    $: localStorage.setItem("categories", JSON.stringify(categories))
    
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

    const updateArticle = () => {
        const productIndex = listArticles.findIndex(p => p.id === updatedArticle.id);
        updatedArticle.description == '' || !updatedArticle.description.trim() ? updatedArticle.status = 'brouillon' : updatedArticle.status = 'archive';
        updatedArticle.updated = new Date().toLocaleDateString("fr");
        listArticles[productIndex]= updatedArticle;
        isNew = true;
        updatedArticle = {};
    }

    const deleteArticle = (id) => {
        listArticles = listArticles.filter(item => item.id !== id);
        categories = categories.filter((item) => listArticles.find((x) => x.category == item))
    }

    const searchArticle = () => {
        listSearch = listArticles.filter((item) => item.title.includes(inputSearch) || item.description.includes(inputSearch));
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
<div class="list" style="display:flex; gap:20px; overflow-x: auto">
{#each listArticles as item}
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

{#if isNew == true}
<h1>Créer un article :</h1>
<form on:submit|preventDefault={createArticle}>
    <input type="text" placeholder="Titre" bind:value={article.title} required>
    <input type="text" placeholder="Description" bind:value={article.description}>
    <input type="text" placeholder="Catégorie" bind:value={article.category}>
    <button type="submit">Create</button>
</form>
{:else}
<h1>Modifier un article :</h1>
<form on:submit|preventDefault={updateArticle}>
    <input type="text" placeholder="Titre" bind:value={updatedArticle.title} required>
    <input type="text" placeholder="Description" bind:value={updatedArticle.description}>
    <input type="text" placeholder="Catégorie" bind:value={updatedArticle.category}>
    <button type="submit">Save</button>
    <button type="reset" on:click={() => isNew = true}>Cancel</button>
</form>
{/if}
<h1 style="margin-top: 40px;">Liste des Categories</h1>
{#each categories as item}
<div class="categories" on:click={alert("category clicked = "+item)}>{item}</div>
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
