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
                            <div v-html="currentContest.rich_description"></div>
                		</div>
                		
                        <form class="form-horizontal padding_top_20" action="form-submit" v-on:submit.prevent="validateBeforeSubmit">
    						<div class="form-group ">
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('child_first_name')}">
    								<label for="child_first_name">Child's First Name <span class="req_star"> *</span></label>
    								<input v-model="form_data.child_first_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="child_first_name" type="text" placeholder="First Name" data-vv-delay="500" data-vv-as="child's first name">
    								<span v-show="errors.has('child_first_name')" class="form-control-feedback">{{ errors.first('child_first_name') }}</span>
    							</div>
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('child_last_name')}">
    								<label for="child_last_name">Child's Last Name <span class="req_star"> *</span></label>
    								<input v-model="form_data.child_last_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="child_last_name" type="text" placeholder="Last Name" data-vv-delay="500" data-vv-as="child's last name">
    								<span v-show="errors.has('child_last_name')" class="form-control-feedback">{{ errors.first('child_last_name') }}</span>
    							</div>
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('child_birthday')}">
    								<label for="child_birthday">Child's Year of Birth <span class="req_star"> *</span></label>
    								<input v-model="form_data.child_birthday" class="form-control" v-validate="'required|date_format:MM/DD/YYYY'" :class="{'input': true}" name="child_birthday" type="text" placeholder="mm/dd/yyyy" data-vv-delay="500" data-vv-as="birthday">
    								<span v-show="errors.has('child_birthday')" class="form-control-feedback">{{ errors.first('child_birthday') }}</span>
    							</div>
    						</div>
    						<div class="form-group ">
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('parent_first_name')}">
    								<label for="parent_first_name">Guardian's First Name <span class="req_star"> *</span></label>
    								<input v-model="form_data.parent_first_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="parent_first_name" type="text" placeholder="First Name" data-vv-delay="500" data-vv-as="guardian's first name">
    								<span v-show="errors.has('parent_first_name')" class="form-control-feedback">{{ errors.first('parent_first_name') }}</span>
    							</div>
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('parent_last_name')}">
    								<label for="parent_last_name">Guardian's Last Name <span class="req_star"> *</span></label>
    								<input v-model="form_data.parent_last_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="parent_last_name" type="text" placeholder="Last Name" data-vv-delay="500" data-vv-as="guardian's last name">
    								<span v-show="errors.has('parent_last_name')" class="form-control-feedback">{{ errors.first('parent_last_name') }}</span>
    							</div>
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('parent_email')}">
    								<label for="parent_email">Guardian's Email <span class="req_star"> *</span></label>
    								<input v-model="form_data.parent_email" v-validate="'required|email'" class="form-control" :class="{'input': true}" name="parent_email" type="email" placeholder="Email" data-vv-delay="500" data-vv-as="email">
    								<span v-show="errors.has('parent_email')" class="form-control-feedback">{{ errors.first('parent_email') }}</span>
    							</div>
    						</div>
    						<div class="form-group">
    							<div class="col-sm-4 col-xs-12 " :class="{'has-error': errors.has('postal_code')}">
    								<label for="postal_code">Postal Code <span class="req_star"> *</span></label>
    								<input v-model="form_data.postal_code" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="postal_code" type="text" placeholder="Postal Code" data-vv-delay="500" data-vv-as="postal code">
    								<span v-show="errors.has('postal_code')" class="form-control-feedback">{{ errors.first('postal_code') }}</span>
    							</div>
    						</div>
    						<div>
    						    <div class="col-xs-12 margin_40">
    						        <label class="checkbox">
                                        <input name="agree_terms" required type="checkbox" >
                                        Yes, I would like to receive ongoing news related to events, promotions and special announcements from Milton Monkeys Kids' Club
                                    </label>
                                    <label class="checkbox">
                                        <input name="privacy_policy" required type="checkbox" >
                                        I agree to the <a href="/pages/milton-privacy-policy" target="_blank">Privacy Policy</a>.
                                    </label>
    						    </div>
    						</div>
    						<div class="form-group">
    							<div class="col-xs-12">
    								<button class="contest_btn animated_btn" type="submit" :disabled="formSuccess">Submit</button>
    							</div>
    						</div>
    					</form>
                        
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
    define(["Vue", "vuex", "jquery", "axios", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate', "v-calendar", 'utility', 'vue-checkbox-radio'], function(Vue, Vuex, $, axios, moment, tz, VueMoment, Meta, VeeValidate, VCalendar, Utility, CheckboxRadio) {
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
                    'findContestBySlug',
                    'findRepoByName'
                ]),
            },
            methods: {
                validateBeforeSubmit() {
                    // if(this.form_data.gender == null || this.form_data.gender == undefined || this.form_data.gender.length == 0){
                    //     this.genderError = true;
                    // }
                    this.$validator.validateAll().then((result) => {
                        let errors = this.errors;
                        // if(this.form_data.agree_newsletter ) {
                        //     $.getJSON("//mobilefringe.createsend.com/t/d/s/irudui/?callback=?",
                        //     "cm-name=" + this.form_data.first_name + this.form_data.last_name +
                        //     "&cm-irudui-irudui=" + this.form_data.email +
                        //     "&cm-f-jtukjj=" + this.form_data.postal_code +
                        //         function (data) {
                        //         if (data.Status === 400) {
                        //             e.preventDefault();
                        //             console.error("Please try again later.");
                        //         } else { // 200
                        //             console.log("Newsletter submission successful.");
                        //         }
                        //     });  
                        // }
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