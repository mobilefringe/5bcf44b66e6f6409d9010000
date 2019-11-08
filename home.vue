<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <h1 class="accessibility">Welcome to Country Club Mall</h1>
        		<div class="banner_div">
        			<div class="home_banner_container">
        				<slick ref="slick" :options="slickOptions">
        					<div class="" v-for="banner in banners" v-if="banners">
        						<div class="home_banner" v-bind:style="{ background: '#000 url(' + banner.image_url + ')' }">
        						    <!--<div class="home_banner_content" v-if="banner.name && banner.description">-->
        						    <!--    <h1>{{ banner.name }}</h1>-->
        						    <!--    <h2>{{ banner.description }}</h2>-->
        						    <!--</div>   -->
        						</div>
        					</div>
        				</slick>
        			</div>
        		</div>
        		<div class="site_container">
        		    <div class="visible_phone">
        		        <div class="hours_container home_hours margin_top_30">
                            <h5 class="center caps" v-if="hour.is_open" v-for="hour in todaysHours">{{ property.name }} is open today:</h5>
        		            <h5 class="center caps" v-else>{{ property.name }} is</h5>
                            <h3 class="center caps" v-if="hour.is_open" v-for="hour in todaysHours">{{hour.open_time | moment("h a", timezone)}} - {{hour.close_time | moment("h a", timezone)}}</h3>
                            <h3 class="center caps" v-else>Closed</h3>
                        </div>
        		    </div>
        		    <div class="home_page_title_container">
        		        <h5 class="home_page_subtitle center caps">Discover {{ property.name }}</h5>
        		        <h3 class="home_page_title caps">What's Happening</h3>
        		    </div>
        		    <div v-masonry transition-duration="0.3s" item-selector=".grid-item" > <!-- class="hidden_phone" -->
                        <div v-masonry-tile class="item">
                            <div v-for="feature in feature_items" :class="'grid-item ' + feature.masonry_class ">
                        	    <div v-if="feature.name" class="feature_item_container">
                        			<img :src="feature.image_url" alt="">
                        			<div class="feature_item_info" >
                        				<div class="feature_item_content">
                        					<p>{{ feature.name }}</p>
                        					<h3 v-if="feature.description">{{ feature.description }}</h3>
                        					<a class="feature_item_link" :href="feature.url">
                        					    <div class="feature_item_more animated_btn" :aria-label="feature.name">View Details</div>
                        					</a>
                        				</div>
                        			</div>
                        	    </div>
                        	    <div v-else class="feature_item_container">
                        	        <a :href="feature.url">
                            			<img :src="feature.image_url" :alt="feature.name">
                            			<div class="feature_item_info"></div>
                            		</a>
                        	    </div>
                            </div>
                        </div>
                    </div>
                    <!--<div v-masonry transition-duration="0.3s" item-selector=".grid-item" class="visible_phone">-->
                    <!--    <div v-masonry-tile class="item">-->
                    <!--        <div v-for="feature in feature_items" :class="'grid-item ' + feature.masonry_class ">-->
                    <!--    	    <div v-if="feature.name && feature.description" class="feature_item_container">-->
                    <!--    	        <a :href="feature.url">-->
                    <!--        			<img :src="feature.image_url" :alt="feature.name">-->
                    <!--        			<div class="feature_item_info" >-->
                    <!--        				<div class="feature_item_content">-->
                    <!--        					<p>{{ feature.name }}</p>-->
                    <!--        					<h3>{{ feature.description }}</h3>-->
                    <!--        					<div class="feature_item_more" :aria-label="feature.name">View Details</div>-->
                    <!--        				</div>-->
                    <!--        			</div>-->
                    <!--        		</a>-->
                    <!--    	    </div>-->
                    <!--    	    <div v-else class="feature_item_container">-->
                    <!--    	        <a :href="feature.url">-->
                    <!--        			<img :src="feature.image_url" :alt="feature.name">-->
                    <!--        			<div class="feature_item_info"></div>-->
                    <!--        		</a>-->
                    <!--    	    </div>-->
                    <!--        </div>-->
                    <!--    </div>-->
                    <!--</div>-->
                    <!-- Additional Feature Items -->
              <!--      <div class="home_page_title_container">-->
        		    <!--    <h5 class="home_page_subtitle center caps">Programs</h5>-->
        		    <!--    <h3 class="home_page_title second caps">Kids Club & Gift Cards</h3>-->
        		    <!--</div>-->
        		    <!--<div class="row">-->
        		    <!--    <div v-for="feature in programs" class="col-sm-6">-->
        		    <!--        <div class="feature_item_container programs" :class="feature.program_class">-->
              <!--      			<div class="feature_item_info">-->
              <!--      				<div class="feature_item_content">-->
              <!--      					<p>{{ feature.name }}</p>-->
              <!--      					<h3>{{ feature.description }}</h3>-->
              <!--      					<a :href="feature.url" :aria-label="feature.name">-->
              <!--          					<div class="feature_item_more animated_btn">View Details</div>-->
              <!--          				</a>-->
              <!--      				</div>-->
              <!--      			</div>-->
              <!--      	    </div>-->
        		    <!--    </div>-->
        		    <!--</div>-->
        		</div>
        	</div>
        </transition>    		
	</div>
