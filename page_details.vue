<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
        			<div class="site_container">
        				<div class="header_content caps">
        					<h1 v-if="currentPage">{{ currentPage.title}}</h1>
        				</div>
        			</div>
        		</div>
                <div class="site_container">
    				<div class="pages_content" v-if="currentPage">
                        <div class="row">
            			    <div :class="{'col-md-6 col-lg-5': currentPage.image_url, 'col-md-12': !currentPage.image_url}">
            			        <div v-if="currentPage">
            			            <div v-if="locale=='en-ca'" v-html="currentPage.body"></div>
            			            <div v-else v-html="currentPage.body_2"></div>
            			        </div>
            			    </div>
            			   <div class="col-md-6 col-lg-7" v-if="currentPage.image_url">
            			       <img class="max_width" :src="currentPage.image_url" alt="" />
            			   </div>
            		    </div>
    				</div>
    			</div>
			</div>
		</transition>
    </div>
</template>
>
<script>
    define(["Vue", "vuex"], function(Vue, Vuex) {
        return Vue.component("page-details-component", {
            template: template, // the variable template will be injected,
            props:['id', 'locale'],
            data: function() {
                return {
                    dataLoaded: false,
                    currentPage: null,
                    pageBanner: null
                }
            },
            created() {
                this.updateCurrentPage(this.id);
                this.loadData().then(response => {
                    var temp_repo = ""
                    console.log("this.$route.path", this.$route.path)
                    if (_.includes(this.$route.path, "-leasing")) {
                        temp_repo = this.findRepoByName('Mall Info Banner');
                    } else {
                        temp_repo = this.findRepoByName('Page Banner');
                    }
                    
                    if (temp_repo && temp_repo.images) {
                        try {
                            this.pageBanner = temp_repo.images[0];
                        } catch(e) {
                            
                        }
                    } else {
                        this.pageBanner = { "image_url": "" }
                    }
                    
                    this.dataLoaded = true;
                });
            },
            watch: {
                $route: function () {
                    this.updateCurrentPage(this.$route.params.id);
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'findRepoByName'
                ])
            },
            methods: {
                updateCurrentPage(id) {
                    var _this = this;
                    this.$store.dispatch('LOAD_PAGE_DATA', { url: this.property.mm_host + "/pages/" + this.id + ".json" }).then(function (response) {
                        _this.currentPage = response.data;
                        _this.dataLoaded = true;
                    }, function (error) {
                        console.error( "Could not retrieve data from server. Please check internet connection and try again.");
                        _this.$router.replace({ name: 'home' });
                    });
                },
                loadData: async function() {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>