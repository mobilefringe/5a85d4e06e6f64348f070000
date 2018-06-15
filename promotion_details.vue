<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div v-if="currentPromo" class="promo_details_container">
                    <div class="row">
                        <div class="col-md-6 col-md-push-6">
                            <router-link :to="{ name: 'storeDetails', params: { id: currentPromo.store.slug }}">
                                <span class="promo_store_name">{{ currentPromo.store.name }}</span>
                            </router-link>
                            <h2 class="promo_name">{{ currentPromo.name }}</h2>
                            <p class="promo_date" v-if="isMultiDayEvent(currentPromo)">
                                {{ currentPromo.start_date | moment("MMM D", timezone)}} to {{ currentPromo.end_date | moment("MMM D", timezone)}}
                            </p>
                            <p class="promo_date" v-else>{{ currentPromo.start_date | moment("MMM D", timezone)}}</p>
                            <div class="promo_desc" v-html="currentPromo.rich_description"></div>
                            <div class="row hidden_phone"> 
                                <div class="col-md-12">
                                    <social-sharing v-if="currentPromo" :url="shareURL(currentPromo.slug)" :title="currentPromo.title" :description="currentPromo.body" :quote="truncate(currentPromo.body)" twitter-user="MiltonMall" :media="currentPromo.image_url" inline-template>
                                        <div class="social_share">
                                            <p class="text-uppercase" style="margin-top: 10px; margin-bottom: 10px;">Share</p>
                                            <network network="facebook">
                                                <i class="fa fa-facebook-square"></i>
                                            </network>
                                            <network network="twitter">
                                                <i class="fa fa-twitter-square"></i>
                                            </network>
                                        </div>
                                    </social-sharing>
                                </div>
                            </div>
                        </div>
                        <div class="col-md-6 col-md-pull-6">
                            <a :href="currentPromo.image_url" :data-lightbox="currentPromo.name">
                                <img :src="currentPromo.image_url" :alt="currentPromo.name"/>
                            </a>
                        </div>
                    </div>
                    <div class="row hidden-lg hidden-md visible-sm-block visible-xs-block"> 
                        <div class="col-md-12">
                            <social-sharing v-if="currentPromo" :url="shareURL(currentPromo.slug)" :title="currentPromo.title" :description="currentPromo.body" :quote="truncate(currentPromo.body)" twitter-user="PickeringTC" :media="currentPromo.image_url" inline-template>
                                <div class="social_share">
                                    <h5>Share</h5>
                                    <network network="facebook">
                                        <i class="fa fa-facebook-square"></i>
                                    </network>
                                    <network network="twitter">
                                        <i class="fa fa-twitter-square"></i>
                                    </network>
                                </div>
                            </social-sharing>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", "lightbox", "vue-lazy-load", "vue-social-sharing"], function(Vue, Vuex, moment, tz, VueMoment, Meta, Lightbox, VueLazyload, SocialSharing) {
        Vue.use(Meta);
        Vue.use(Lightbox);
        Vue.use(VueLazyload);
        Vue.component('social-sharing', SocialSharing);
        return Vue.component("promo-details-component", {
            template: template, // the variable template will be injected,
            props: ['id'],
            data: function() {
                return {
                    dataLoaded: false,
                    currentPromo: null
                }
            },
            created() {
				this.$store.dispatch("getData", "promotions").then(response => {
					this.currentPromo = this.findPromoBySlug(this.id);
					if (this.currentPromo === null || this.currentPromo === undefined) {
						this.$router.replace({ path: '/promotions' });
					}
					this.dataLoaded = true;
				}, error => {
					console.error("Could not retrieve data from server. Please check internet connection and try again.");
				});
				
			},
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findPromoBySlug'
                ])
            },
            methods: {
				isMultiDayEvent(currentEvent) {
					var timezone = this.timezone
					var start_date = moment(currentEvent.start_date).tz(timezone).format("MM-DD-YYYY")
					var end_date = moment(currentEvent.end_date).tz(timezone).format("MM-DD-YYYY")
					if (start_date === end_date) {
						return false
					} else {
						return true
					}
				},
				truncate(val_body) {
                    var truncate = _.truncate(val_body, { 'length': 99, 'separator': ' ' });
                    return truncate;
                },
				shareURL(slug) {
                    var share_url = "https://www.miltonmall.com/promotions/" + slug
                    return share_url
                }
			}
        });
    });
</script>
