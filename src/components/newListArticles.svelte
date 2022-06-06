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

        event.detail.id = Date.now();
        event.detail.created = new Date().toLocaleDateString("fr");
        event.detail.lock = false;
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
        const productIndex = listArticles.findIndex(p => p.id === event.detail.id);
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

    const lockArticle = (id) => {
        const productIndex = listArticles.findIndex(p => p.id === id);
        listArticles[productIndex].lock= !listArticles[productIndex].lock ;
    }

    const searchArticle = () => {
        listFiltreMot = [];
        listFiltreCategorie = [];
        listSearch = listArticles.filter((item) => item.title.includes(inputSearch) || item.description.includes(inputSearch));
        console.log(listSearch)
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

<nav>
    <ul>
        <li>
            <ul class="nav-links">

                <button on:click={() => listFiltreCategorie = []}>Retirer filtre</button>
                {#each categories as item}
                <div class="categories" on:click={filtreCategorie(item)}>{item}</div>
                {/each}

            </ul>
        </li>
        <li>
            <ul class="nav-links">
                <li>Notes</li>
                <li>Vérouillé</li>
            </ul>
        </li>
        <li>
            <ul class="nav-links">
                <button on:click={() => listFiltreMot = []}>Retirer filtre</button>
                {#each mots_clefs as item}
                <div class="mots" on:click={(filtreMot(item))}>{item}</div>
                {/each}

            </ul>
        </li>
    </ul>
</nav>
<header>
    <div class="header-contain">
        <span class="search-input">
            <form on:submit|preventDefault={searchArticle}>
            <input type="text" name="" bind:value={inputSearch} >
            <img src="./img/search.svg" alt="search">
            <button type="submit">Search</button>
            </form>
         </span>
     </div>
 </header>

<main>
    <ul class="notes">
                          
{#if listFiltreCategorie.length == 0 && listFiltreMot.length == 0 && listSearch.length == 0 }
    {#if listArticles.length == 0}
    <p>Liste vide, veuillez créer un article</p>
    {/if}

    {#each listArticles as item}

        <li class="note" id="{item.id}">
            <h2>{item.title}</h2>
            <p>{item.description}</p>
            <p>{item.categories ? item.categories : ""}</p>
            <div class="note-foot">
                {#if item.mots_clefs}
                    <ul class="keywords">
                        {#each item.mots_clefs as keyword}
                            <li>{keyword}</li>
                        {/each}
                    </ul>
                {/if}
                <div class="buttons">
                    <button class="button lock" on:click={lockArticle(item.id)}>
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 30 30" ><path d="M 15 2 C 11.145666 2 8 5.1456661 8 9 L 8 11 L 6 11 C 4.895 11 4 11.895 4 13 L 4 25 C 4 26.105 4.895 27 6 27 L 24 27 C 25.105 27 26 26.105 26 25 L 26 13 C 26 11.895 25.105 11 24 11 L 22 11 L 22 9 C 22 5.2715823 19.036581 2.2685653 15.355469 2.0722656 A 1.0001 1.0001 0 0 0 15 2 z M 15 4 C 17.773666 4 20 6.2263339 20 9 L 20 11 L 10 11 L 10 9 C 10 6.2263339 12.226334 4 15 4 z"/></svg>                            
                    </button>
                    {#if item.lock == false}
                    <button class="button edit" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}}>
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 24 24" ><path d="M 18.414062 2 C 18.158188 2 17.902031 2.0974687 17.707031 2.2929688 L 16 4 L 20 8 L 21.707031 6.2929688 C 22.098031 5.9019687 22.098031 5.2689063 21.707031 4.8789062 L 19.121094 2.2929688 C 18.925594 2.0974687 18.669937 2 18.414062 2 z M 14.5 5.5 L 3 17 L 3 21 L 7 21 L 18.5 9.5 L 14.5 5.5 z"/></svg>
                    </button>
                    <button class="button delete" on:click={deleteArticle(item.id)}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M 10.806641 2 C 10.289641 2 9.7956875 2.2043125 9.4296875 2.5703125 L 9 3 L 4 3 A 1.0001 1.0001 0 1 0 4 5 L 20 5 A 1.0001 1.0001 0 1 0 20 3 L 15 3 L 14.570312 2.5703125 C 14.205312 2.2043125 13.710359 2 13.193359 2 L 10.806641 2 z M 4.3652344 7 L 5.8925781 20.263672 C 6.0245781 21.253672 6.877 22 7.875 22 L 16.123047 22 C 17.121047 22 17.974422 21.254859 18.107422 20.255859 L 19.634766 7 L 4.3652344 7 z"/></svg>                            
                    </button>
                    {/if}
                </div>
            </div>
        </li>
    {/each}
{:else if listFiltreMot.length > 0}
    {#each listFiltreMot as item}

        <li class="note" id="{item.id}">
            <h2>{item.title}</h2>
            <p>{item.description}</p>
            <p>{item.categories ? item.categories : ""}</p>
            <div class="note-foot">
                {#if item.mots_clefs}
                    <ul class="keywords">
                        {#each item.mots_clefs as keyword}
                            <li>{keyword}</li>
                        {/each}
                    </ul>
                {/if}
                <div class="buttons">
                    <button class="button lock">
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 30 30" ><path d="M 15 2 C 11.145666 2 8 5.1456661 8 9 L 8 11 L 6 11 C 4.895 11 4 11.895 4 13 L 4 25 C 4 26.105 4.895 27 6 27 L 24 27 C 25.105 27 26 26.105 26 25 L 26 13 C 26 11.895 25.105 11 24 11 L 22 11 L 22 9 C 22 5.2715823 19.036581 2.2685653 15.355469 2.0722656 A 1.0001 1.0001 0 0 0 15 2 z M 15 4 C 17.773666 4 20 6.2263339 20 9 L 20 11 L 10 11 L 10 9 C 10 6.2263339 12.226334 4 15 4 z"/></svg>                            
                    </button>
                    <button class="button edit" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}}>
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 24 24" ><path d="M 18.414062 2 C 18.158188 2 17.902031 2.0974687 17.707031 2.2929688 L 16 4 L 20 8 L 21.707031 6.2929688 C 22.098031 5.9019687 22.098031 5.2689063 21.707031 4.8789062 L 19.121094 2.2929688 C 18.925594 2.0974687 18.669937 2 18.414062 2 z M 14.5 5.5 L 3 17 L 3 21 L 7 21 L 18.5 9.5 L 14.5 5.5 z"/></svg>
                    </button>
                    <button class="button delete" on:click={deleteArticle(item.id)}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M 10.806641 2 C 10.289641 2 9.7956875 2.2043125 9.4296875 2.5703125 L 9 3 L 4 3 A 1.0001 1.0001 0 1 0 4 5 L 20 5 A 1.0001 1.0001 0 1 0 20 3 L 15 3 L 14.570312 2.5703125 C 14.205312 2.2043125 13.710359 2 13.193359 2 L 10.806641 2 z M 4.3652344 7 L 5.8925781 20.263672 C 6.0245781 21.253672 6.877 22 7.875 22 L 16.123047 22 C 17.121047 22 17.974422 21.254859 18.107422 20.255859 L 19.634766 7 L 4.3652344 7 z"/></svg>                            
                    </button>
                </div>
            </div>
        </li>
    {/each}
{:else if listSearch.length > 0}
{#each listSearch as item}
        <li class="note" id="{item.id}">
            <h2>{item.title}</h2>
            <p>{item.description}</p>
            <p>{item.categories ? item.categories : ""}</p>
            <div class="note-foot">
                {#if item.mots_clefs}
                    <ul class="keywords">
                        {#each item.mots_clefs as keyword}
                            <li>{keyword}</li>
                        {/each}
                    </ul>
                {/if}
                <div class="buttons">
                    <button class="button lock">
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 30 30" ><path d="M 15 2 C 11.145666 2 8 5.1456661 8 9 L 8 11 L 6 11 C 4.895 11 4 11.895 4 13 L 4 25 C 4 26.105 4.895 27 6 27 L 24 27 C 25.105 27 26 26.105 26 25 L 26 13 C 26 11.895 25.105 11 24 11 L 22 11 L 22 9 C 22 5.2715823 19.036581 2.2685653 15.355469 2.0722656 A 1.0001 1.0001 0 0 0 15 2 z M 15 4 C 17.773666 4 20 6.2263339 20 9 L 20 11 L 10 11 L 10 9 C 10 6.2263339 12.226334 4 15 4 z"/></svg>                            
                    </button>
                    <button class="button edit" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}}>
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 24 24" ><path d="M 18.414062 2 C 18.158188 2 17.902031 2.0974687 17.707031 2.2929688 L 16 4 L 20 8 L 21.707031 6.2929688 C 22.098031 5.9019687 22.098031 5.2689063 21.707031 4.8789062 L 19.121094 2.2929688 C 18.925594 2.0974687 18.669937 2 18.414062 2 z M 14.5 5.5 L 3 17 L 3 21 L 7 21 L 18.5 9.5 L 14.5 5.5 z"/></svg>
                    </button>
                    <button class="button delete" on:click={deleteArticle(item.id)}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M 10.806641 2 C 10.289641 2 9.7956875 2.2043125 9.4296875 2.5703125 L 9 3 L 4 3 A 1.0001 1.0001 0 1 0 4 5 L 20 5 A 1.0001 1.0001 0 1 0 20 3 L 15 3 L 14.570312 2.5703125 C 14.205312 2.2043125 13.710359 2 13.193359 2 L 10.806641 2 z M 4.3652344 7 L 5.8925781 20.263672 C 6.0245781 21.253672 6.877 22 7.875 22 L 16.123047 22 C 17.121047 22 17.974422 21.254859 18.107422 20.255859 L 19.634766 7 L 4.3652344 7 z"/></svg>                            
                    </button>
                </div>
            </div>
        </li>
    {/each}
{:else}
{#each listFiltreCategorie as item}

        <li class="note" id="{item.id}">
            <h2>{item.title}</h2>
            <p>{item.description}</p>
            <p>{item.categories ? item.categories : ""}</p>
            <div class="note-foot">
                {#if item.mots_clefs}
                    <ul class="keywords">
                        {#each item.mots_clefs as keyword}
                            <li>{keyword}</li>
                        {/each}
                    </ul>
                {/if}
                <div class="buttons">
                    <button class="button lock">
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 30 30" ><path d="M 15 2 C 11.145666 2 8 5.1456661 8 9 L 8 11 L 6 11 C 4.895 11 4 11.895 4 13 L 4 25 C 4 26.105 4.895 27 6 27 L 24 27 C 25.105 27 26 26.105 26 25 L 26 13 C 26 11.895 25.105 11 24 11 L 22 11 L 22 9 C 22 5.2715823 19.036581 2.2685653 15.355469 2.0722656 A 1.0001 1.0001 0 0 0 15 2 z M 15 4 C 17.773666 4 20 6.2263339 20 9 L 20 11 L 10 11 L 10 9 C 10 6.2263339 12.226334 4 15 4 z"/></svg>                            
                    </button>
                    <button class="button edit" on:click={() => {isNew = false; modalIsOpen = true;updatedArticle=item}}>
                        <svg xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 24 24" ><path d="M 18.414062 2 C 18.158188 2 17.902031 2.0974687 17.707031 2.2929688 L 16 4 L 20 8 L 21.707031 6.2929688 C 22.098031 5.9019687 22.098031 5.2689063 21.707031 4.8789062 L 19.121094 2.2929688 C 18.925594 2.0974687 18.669937 2 18.414062 2 z M 14.5 5.5 L 3 17 L 3 21 L 7 21 L 18.5 9.5 L 14.5 5.5 z"/></svg>
                    </button>
                    <button class="button delete" on:click={deleteArticle(item.id)}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M 10.806641 2 C 10.289641 2 9.7956875 2.2043125 9.4296875 2.5703125 L 9 3 L 4 3 A 1.0001 1.0001 0 1 0 4 5 L 20 5 A 1.0001 1.0001 0 1 0 20 3 L 15 3 L 14.570312 2.5703125 C 14.205312 2.2043125 13.710359 2 13.193359 2 L 10.806641 2 z M 4.3652344 7 L 5.8925781 20.263672 C 6.0245781 21.253672 6.877 22 7.875 22 L 16.123047 22 C 17.121047 22 17.974422 21.254859 18.107422 20.255859 L 19.634766 7 L 4.3652344 7 z"/></svg>                            
                    </button>
                </div>
            </div>
        </li>
    {/each}

{/if}
</ul>
    <div class="more" on:click={() => {modalIsOpen = true;isNew = true}}><img src="./img/add.svg" alt="plus" ></div>
</main> 


{#if modalIsOpen == true && isNew == true}
<ModalAdd on:isClosed={closeModal} on:newArticle={createArticle}/>
{/if}
{#if modalIsOpen == true && isNew == false}
<ModalEdit updatedArticle={updatedArticle} categories={categories} on:isClosed={closeModal} on:newUpdatedArticle={updateArticle}/>
{/if}