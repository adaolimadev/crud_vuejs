<template>
  <Banner />
  <div class="main-container">
    <h1>Monte seu Burguer:</h1>
    <BurgerForm/>
  </div>
</template>

<script>
import Banner from '../components/Banner.vue';
import BurgerForm from '../components/BurguerForm.vue';

export default{
  name:"Home",
  components:{
    Banner,
    BurgerForm
  }
}
</script>
