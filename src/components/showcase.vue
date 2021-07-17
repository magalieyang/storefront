<template>
  <div class="showcase">

    <div class="header">
      <h1 v-show="!displayMenu">Nouveautés
        <span v-for="index in 4" :key="index">
              <span v-for="(feature, featureId) in filters[index - 1]" :key="featureId"> {{ feature }}<span
                  v-if="filters[index - 1].length > 1 && featureId !== (filters[index - 1].length - 1)">, </span>
              </span></span>
        ({{ result.total || 0 }})
      </h1>
      <MenuMobileBtns v-on:btnCloseMenu="displayMenuMobile" :display="displayMenu"  />
    </div>
    <Menu v-if="winnerWidth <= 1200"  v-on:btnCloseMenu="displayMenuMobile"
          v-on:filteringMenu="filtering" :results="result" :display="displayMenu" />

    <div class="wrapper">
      <Menu v-if="winnerWidth > 1200" v-on:filteringMenu="filtering"
            v-on:btnCloseMenu="displayMenuMobile"
            :results="result" :display="displayMenu"  />
      <div class="container">
        <Product v-for="products in filteredList" :key="products.id"
                 :product="products"/>
      </div>
    </div>
  </div>
</template>

<script>
import Menu from '@/components/common/menu';
import productJson from "../../../produits.json";
import Product from "@/components/product";
import MenuMobileBtns from '@/components/common/menuMobileBtns';


export default {
  name: 'Showcase',
  components: {
    Product,
    Menu,
    MenuMobileBtns
  },
  data() {
    return {
      winnerWidth: window.innerWidth,
      filters: [],
      items: productJson,
      result: {total: 0, submenu: [0, 0, 0, 0]},
      displayMenu : false,
    }
  },
  computed: {
    /**
     * New list of product if filters
     */
    filteredList: function () {
      var _this = this;
      const newFilters = {
        sex: _this.filters[0],
        price: _this.filters[1],
        colors: _this.filters[2],
        sport: _this.filters[3]
      };
      var newList = [];

      _this.result.submenu = this.setAllResultToZero(_this.result.submenu);

      if ((newFilters.sex === undefined && newFilters.price === undefined &&
          newFilters.colors === undefined && newFilters.sport === undefined) ||
          (newFilters.sex.length <= 0 && newFilters.price.length <= 0 &&
              newFilters.colors.length <= 0 && newFilters.sport.length <= 0) ) {

        _this.result.total = this.countTotalElement(_this.items);
        return this.countColors(this.items);
      }
      newList = this.newListFiltered(newFilters, _this.items)

      _this.result.total = newList.length;
      return this.countColors(newList);
    },
  },

  mounted() {
    this.$nextTick(() => {
      window.addEventListener('resize', this.onResize);
    })
  },

  beforeDestroy() {
    window.removeEventListener('resize', this.onResize);
  },

  methods: {

    /**
     * Listen window resize
     */
    onResize() {
      this.winnerWidth = window.innerWidth
    },

    /**
     * Create a new list with products based on the filters
     * */
    newListFiltered: function (newFilters, items) {
      var newList = [];
      var available = false;

      for (var index = 0; index < items.length; index++) {
        if (items[index].article !== '') {
          if (newFilters.sex !== undefined && this.isIncluded(newFilters.sex, items[index].sexe, 0))
            available = true;
          if (newFilters.price !== undefined && this.priceRange(newFilters.price, items[index].prix) === true)
            available = true;
          if (newFilters.colors !== undefined && this.commonColor(newFilters.colors, items[index].couleur))
            available = true;
          if (newFilters.sport !== undefined && this.isIncluded(newFilters.sport, items[index].sport, 3))
            available = true;

          if (available === true) {
            newList.push(items[index]);
            available = 0;
          }
        }

      }
      return newList;
    },

    /**
     * Reset all result number of submenu
     * */
    setAllResultToZero: (submenuTotal) => {
      var newSubmenuTotal = submenuTotal;
      for (var i = 0; i < newSubmenuTotal.length; ++i) {
        newSubmenuTotal[i] = 0;
      }
      return newSubmenuTotal;
    },

    /**
     * At load, display the total number of products
     */
    countTotalElement: (item) => {
      var result = 0;
      if (item.length > 0 ) {
        for (var i = 0; i < item.length; i++) {
          if (item[i] && item[i].article !== '') {
            result += 1;
          }
        }
      }
      return result;
    },

    /**
     * Filter by gender or sport
     * */
    isIncluded: function (filters, list, indexResult) {
      if (!filters || !list) {
        return false;
      } else if (filters.length > 0 && list.length > 0 && filters.includes(list)) {
        this.result.submenu[indexResult] += 1;
        return true;
      }
      return false
    },

    /**
     * Filter by price
     */
    priceRange: function (filters, list) {
      if (!filters || !list) {
        return false;
      }
      var priceRange = [];
      var inRange = false;

      var needlePrice = parseFloat(list.replace(' €', '').replace(',', '.'));
      for (var range = 0; range < filters.length; range++) {

        switch (filters[range]) {
          case 'Moins de €50':
            priceRange = [0, 50];
            break;
          case '€50-€100':
            priceRange = [50, 100];
            break;
          case '€100-€150':
            priceRange = [100, 150];
            break;
          case 'Plus de €150':
            priceRange = [150];
            break;
          default:
            [];
        }
        if ((!(priceRange[1]) && needlePrice >= priceRange[0]) ||
            (needlePrice <= priceRange[1] && needlePrice >= priceRange[0])) {
          inRange = true;
          this.result.submenu[1] += 1;
          break;
        }
      }
      return inRange;
    },

    /**
     * Filter by colors
     */
    commonColor: function (filters, list) {

      var isCommon;
      isCommon =  filters.some(function (el) {
        return list.indexOf(el) > -1;
      });

      if(isCommon){
        this.result.submenu[2] += 1;
      }
      return isCommon;
    },

    /**
     * Add the number of available colors
     * @param list
     * @returns {*}
     */
    countColors: (list) => {
      list.forEach((item) => {
        item.couleurNbr = item.couleur.split(',').length;
      })
      return list;
    },

    /**
     * Catch filters from menu
     * @param filter
     */
    filtering(filter) {
      this.filters = filter;
    },

    /**
     * Catch close/open button from menu
     * @param btn
     */
    displayMenuMobile(btn) {
      this.displayMenu = btn;
    }

  },

}
</script>