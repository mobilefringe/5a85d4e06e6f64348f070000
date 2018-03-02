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
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('birthday')}">
    								<label for="birthday">Child's Year of Birth <span class="req_star"> *</span></label>
    								<input v-model="form_data.birthday" class="form-control" v-validate="'required|date_format:MM/DD/YYYY'" :class="{'input': true}" name="birthday" type="text" placeholder="mm/dd/yyyy" data-vv-delay="500" data-vv-as="birthday">
    								<span v-show="errors.has('birthday')" class="form-control-feedback">{{ errors.first('birthday') }}</span>
    							</div>
    							<!--<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('gender')}"  style="padding-top: 20px;">-->
    							<!--	<label class="label" for="gender">Gender <span class="req_star"> *</span></label>-->
    							<!--	<radio name="gender" value="male" v-model="form_data.gender">Male</radio>-->
           <!--                         <radio name="gender" value="female" v-model="form_data.gender">Female</radio>-->
           <!--                         <span v-if="genderError" class="form-control-feedback">Please choosed a gender</span>-->
    							<!--</div>-->
    						</div>
    						<div class="form-group ">
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('first_name')}">
    								<label for="first_name">Guardian's First Name <span class="req_star"> *</span></label>
    								<input v-model="form_data.first_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="first_name" type="text" placeholder="First Name" data-vv-delay="500" data-vv-as="guardian's first name">
    								<span v-show="errors.has('first_name')" class="form-control-feedback">{{ errors.first('first_name') }}</span>
    							</div>
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('last_name')}">
    								<label for="last_name">Guardian's Last Name <span class="req_star"> *</span></label>
    								<input v-model="form_data.last_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="last_name" type="text" placeholder="Last Name" data-vv-delay="500" data-vv-as="guardian's last name">
    								<span v-show="errors.has('last_name')" class="form-control-feedback">{{ errors.first('last_name') }}</span>
    							</div>
    							<div class="col-sm-4 col-xs-12" :class="{'has-error': errors.has('email')}">
    								<label for="email">Guardian's Email <span class="req_star"> *</span></label>
    								<input v-model="form_data.email" v-validate="'required|email'" class="form-control" :class="{'input': true}" name="email" type="email" placeholder="Email" data-vv-delay="500" data-vv-as="email">
    								<span v-show="errors.has('email')" class="form-control-feedback">{{ errors.first('email') }}</span>
    							</div>
    						</div>
    						<div class="form-group">
    							
    							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('phone')}" >
    								<label class="label" for="phone">Phone Number <span class="req_star"> *</span></label>
    								<input v-model="form_data.phone" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="phone" type="text" placeholder="Phone number" data-vv-delay="500" data-vv-as="phone">
    								<span v-show="errors.has('phone')" class="form-control-feedback">{{ errors.first('phone') }}</span>
    							</div>
    							<!--<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('street')}"  style="padding-top: 20px;">-->
    							<!--	<label class="label" for="street">Street Address</label>-->
    							<!--	<input v-model="form_data.mailing_address" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="street" type="text" placeholder="Street Address" data-vv-delay="500" data-vv-as="street address">-->
    							<!--	<span v-show="errors.has('street')" class="form-control-feedback">{{ errors.first('street') }}</span>-->
    							<!--</div>-->
    							<div class="col-sm-6 col-xs-12"  :class="{'has-error': errors.has('city')}" style="padding-top: 20px;">
    								<label class="label" for="city"> City <span class="req_star"> *</span></label>
    								<input v-model="form_data.city" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="city" type="text" placeholder="Town/City" data-vv-delay="500" data-vv-as="city">
    								<span v-show="errors.has('city')" class="form-control-feedback">{{ errors.first('city') }}</span>
    							</div>
    							<div class="col-sm-6 col-xs-12 " :class="{'has-error': errors.has('postal')}"  style="padding-top: 20px;">
    								<label class="label" for="postal">Postal Code <span class="req_star"> *</span></label>
    								<input v-model="form_data.postal_code" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="postal" type="text" placeholder="Postal Code" data-vv-delay="500" data-vv-as="postal code">
    								<span v-show="errors.has('postal')" class="form-control-feedback">{{ errors.first('postal') }}</span>
    							</div>
    							
    						</div>
    						<div class="form-group account-btn text-left m-t-10 agreement">
    						    <div class="col-xs-12">
    						        <label class="checkbox">
                                        <input name="agree_terms" required type="checkbox" >
                                        I understand by checking the box, I acknowledge that I have read, understood, and accept the <a href="/pages/pinecentre-pine-kid-s-club-disclaimer-and-the-code-of-conduct" target="_blank">Disclaimer and the Code of Conduct</a>
                                    </label>
                                    <label class=""> <a href="https://www.morguard.com/privacy/"> Read our Privacy Policy here</a></label> 
    						    </div>
    							<div class="col-xs-12" style="margin-top: 20px;padding: 0;">
    								<button class="contest_btn animated_btn" type="submit" :disabled="formSuccess">Submit</button>
    							</div>
    						</div>
    					</form>
                        
                        <div id="send_contact_success" class="alert alert-success text-left" role="alert" v-show="formSuccess" style="margin-top: 20px;">
                            <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
                            <span class="sr-only">Success</span>
                            Thank you for contacting us. A member from our team will contact you shortly.
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
    define(["Vue", "vuex", "axios", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate', "v-calendar", 'utility', 'vue-checkbox-radio'], function(Vue, Vuex,axios, moment, tz, VueMoment, Meta, VeeValidate, VCalendar, Utility, CheckboxRadio) {
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
                    validaNum: '',
                    correctValNum: null,
                    validNumError: false,
                    currentContest: null,
                    pageBanner: null,
                    genderError : false,
                }
            },
            created() {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.currentContest = this.findContestBySlug('pinecentre-kids-pine-club');
                    var temp_repo = this.findRepoByName('Pages Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    this.pageBanner = this.pageBanner;
                });
            },
            mounted() {
                //creating random validation num 
                this.correctValNum = Utility.rannumber();
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
                    if(this.form_data.gender == null || this.form_data.gender == undefined || this.form_data.gender.length == 0){
                        this.genderError = true;
                    }
                    this.$validator.validateAll().then((result) => {
                        let errors = this.errors;
                        if(this.form_data.agree_newsletter ) {
                            $.getJSON("//mobilefringe.createsend.com/t/d/s/irudui/?callback=?",
                            "cm-name=" + this.form_data.first_name + this.form_data.last_name +
                            "&cm-irudui-irudui=" + this.form_data.email +
                            "&cm-f-jtukyu=" + this.form_data.city+
                            "&cm-f-jtukjr=" + this.form_data.phone +
                            "&cm-f-jtukjy=" + this.form_data.mailing_address +
                            "&cm-f-jtukjj=" + this.form_data.postal_code +
                            "&cm-f-jtukjt=" + this.form_data.birthday,
                                function (data) {
                                if (data.Status === 400) {
                                    e.preventDefault();
                                    console.error("Please try again later.");
                                } else { // 200
                                    console.log("Newsletter submission successful.");
                                }
                            });  
                        }
                        //format contests data for MM
                        var contest_entry = {};
                        contest_entry.contest = this.form_data;
                        var vm = this;
                        host_name = this.property.mm_host.replace("http:", "");
                        var url = host_name + "/contests/" + this.currentContest.slug + "/create_js_entry";
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
                        let results = await Promise.all([this.$store.dispatch("getData", "contests"),this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>