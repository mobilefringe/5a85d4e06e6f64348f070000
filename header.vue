<template>
    <header>
        <section id="header" class="header main_container">
            <div class="row logo_container">
                <div class="col-md-2">
                    <div class="site_logo center-block">
                        <a href="/">
                            <img alt="Property Logo" src="//codecloud.cdn.speedyrails.net/sites/5a85d4e06e6f64348f070000/image/png/1519152520000/logo.png">
                        </a>
                    </div>
                    <div @click="show_menu = !show_menu" :class="{ open: show_menu }" id="menu-icon">
                        <span></span>
                        <span></span>
                        <span></span>
                        <span></span>
                    </div>
                </div>
                <div class="col-md-10">
                    <div class="header_social_container hidden-sm hidden-xs">
                        <div class="header_social">
                            <div class="social_icons">
                                <span v-for="item in social_media">
                                    <a :href="item.url" target="_blank">
                                        <i :class="item.iconClass" aria-hidden="true"></i>
                                    </a>
                                </span>
                            </div>
                        </div>
                        <div class="header_newsletter">
                            <div class="header_newsletter_container">
                                <input id="header_newsletter" class="newsletter_input" type="text" placeholder="Join Our Newsletter!" v-model="newsletter_email"/>
                                <router-link :to="'/newsletter?email='+ newsletter_email" >
                                    <input id="header_newsletter_submit" class="newsletter_btn" type="submit" value="Subscribe"/>
                                </router-link>
                            </div>
                        </div>
                    </div>
                    <transition name="custom-classes-transition" enter-active-class="animated slideInRight" leave-active-class="animated slideOutRight">
                        <nav id="primary_nav" v-if="show_menu" v-on:keyup.esc="show_menu = false">
                            <div class="todays_hours" v-if="todays_hours">
                                <span v-if="!todays_hours.is_closed">
                                    Open Today - {{todays_hours.open_time | moment("h:mma", timezone)}} to {{todays_hours.close_time | moment("h:mma", timezone)}}
                                </span>
                                <span v-if="todays_hours.is_closed">
                                    Closed Today
                                </span>
                            </div>
                            <ul>
                                <li id="dropDown1" @click="toggleSubMenu('dropDown1')" class="menu_item">Store Directory
                                    <ul :class="{ show_submenu: showSubMenu1 }">
                                        <router-link tag="li" to="/stores" class="submenu_item" exact>
                                            <a>Store Directory</a>
                                        </router-link>
                                        <router-link tag="li" to="/pages/milton-office-directory" class="submenu_item" exact>
                                            <a>Office Directory</a>
                                        </router-link>
                                        <router-link tag="li" to="/map" class="submenu_item" exact>
                                            <a>Centre Map</a>
                                        </router-link>
                                    </ul>
                                </li>
                                <router-link tag="li" to="/events" class="menu_item" exact>
                                    Events
                                </router-link>
                                <router-link tag="li" to="/promotions" class="menu_item" exact>
                                    Promotions
                                </router-link>
                                <li id="dropDown2" @click="toggleSubMenu('dropDown2')" class="menu_item">Leasing
                                    <ul :class="{ show_submenu: showSubMenu2 }">
                                        <router-link tag="li" to="/pages/milton-leasing" class="submenu_item" exact>
                                            <a>Leasing</a>
                                        </router-link>
                                        <router-link tag="li" to="/photos" class="submenu_item" exact>
                                            <a>Photos</a>
                                        </router-link>
                                    </ul>
                                </li>
                                <li id="dropDown3" @click="toggleSubMenu('dropDown3')" class="menu_item">Contact Us
                                    <ul :class="{ show_submenu: showSubMenu3 }">
                                        <router-link tag="li" to="/contact-us" class="submenu_item" exact>
                                            <a>Contact Us</a>
                                        </router-link>
                                        <router-link tag="li" to="/directions" class="submenu_item" exact>
                                            <a>Find Us</a>
                                        </router-link>
                                        <router-link tag="li" to="/pages/milton-about-us" class="submenu_item" exact>
                                            <a>About Us</a>
                                        </router-link>
                                        <router-link tag="li" to="/jobs" class="submenu_item" exact>
                                            <a>Jobs</a>
                                        </router-link>
                                        <router-link tag="li" to="/hours" class="submenu_item" exact>
                                            <a>Hours</a>
                                        </router-link>
                                        <router-link tag="li" to="/community" class="submenu_item" exact>
                                            <a>Community</a>
                                        </router-link>
                                    </ul>
                                </li>
                            </ul>
                            <div class="mobile_social_icons">
                                <span v-for="item in social_media">
                                    <a :href="item.url" target="_blank">
                                        <i :class="item.iconClass" aria-hidden="true"></i>
                                    </a>
                                </span>
                            </div>
                        </nav>
                    </transition>
                </div>
            </div>
        </section>
    </header>
</template>

<script>
var _extends=Object.assign||function(t){for(var e=1;e<arguments.length;e++){var o=arguments[e];for(var n in o)Object.prototype.hasOwnProperty.call(o,n)&&(t[n]=o[n])}return t};define(["Vue","vuex","vue_router","routes","vue!today_hours.vue"],function(t,e){return t.component("header-component",{template:template,data:function(){return{active:!1,newsletter_email:"",isOpen:!1,windowWidth:0,show_menu:!0,showSubMenu1:!1,showSubMenu2:!1,showSubMenu3:!1}},props:["social_media"],watch:{$route:function(){this.windowWidth<=768&&(this.show_menu=!1)},windowWidth:function(){this.windowWidth<=768?this.show_menu=!1:(this.show_menu=!0,document.body.classList.remove("no-scroll"))},show_menu:function(){1==this.show_menu?document.body.classList.add("no-scroll"):0==this.show_menu&&document.body.classList.remove("no-scroll")}},mounted:function(){this.$nextTick(function(){window.addEventListener("resize",this.getWindowWidth),this.getWindowWidth()})},computed:_extends({},e.mapGetters(["property","timezone","hours","getTodayHours"]),{locale:{get:function(){return this.$store.state.locale},set:function(t){this.$store.commit("SET_LOCALE",{lang:t})}},todays_hours:function(){return this.getTodayHours}}),methods:{changeLocale:function(t){this.locale=t},getWindowWidth:function(){this.windowWidth=window.innerWidth},toggleSubMenu:function(t){this.showSubMenu1=!1,this.showSubMenu2=!1,this.showSubMenu3=!1,"dropDown1"==t?this.showSubMenu1=!0:"dropDown2"==t?this.showSubMenu2=!0:"dropDown3"==t&&(this.showSubMenu3=!0)}},beforeDestroy:function(){window.removeEventListener("resize",this.getWindowWidth)}})});
</script>