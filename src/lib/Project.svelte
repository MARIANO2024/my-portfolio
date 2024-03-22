
<script>

export let info = {};
export let hLevel = 2;

</script>

<article>
    
    <svelte:element this={"h" + hLevel}>{ info.title }</svelte:element>
    <img src={info.image} alt={info.title} style="max-width: 100%; height: auto;">
    <div class="year_tag"> <p>{info.description}</p> c. {info.year} </div>
    
   
    
</article>



