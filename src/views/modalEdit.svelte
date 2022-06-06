<script>
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();
    export let updatedArticle;
    export let categories;

    function closeModal() {
        dispatch('isClosed', false);
    }

    const updateArticle = () => {
        dispatch('newUpdatedArticle', updatedArticle);
        closeModal();
    }
</script>
<form class="modal modal-edit" on:submit|preventDefault={updateArticle} >
    <div class="modal-bg" ></div>
    <div class="modal-inner">
       <div class="modal-wrap">
            <div class="icon-close" on:click={() => closeModal()}></div>
            <h2 class="modal-h2">Modifier une note</h2>
            <h3 class="modal-h3">Le titre:</h3>
                <span class="text">
                    <input type="text" bind:value={updatedArticle.title} required>
                </span>
            <h3 class="modal-h3">Le texte:</h3>
            <span class="text">
                <textarea name="note" placeholder="Ecrire votre note..." bind:value={updatedArticle.description}></textarea>
            </span>
            <h3 class="modal-h3">Modifier mots-clefs:</h3>
            <span class="text text-small">
                <input name="keywords" placeholder="Ecrire les mots clefs séparés par une vigurle" bind:value={updatedArticle.mots_clefs} />
            </span>
            <h3 class="modal-h3">Modifier catégories:</h3>
            <div class="categories" >
                <input name="categories" placeholder="Ecrire les categories séparés par une vigurle" bind:value={updatedArticle.categories} />
            </div>
            <div class="modal-foot">
                <button class="button button-large" type="reset" on:click={() => closeModal()} >Annuler</button>
                <button type="submit" class="button button-large">Ajouter</button>
            </div>
       </div>
    </div>
</form>