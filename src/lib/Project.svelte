
<script>export let info = {};
export let hLevel = 2;
</script>
<!-- let p = info; -->
<article>
    <!-- <h2>{p.title}</h2>
    <img src={p.image} alt="">
    <p>{p.description}</p> -->
    <!-- <h2>{info.title}</h2> -->
    <svelte:element this={"h" + hLevel}>{ info.title }</svelte:element>
    <img src={info.image} alt={info.title}>
    <p>{info.description}</p>
</article>


