<script>
let listArticles = [];
if(localStorage.getItem("articles")){
    listArticles = JSON.parse(localStorage.getItem("articles"));
}

console.log('test',listArticles);
</script>
