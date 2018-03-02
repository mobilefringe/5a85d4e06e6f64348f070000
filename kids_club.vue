<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div class="row">
                
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