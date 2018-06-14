<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div v-if="promos.length == 0">
                    <p>Sorry, there are no event postings at this time. Please check back soon.</p>
                </div>
                <div class="promos_container" v-if="promos.length >= 1">
                    <div class="promo_container" v-for="promo in promos">
                        <div class="promo_img_container">
                            <a :href="promo.image_url" :data-lightbox="promo.name">
                                <img v-lazy="promo.image_url" :alt="promo.name" />
                            </a>
                        </div>
                        <router-link :to="{ name: 'storeDetails', params: { id: promo.store.slug }}">
                            <span class="promo_store_name">{{ promo.store.name }}</span>
                        </router-link>
                        <h3 class="promo_name">{{ promo.name }}</h3>
                        <p class="promo_date" v-if="isMultiDayEvent(promo)">
                            {{ promo.start_date | moment("MMM D", timezone)}} to {{ promo.end_date | moment("MMM D", timezone)}}
                        </p>
                        <p class="promo_date" v-else>{{ promo.start_date | moment("MMM D", timezone)}}</p>
                        <router-link :to="{ name: 'promotionDetails', params: { id: promo.slug }}">
                            <span class="promo_read_more">Read More</span>
                        </router-link>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", "lightbox", "vue-lazy-load"], function(Vue, Vuex, moment, tz, VueMoment, Meta, Lightbox, VueLazyload) {
        Vue.use(Meta);
        Vue.use(Lightbox);
        Vue.use(VueLazyload);
        return Vue.component("promos-component", {
            template: template, // the variable template will be injected
            data: function () {
                return {
                    dataLoaded: false,
                }
            },
            created() {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedPromos'
                ]),
                promos: function promos() {
                    var vm = this;
                    var showPromos = [];
                    _.forEach(this.processedPromos, function(value, key) {
                        var today = moment.tz(this.timezone).format();
                        var showOnWebDate = moment.tz(value.show_on_web_date, this.timezone).format();
                        if (today >= showOnWebDate) {
                            if (value.store != null && value.store != undefined && _.includes(value.store.image_url, 'missing')) {
                                value.store.image_url = "https://placehold.it/400x400";
                            }
                            if (_.includes(value.image_url, 'missing')) {
                                value.image_url = "https://placehold.it/400x400";
                            }
                            value.name = _.truncate(value.name, { 'length': 100, 'separator': ' ' });
                            showPromos.push(value);
                        }
                    });
                    var sortedPromos = _.orderBy(showPromos, [function(o) { return o.end_date; }]);
                    return sortedPromos;
                }
            },
            methods: {
                loadData: async function () {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "promotions")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                isMultiDayEvent(promo) {
                    var timezone = this.timezone
                    var start_date = moment(promo.start_date).tz(timezone).format("MM-DD-YYYY")
                    var end_date = moment(promo.end_date).tz(timezone).format("MM-DD-YYYY")
                    if (start_date === end_date) {
                        return false
                    } else {
                        return true
                    }
                }
            }
        });
    });
</script>