<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div v-if="processedJobs.length == 0">
                    <p>Sorry, there are no job postings at this time. Please check back soon.</p>
                </div>
                <div class="job_container" v-if="jobs.length >= 1" v-for="job in jobs">
                    <div class="job_image_container">
                        <img v-lazy="job.store.store_front_url_abs" :alt="job.name" />
                    </div>
                    <router-link :to="{ name: 'storeDetails', params: { id: job.store.slug }}">
                        <span class="job_store_name">{{ job.store.name }}</span>
                    </router-link>
                    <h2 class="job_name">{{ job.name }}</h2>
                    <p class="job_type">{{job.job_type}}</p>
                    <router-link :to="{ name: 'jobDetails', params: { id: job.slug }}">
                        <span class="read_more">Job Details</span>
                    </router-link>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-lazy-load"], function(Vue, Vuex, moment, tz, VueMoment, VueLazyload) {
        Vue.use(VueLazyload);
        return Vue.component("jobs-component", {
            template: template, // the variable template will be injected
            data: function () {
                return {
                    dataLoaded: false
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
                    'processedJobs'
                ]),
                jobs() {
                    var vm = this;
                    var job_list = this.processedJobs;
                    var temp_job = [];
                    _.forEach(job_list, function(value, key) {
                        today = moment().tz(vm.timezone);
                        webDate = moment(value.show_on_web_date).tz(vm.timezone);
                        if (today >= webDate) {
                            value.description_short = _.truncate(value.description, { 'length': 150 });
                            
                            if (value.store  && _.includes(value.store.store_front_url_abs, 'missing')) {
                                value.store.no_store_logo = true;
                            } else if (!value.store) {
                                value.store = {};
                                value.store.store_front_url_abs = vm.property.default_logo_url;
                            }
                            temp_job.push(value);
                        }
                    });

                    return temp_job;
                },
            },
            methods: {
                loadData: async function () {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "jobs")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>