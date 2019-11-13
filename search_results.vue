<template>
	<div>
		<loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="site_container">
                    <div class="pages_content">
                		<div class="page_container text-left" v-if="searchResults && searchResults.length > 0" id="searchResults">
                            <p class="search_result_title">Found {{searchResults.length}} results matching "{{searchQuery}}"</p>
                            <div class="row result_container_row" v-for="(result, index) in searchResults" :key="index">
                                <div v-if="result.is_store" class="col-sm-2">
                                    <div v-if="result.image_url && _.includes(result.image_url,'missing') || (!result.image_url && _.includes(result.store.store_front_url_abs, 'missing'))">
                                        <div class="no_logo">
                                            <img src="//codecloud.cdn.speedyrails.net/sites/5b88438d6e6f641e8d3c0000/image/png/1536092029690/transparent_logo.png">
                                            <h4 class="store_details_name">
                                                <span v-if="result.store_front_url_abs">{{result.name}}</span>
                                                <span v-else>{{ result.store.name }}</span>
                                            </h4>
                                        </div>    
                                    </div> 
                                    <div v-else class="store_details_image center-block">
                                        <img v-if="result.store" class="result_logo" :src="result.store.store_front_url_abs"/>
                                        <img v-else-if="result.store_front_url_abs" class="result_logo" :src="result.store_front_url_abs"/>
                                    </div>
                                </div>
                                <div v-else class="col-sm-2 store_details_image center-block">
                                    <img class="result_logo" src="//codecloud.cdn.speedyrails.net/sites/5bcf44b66e6f6409d9010000/image/png/1573070939863/cc_default_logo.png"/>    
                                </div>
                                <div class="col-sm-10 search_result_content">
                                    <h4 class="bold">{{result.name}}</h4>
                                    <p>{{ getCategory(result) }}</p>
                                    <p>{{truncated(result.description)}}</p>
                                    <router-link v-if="result.store_front_url_abs" class="result_link hvr-icon-forward" :to="{ name: 'storeDetails', params:{ id:result.slug }}">
                                        <div class="results_btn animated_btn">View Store Details</div>
                                    </router-link>
                                    <router-link v-else-if="result.promo_image_url_abs" class="result_link hvr-icon-forward" :to="{ name: 'promotionDetails', params: { id: result.slug }}">
                                        <div class="results_btn animated_btn">View Promotion Details</div>
                                    </router-link>
                                    <router-link v-else-if="result.event_image_url_abs" class="result_link hvr-icon-forward" :to="{ name: 'eventDetails', params: { id: result.slug }}">
                                        <div class="results_btn animated_btn">View Event Details</div>
                                    </router-link>
                                    <router-link v-else-if="result.jobable_id" class="result_link hvr-icon-forward" :to="{ name: 'jobDetails', params: { id: result.slug }}">
                                        <div class="results_btn animated_btn">View Job Details</div>
                                    </router-link>
                                </div>
                            </div>                
                		</div>
                        <div class="page_container text-left" v-else> 
                            <p>Sorry, there are no results that match your search.</p>
                        </div>
                    </div>
	            </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "jquery"], function(Vue, Vuex, $) {
        return Vue.component("search-results", {
            template: template, // the variable template will be injected
            data() {
                return {
                    searchResults: null,
                    searchQuery: null,
                    dataLoaded: true,
                }
            },
            beforeRouteUpdate(to, from, next) {
                this.$nextTick(function() {
                    this.updateResults();
                });
                next();
            },
            created() {
                this.updateResults();
                if (this.$route.params.results == null && this.$route.params.results == undefined) {
                    this.$router.push("/");
                }
                console.lo
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'findCategoryById',
                ])
            },
            methods: {
                truncated(string) {
                    return _.truncate(string, {
                        length: 150,
                        separator: " "
                    });
                },
                updateResults() {
                    if (
                        this.$route.query.searchQuery !== null &&
                        this.$route.query.searchQuery !== undefined &&
                        this.$route.query.searchQuery.length > 0
                    ) {
                        if (
                            this.$route.params.results !== null &&
                            this.$route.params.results !== undefined &&
                            Array.isArray(this.$route.params.results)
                        ) {
                            this.searchResults = this.$route.params.results;
                            console.log("results", this.searchResults)
                            this.searchQuery = this.$route.query.searchQuery;
                        }
                    } else {
                        this.$router.push("/");
                    }
                },
                getCategory(result) {
                    console.log("result", result)
                    if (result.is_store) {
                        if (result.store) {
                            var storeCategory = result.store.categories[0];
                            if (storeCategory) {
                                category = this.findCategoryById(storeCategory)
                                return category.name    
                            }
                        } else {
                            if (result.categories){
                                var storeCategory = result.categories[0];
                                if (storeCategory) {
                                    category = this.findCategoryById(storeCategory)
                                    return category.name    
                                }
                            }
                        }
                    } else {
                        
                    }
                }
            }
        });
    });
</script>