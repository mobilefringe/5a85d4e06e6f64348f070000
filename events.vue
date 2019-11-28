<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <inside-header-component></inside-header-component>
        </transition>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="main_container margin_30">
                <div v-if="events.length == 0">
                    <p>Sorry, there are no event postings at this time. Please check back soon.</p>
                </div>
                <div class="events_container" v-if="events.length >= 1" v-for="event in events">
                    <div class="event_left">
                        <img :src="event.image_url" alt=""/>
                    </div>
                    <div class="event_right">
                        <h2 class="event_name">{{ event.name }}</h2>
                        <p class="dates" v-if="isMultiDayEvent(event)">{{ event.start_date | moment("MMM D", timezone)}} to {{ event.end_date | moment("MMM D", timezone)}}</p>
                        <p class="dates" v-else>{{ event.start_date | moment("MMM D", timezone)}}</p>
                        <div class="event_desc">{{ event.description }}</div>
                        <!--<router-link :to="{ name: 'eventDetails', params: { id: event.slug }}">-->
                        <router-link :to="'/events/' + event.slug">
                            <p class="read_more">Event Details</p>
                        </router-link>
                    </div>
                </div>  
            </div>
        </transition>
    </div>
</template>
<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", "lightbox", "vue-lazy-load"], function (Vue, Vuex, moment, tz, VueMoment, Meta, Lightbox, VueLazyload) {
        Vue.use(Meta);
        Vue.use(Lightbox);
        Vue.use(VueLazyload);
        return Vue.component("events-component", {
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
                    'timezone',
                    'processedEvents',
                ]),
                events: function events() {
                    var events = this.processedEvents;
                    var showEvents = [];
                    _.forEach(events, function (value, key) {
                        var today = moment.tz(this.timezone).format();
                        var showOnWebDate = moment.tz(value.show_on_web_date, this.timezone).format();
                        if (today >= showOnWebDate) {
                            if (_.includes(value.image_url, 'missing')) {
                                value.image_url = "//codecloud.cdn.speedyrails.net/sites/5b22de6b6e6f640dbb2c0000/image/png/1527612896000/milton_logo.png";
                            }
                            
                            if(window.width > 768) {
                                value.description = _.truncate(value.description, { 'length': 100, 'separator': ' ' });
                            } else {
                                value.description = _.truncate(value.description, { 'length': 50, 'separator': ' ' });    
                            }
                            showEvents.push(value);
                        }
                    });
                    var sortedEvents = _.orderBy(showEvents, function (o) { return o.end_date })
                    return sortedEvents
                }
            },
            methods: {
                loadData: async function () {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "events")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                isMultiDayEvent(event) {
                    var timezone = this.timezone
                    var start_date = moment(event.start_date).tz(timezone).format("MM-DD-YYYY")
                    var end_date = moment(event.end_date).tz(timezone).format("MM-DD-YYYY")
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
