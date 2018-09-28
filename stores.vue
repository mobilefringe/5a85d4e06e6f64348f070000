<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div class="">
                    <div class="row store_nav">
                        <div class="col-md-3 col-xs-12">
                            <a class="store_nav_link active_store_nav" href="#">Directory</a>
                        </div>
                        <div class="col-md-3 col-xs-12">
                            <a class="store_nav_link" href="/map">Centre Map</a>
                        </div>
                        <div class="col-md-3 col-xs-12">
                            <span>Sort By: </span>
                            <a class="store_nav_link" v-on:click="changeMode('alphabetical')">Alphabetical</a>
                        </div>
                        <div class="col-md-3 col-xs-12">
                            <v-select v-if="dropDownCats" v-model="selectedCat" :options="dropDownCats" :searchable="false" :on-change="filteredByCategory" class="category-select" :placeholder="$t('stores_page.sort_by_cats')" id="selectByCat"></v-select>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-md-12" :class="{ store_col_count: breakIntoCol }">
                            <div class="store_index_container" v-for="(stores, index) in filteredStores" v-if="listMode === 'alphabetical'">
                                <div class="list_header">
                                    <div class="store_initial_container">
                                        <div class="store_initial_box">
                                            <span>{{index}}</span>    
                                        </div>
                                    </div>
                                </div>
                                <div class="store_list_name" v-for="store in stores">
                                    <router-link :to="{ name: 'storeDetails', params: { id: store.slug }}">
                                        {{store.name}}
                                    </router-link>
                                    <span v-if="store.is_new_store" class="pull-right new_store"><span class="hidden_phone">NEW</span><span class="visible-xs-block"><i class="fa fa-star"></i></span></span>
                                    <span v-if="store.is_coming_soon_store" class="pull-right coming_soon_store"><span class="hidden_phone">COMING SOON</span><span class="visible-xs-block"><i class="fa fa-clock"></i></span></span>
                                    <span v-if="store.promotions != null" class="promo_exist pull-right"><span class="hidden_phone">PROMOTION</span><span class="visible-xs-block"><i class="fa fa-tags"></i></span></span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <!--<div class="hidden-lg hidden-md visible-sm-block visible-xs-block">-->
                <!--    <div class="row hidden-lg hidden-md visible-sm-block visible-xs-block">-->
                <!--        <div class="col-md-12 mobile_store_select">-->
                <!--            <v-select v-if="allStores" :options="allStores" :placeholder="'Select A Store'" :searchable="false" :label="'name'" :on-change="dropPin"></v-select> -->
                <!--        </div>-->
                <!--    </div>-->
                <!--    <div class="row">-->
                <!--        <div class="col-md-12">-->
                <!--            <svg-map ref="svgRef" v-bind:svgMapUrl="getSVGurl" :regions="regions"></svg-map>-->
                <!--        </div>-->
                <!--    </div>    -->
                <!--</div>-->
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "vue-meta", "vue-select", "jquery", "Raphael", "mm_mapsvg", "mousewheel", "vue!svg-map"], function(Vue, Vuex, Meta, VueSelect, $, Raphael, mapSvg, mousewheel, SVGMapComponent) {
        Vue.use(Meta);
        Vue.component('v-select', VueSelect.VueSelect);
        return Vue.component("stores-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: false,
                    listMode: "alphabetical",
                    filteredStores: null,
                    selectedCat: "Select a Category",
                    breakIntoCol : true
                }
            },
            created(){
                this.loadData().then(response => {
                    window.Raphael = Raphael; // our mapSvg plugin is stupid and outdated. need this hack to tie Raphael to window object (global variable)
                    this.filteredStores = this.storesByAlphaIndex;
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'processedStores',
                    'processedCategories',
                    'storesByCategoryName',
                    'findCategoryByName',
                    'findCategoryById'
                ]), 
                allStores() {
                    return _.filter(this.processedStores, function(o) { return o.name != "Bentall Kennedy Store" });
                },
                storesByAlphaIndex() {
                  let stores = this.allStores;
                  let grouped = _.groupBy(stores, store => (isNaN(_.upperCase(store.name.charAt(0))) ? _.upperCase(store.name.charAt(0)) : "#"));
                  return grouped;
                },
                dropDownCats() {
                    console.log(this.processedCategories)
                    var cats = this.processedCategories
                    full_cats = [];
                    _.forEach(cats, function( val, key ) {
                        if (val.store_ids) {
                            full_cats.push(val);    
                        }
                    });
                    console.log(full_cats)
                    var cats = _.map(this.processedCategories, 'name');
                    cats.unshift('All');
                    return cats;
                },
                getSVGurl () {
                    return "https://www.mallmaverick.com" + this.property.svgmap_url;
                },
                svgMapRef() {
                    return this.$refs.svgRef;
                },
                regions () {
                    var regions = {}
                    _.forEach( this.processedStores , function( val, key ) {
                        if(val.svgmap_region != null && typeof(val.svgmap_region)  != 'undefined'){
                            obj = {};
                            obj["tooltip"] = "<p class='tooltip_name'>" + val.name + "</p>";
                            obj["attr"] = {};
                            obj["attr"]["href"] = "/stores/" + val.slug;
                            regions[val.svgmap_region] = obj;
                        }
                    });
                    return regions;
                }
            },
            methods: {
                loadData: async function () {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "categories")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                changeMode (mode) {
                    this.breakIntoCol = true;
                    this.filteredStores = this.storesByAlphaIndex;
                },
                filteredByCategory (cat_id) {
                    if(cat_id == "Select a Category" || cat_id == "All" || cat_id == null || cat_id == undefined){
                        category_id = "All";
                    } else {
                        category_id = this.findCategoryByName(cat_id).id;
                    }
                    
                    this.breakIntoCol = false;
                    
                    if(category_id == "All"){
                        this.filteredStores = this.storesByAlphaIndex;
                        this.breakIntoCol = true;
                    } else {
                        var find = this.findCategoryById;
                        var filtered = _.filter(this.allStores, function(o) {return _.indexOf(o.categories, _.toNumber(category_id)) > -1; });
                        _.forEach(filtered, function(value, i) {
                            value.currentCategory = find(category_id).name;
                        });
                        sortedCats = _.groupBy(filtered, store => store.currentCategory);
                        this.filteredStores = sortedCats;
                    }
                },
                dropPin (store) {
                    this.svgMapRef.hideMarkers();
                    this.svgMapRef.addMarker(store, '//codecloud.cdn.speedyrails.net/sites/589e308f6e6f641b9f010000/image/png/1484850466000/show_pin.png');
                    this.svgMapRef.setViewBox(store)
                }
            }
        });
    });
</script>