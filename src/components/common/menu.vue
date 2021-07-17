<template>

  <div class="menu" :class="{opened : display}">
    <div v-for="(submenu, submenuId) in submenuList"
         v-bind:key="submenuId" class="submenu">
      <h3>{{ submenu.title }} <span v-show=" results.submenu[submenuId] &&  results.submenu[submenuId] >= 0">({{
          results.submenu[submenuId]
        }})</span>
        <a @click="$set(submenu, 'toggled', !submenu.toggled)"
           :class="{toggled:!submenu.toggled}" class="toggleBtn "
        >ᐳ</a>
      </h3>
      <ul v-if="!submenu.toggled" :class="{roundCheckboxes : submenu.type === 'couleur'}">
        <li v-for="(feature, i) in submenu.featureList" v-bind:key="feature.id">

          <input v-if="submenu.type === 'couleur'" :value="feature.value" type="checkbox"
                 v-bind:class="bindColor[i]" class="roundCheckbox"
                 v-model="select[submenuId]" v-on:change="emitToApp">

          <input v-else :value="feature.value" type="checkbox"
                 v-model="select[submenuId]" v-on:change="emitToApp">

          <label :for='feature.value'>{{ feature.value }}</label>
        </li>
      </ul>
    </div>


    <div class="menu-btns-validate" v-if="display">
      <button @click="hideBtn" v-show="results.total > 0 && displayCleanBtn" v-on:click="clearAllFilters">
        Effacer ({{ results.total }})
      </button>
      <button v-on:click="closeMenu">Appliquer</button>
    </div>

  </div>
</template>

<script>


export default {
  name: 'Menu',
  props: [
    'results',
    'display',
  ],
  data() {
    return {

      sendBtn : this.display,
      bindColor: ['black', 'red', 'white', 'yellow', 'green', 'blue', 'pink', 'grey'],
      displayMenu: [false, false, false, false],
      select: [
        [], [], [], []
      ],
      submenuList: [
        {
          title: 'Sexe',
          type: 'sexe',
          featureList: [{'value': 'Homme'}, {'value': 'Femme'}, {'value': 'Mixte'}]
        },
        {
          title: 'Rechercher par prix',
          type: 'prix',
          featureList:
              [
                {'value': 'Moins de €50'},
                {'value': '€50-€100'},
                {'value': '€100-€150'},
                {'value': 'Plus de €150'}
              ],
        },
        {
          title: 'Couleur',
          type: 'couleur',
          featureList: [{'value': 'noir'}, {'value': 'rouge'}, {'value': 'blanc'},
            {'value': 'jaune'}, {'value': 'vert'}, {'value': 'bleu'}, {'value': 'rose'}, {'value': 'gris'}]
        },
        {
          title: 'Sports',
          type: 'sport',
          featureList: [{'value': 'Football'}, {'value': 'Basket'}, {'value': 'Running'}]
        }
      ],

      displayCleanBtn: false,
    }
  },

  methods: {

    emitToApp() {
      var _this = this;
      var isClean = false;

      for(var i = 0; i < _this.select.length; i++){
        if (this.select[i].length > 0) {
          isClean = true;
        }
      }
      _this.displayCleanBtn = isClean;
      this.$emit('filteringMenu', this.select);
    },

    /**
     * Reset filters
     */
    clearAllFilters: function () {
      var _this = this;
      for(var i = 0; i < _this.select.length; i++){
        if (_this.select[i].length > 0) {
          _this.select[i].splice(0, _this.select[i].length);
        }
      }
      this.emitToApp();
    },

    hideBtn() {
      this.displayCleanBtn = false;
    },
    closeMenu() {
      this.sendBtn = !this.sendBtn;
      this.$emit('btnCloseMenu', this.sendBtn)
    },

  },

}


</script>


