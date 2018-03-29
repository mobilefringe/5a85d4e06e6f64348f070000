<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div class="row">
                    <div class="col-md-12">
                        <div v-if="currentContest" class="margin_60">
                            <img class="img_width margin_20" :src="currentContest.image_url" :alt="currentContest.name">
                            <div class="kidsclub_body" v-html="currentContest.rich_description"></div>
                		</div>
                		<form id="subForm" class="form-horizontal js-cm-form" action="https://www.createsend.com/t/subscribeerror?description=" method="post" data-id="92D4C54F0FEC16E5ADC2B1904DE9ED1AB518424692B1918726CC1BDEC952DE8B96532C4DD58B1C389252E53F3C68E748D5F4932FCBD3A00FA5F4EE555698A95C">
                            <div class="form-group kids">
    							<div class="col-sm-4 col-xs-12">
                                    <label for="fielddhkjhh">Child&#x27;s Name *</label><br />
                                    <input id="fielddhkjhh" class="form-control" name="cm-f-dhkjhh" type="text" />
                                </div>
                                <div class="col-sm-4 col-xs-12">
                                    <label for="fieldEmail">Parent/Guardian's Email *</label><br />
                                    <input id="fieldEmail" class="form-control js-cm-email-input" name="cm-ukihdd-ukihdd" type="email" required />
                                </div>
                                <div class="col-sm-4 col-xs-12">
                                    <label for="fielddhkjkt">Postal Code *</label><br />
                                    <input id="fielddhkjkt" class="form-control" name="cm-f-dhkjkt" type="text" />
                                </div>
                            </div>
                            <div class="margin_30 form-inline">
                                <label class="checkbox" for="listiljuii">
                                    <input id="listiljuii" name="cm-ol-iljuii" type="checkbox" required />
                                    I agree to receive emails form Milton Mall and Milton Monkeys’ Kids’ Club
                                </label>
                            </div>
                            <div class="form-group ">
    							<div class="col-xs-12">
                                    <button class="fill_btn js-cm-submit-button" type="submit">Submit</button> 
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "jquery", "axios", "vue-meta", "campaign-monitor"], function(Vue, Vuex, $, axios, Meta, CampaignMonitor) {
        Vue.use(Meta);
        return Vue.component("kids-club-component", {
            template: template, // the variable template will be injected
            props:['locale'],
            data: function() {
                return {
                    dataLoaded: false,
                    form_data: {},
                    formSuccess: false,
                    formError: false,
                    currentContest: null
                }
            },
            created() {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.currentContest = this.findContestBySlug('milton-kids-club');
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findContestBySlug'
                ]),
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "contests")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                validateBeforeSubmit(form) {
                    this.$validator.validateAll().then((result) => {
                        if (result) {
                            let errors = this.errors;
                            if(errors.length > 0) {
                                console.log("Error");
                            } else {
                                console.log("No Error");
                                form.target.submit();
                            }
                        }
                    })
                }
            }
        });
    });
</script>