</template>

<script>
    define(["Vue", "vuex", "vue-meta", "vue!vue-slick", "masonry", "vue-masonry-plugin", "moment", "moment-timezone"], function(Vue, Vuex, Meta, slick, masonry, VueMasonryPlugin, moment, tz) {
        Vue.use(VueMasonryPlugin.default);
        return Vue.component("home-component", {
            template: template, // the variable template will be injected
            props:['locale'],
            data: function() {
                return {
                    dataLoaded: false,
                    meta: {
                        meta_title: "",
                        meta_description: "",
                        meta_keywords: "",
                        meta_image: ""
                    },
                    slickOptions: {
                        arrows: false,
                        autoplay: true,
                        autoplaySpeed: 3000,
                        cssEase: 'linear',
                        dots: true,
                        fade: true,
                        infinite: true,
                        slidesToShow: 1,
                        speed: 1200
                    },
                    suggestionAttribute: "name",
                    storeSearch: null,
                }
            },
            created () {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    
                    this.meta = this.findMetaDataByPath(this.$route.path);
                });
            },
            // watch : {
            //     dataLoaded () {
            //         var viewed = Cookies.get('popup_viewed');
            //         if(this.popup !== null && viewed !== "true") {
            //             Cookies.set('popup_viewed', "true");
            //             viewed = Cookies.get('popup_viewed');
            //             this.show_popup = true;
            //             this.popup.image_url = "//mallmaverick.cdn.speedyrails.net" + this.popup.photo_url;
            //             document.getElementById('popup_backdrop').style.display = "block";
            //         } else {
            //             document.getElementById('popup_backdrop').style.display = "none";
            //         }
            //     }
            // },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'getPropertyHours',
                    'getPropertyHolidayHours',
                    'processedStores'
                ]),
                banners () {
                    var banners = [];
                    _.forEach(this.$store.state.banners, function(value, key) {
                        var today = new Date();
                        var start = new Date (value.start_date);
                        if (start <= today){
                            if (value.end_date){
                                var end = new Date (value.end_date);
                                if (end >= today){
                                    banners.push(value);  
                                }
                            } else {
                                banners.push(value);
                            }
                        }
                    })
                    return _.orderBy(banners, ['position'], ['asc']);
                },
                feature_items () {
                    var features = _.slice(this.$store.state.feature_items, 0, 4);
                    _.forEach(features, function(value, key) {
                        if ( _.includes([1], key) ) {
                            value.masonry_class = "grid-item--width2";
                        } else if ( _.includes([2], key) ){
                            value.masonry_class = "grid-item--width2";
                        } else {
                            value.masonry_class = " ";
                        }
                    });
                    return features;
                },
                programs () {
                    var features = _.slice(this.$store.state.feature_items, 6, 8); 
                    _.forEach(features, function(value, key) {
                        if ( _.includes([1], key)) {
                            value.program_class = "last";
                        }
                    });
                    return features
                },
                todaysHours() {
                    var timezone = this.timezone
                    var regHours = this.getPropertyHours;
                    var holHours = this.getPropertyHolidayHours;
                    var hours = [];
                    // First check if there is a holiday today
                    _.forEach(holHours, function(value) {
                        var today = moment().format("YYYY-MM-DD");
                        var holiday_date = moment(value.holiday_date).tz(timezone).format("YYYY-MM-DD");
                        if (today == holiday_date) {
                            if (value.is_closed) {
                                value.is_open = false;
                            } else {
                                value.is_open = true;
                            }
                            hours.push(value);
                        }
                    });
                    // If there is no holiday today, check for today's hours
                    if (hours.length > 0) {
                        return hours;
                    } else {
                        _.forEach(regHours, function(value) {  
                            var today = moment().day();
                            if ( today == value.day_of_week ) {
                                var time = moment().tz(timezone).format('HH:mm');
                                var opens = moment(value.open_time).tz(timezone).format('HH:mm');
                                var closes = moment(value.close_time).tz(timezone).format('HH:mm');
                                if (time > opens && time < closes) {
                                    value.is_open = true;
                                } else {
                                    value.is_open = false;
                                }
                                hours.push(value);
                            }         
                        });
                        return hours;
                    }
                }
            },
            methods: {
                loadData: async function() {
                    try {
                        let results = await Promise.all([
                            this.$store.dispatch("getData", "banners"), 
                            this.$store.dispatch("getData", "feature_items"), 
                            this.$store.dispatch("getData", "popups")
                        ]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            },
            metaInfo () {
                return {
                    title: this.meta.meta_title,
                    meta: [
                        { name: 'description', vmid: 'description', content: this.meta.meta_description },
                        { name: 'keywords',  vmid: 'keywords', content: this.meta.meta_keywords },
                        { property: 'og:title', vmid: 'og:title', content: this.meta.meta_title },
                        { property: 'og:description', vmid: 'og:description', content: this.meta.meta_description },
                        { property: 'og:image', vmid: 'og:image', content: this.meta.meta_image }
                    ]
                }
            }
        })
    })
</script>