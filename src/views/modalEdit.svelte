<script>
    let modalIsOpen = true;
    export let updatedArticle;
    export let categories;
</script>
<form class="modal modal-edit"  >
    <div class="modal-bg" ></div>
    <div class="modal-inner">
       <div class="modal-wrap">
            <div class="icon-close" on:click={() => {modalIsOpen = false;}}></div>
            <h2 class="modal-h2">Modifier une note</h2>
            <h3 class="modal-h3">Le titre:</h3>
                <span class="text">
                    <input type="text" bind:value={updatedArticle.title} required>
                </span>
            <h3 class="modal-h3">Le texte:</h3>
            <span class="text">
                <textarea name="note" placeholder="Ecrire votre note..." bind:value={updatedArticle.description} required></textarea>
            </span>
            <h3 class="modal-h3">Ajouter les mots-clefs:</h3>
            <span class="text text-small">
                <textarea name="keywords" placeholder="Ecrire les mots clefs séparés par une vigurle"  ></textarea>
            </span>
            <h3 class="modal-h3">Ajouter des catégories:</h3>
            <div class="categories" >
                {#each categories as item}
                    <input type="checkbox" name="category" value={item}>
                    <span>{item}</span>
                {/each}
            </div>
            <div class="modal-foot">
                <button class="button button-large" >Annuler</button>
                <button type="submit" class="button button-large">Ajouter</button>
            </div>
       </div>
    </div>
</form>