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
                            <div class="contest_body" v-html="currentContest.rich_description"></div>
                		</div>
                		
                		<form id="subForm" class="js-cm-form" action="https://www.createsend.com/t/subscribeerror?description=" method="post" data-id="92D4C54F0FEC16E5ADC2B1904DE9ED1AB518424692B1918726CC1BDEC952DE8B96532C4DD58B1C389252E53F3C68E748D5F4932FCBD3A00FA5F4EE555698A95C">
                            <div class="form-group ">
    							<div class="col-sm-4 col-xs-12">
                                    <label for="fielddhkjhh">Child&#x27;s Name</label><br />
                                    <input id="fielddhkjhh" name="cm-f-dhkjhh" type="text" />
                                </div>
                                <div class="col-sm-4 col-xs-12">
                                    <label for="fieldEmail">Parent/Guardian's Email</label><br />
                                    <input id="fieldEmail" class="js-cm-email-input" name="cm-ukihdd-ukihdd" type="email" required /> 
                                </div>
                                <div class="col-sm-4 col-xs-12">
                                    <label for="fielddhkjkt">Postal Code</label><br />
                                    <input id="fielddhkjkt" name="cm-f-dhkjkt" type="text" />
                                </div>
                            </div>
                            <div class="form-group ">
    							<div class="col-xs-12">
                                    <label for="listiljuii">
                                        <input id="listiljuii" name="cm-ol-iljuii" type="checkbox" />
                                        Yes! Sign me up for Milton Mall's Newsletter
                                    </label>
                                </div>
                            </div>
                            <div class="form-group ">
    							<div class="col-xs-12">
                                    <label class="checkbox">
                                        <input name="privacy_policy" required type="checkbox" >
                                        I agree to the <a href="/pages/milton-privacy-policy" target="_blank">Privacy Policy</a>.
                                    </label>
                                </div>
                            </div>
                            <div class="form-group ">
    							<div class="col-xs-12">
                                    <button class="fill_btn js-cm-submit-button" type="submit">Submit</button> 
                                </div>
                            </div>
                        </form>
    
                		
                		
         <!--               <form class="form-horizontal padding_top_20" action="form-submit" v-on:submit.prevent="validateBeforeSubmit">-->
    						
    						
    						
    					<!--	<div class="form-group ">-->
    					<!--		<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('child_first_name')}">-->
    					<!--			<label for="child_first_name">Child's First Name <span class="req_star"> *</span></label>-->
    					<!--			<input v-model="form_data.child_first_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="child_first_name" type="text" placeholder="First Name" data-vv-delay="500" data-vv-as="child's first name">-->
    					<!--			<span v-show="errors.has('child_first_name')" class="form-control-feedback">{{ errors.first('child_first_name') }}</span>-->
    					<!--		</div>-->
         <!--                       <div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('parent_email')}">-->
    					<!--			<label for="parent_email">Parent/Guardian's Email <span class="req_star"> *</span></label>-->
    					<!--			<input v-model="form_data.parent_email" v-validate="'required|email'" class="form-control" :class="{'input': true}" name="parent_email" type="email" placeholder="Email" data-vv-delay="500" data-vv-as="email">-->
    					<!--			<span v-show="errors.has('parent_email')" class="form-control-feedback">{{ errors.first('parent_email') }}</span>-->
    					<!--		</div>-->
    					<!--		<div class="col-sm-4 col-xs-12 " :class="{'has-error': errors.has('postal_code')}">-->
    					<!--			<label for="postal_code">Postal Code <span class="req_star"> *</span></label>-->
    					<!--			<input v-model="form_data.postal_code" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="postal_code" type="text" placeholder="Postal Code" data-vv-delay="500" data-vv-as="postal code">-->
    					<!--			<span v-show="errors.has('postal_code')" class="form-control-feedback">{{ errors.first('postal_code') }}</span>-->
    					<!--		</div>-->
    					<!--	</div>-->
    					<!--	<div>-->
    					<!--	    <div class="col-xs-12 margin_40">-->
    					<!--	        <label class="checkbox">-->
         <!--                               <input v-model="form_data.newsletter" name="newsletter" required type="checkbox" >-->
         <!--                               I agree to receive emails from Milton Mall.-->
         <!--                           </label>-->
         <!--                           <label class="checkbox">-->
         <!--                               <input name="privacy_policy" required type="checkbox" >-->
         <!--                               I agree to the <a href="/pages/milton-privacy-policy" target="_blank">Privacy Policy</a>.-->
         <!--                           </label>-->
    					<!--	    </div>-->
    					<!--	</div>-->
    					<!--	<div class="form-group">-->
    					<!--		<div class="col-xs-12">-->
    								<!--<button class="contest_btn fill_btn" type="submit" :disabled="formSuccess">Submit</button>   -->
    					<!--		</div>-->
    					<!--	</div>-->
    					<!--</form>-->
                        <div id="send_contact_success" class="alert alert-success text-left" role="alert" v-show="formSuccess" style="margin-top: 20px;">
                            <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
                            <span class="sr-only">Success</span>
                            Your subscription has been confirmed. You've been added to our list and will hear from us soon.
                        </div>
                        <div id="send_contact_error" class="alert alert-danger text-left" role="alert" v-show="formError" style="margin-top: 20px;">
                            <span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
                            <span class="sr-only">Error:</span>
                            There was an error when trying to submit your request. Please try again later.
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "jquery", "axios", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate', "v-calendar", 'utility', 'vue-checkbox-radio', 'campaign-monitor'], function(Vue, Vuex, $, axios, moment, tz, VueMoment, Meta, VeeValidate, VCalendar, Utility, CheckboxRadio, CampaignMonitor) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        Vue.use(VCalendar.default);
        Vue.use(CheckboxRadio.default);
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
                validateBeforeSubmit() {
                    this.$validator.validateAll().then((result) => {
                        let errors = this.errors;
                        //format contests data for MM
                        var contest_entry = {};
                        contest_entry.json = this.form_data;
                        var vm = this;
                        host_name = this.property.mm_host.replace("http:", "");
                        var url = host_name + "/contests/" + this.currentContest.slug + "/json_entry";
                        $.ajax({
                            url: url,
                            type: "POST",
                            data: contest_entry,
                            success: function(data) {
                                vm.formSuccess = true;
                            },
                            error: function(data){
                                vm.formError = true;
                            }
                        });
                    })
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "contests")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>