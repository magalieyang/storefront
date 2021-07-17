<template>
  <div class="menu-btns">
    <button class="close" @click="closeMenu" v-show="checkMenu">✕</button>
    <button class="hamburger" @click="closeMenu" v-show="!checkMenu">
      <div></div>
      <div></div>
    </button>
  </div>

</template>

<script>
export default {
  name: 'MenuMobileBtns',
  props: ['display',],
  data() {
    return {
      menuDisplayed: this.display,
    }
  },
  computed:{
    checkMenu(){
      var _this = this;
      if(_this.menuDisplayed !== this.display){
        _this.menuDisplayed = this.display;
      }
      if(_this.menuDisplayed === true){
        return true;
      }else{
        return false;
      }
    }
  },
  methods: {

    /**
     * Listen close/open menu button
     */
    closeMenu() {
      this.menuDisplayed = !this.menuDisplayed;
      this.$emit('btnCloseMenu', this.menuDisplayed)
    },
  }
}
</script>