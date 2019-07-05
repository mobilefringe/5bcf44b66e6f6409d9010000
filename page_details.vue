<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="site_container">
    				<div class="pages_content">
    				    <h3 v-if="locale=='en-ca'">{{ currentPage.title }}</h3>
    				    <h3 v-else>{{ currentPage.title_2 }}</h3>
    					<div class="page_body" v-if="locale=='en-ca'" v-html="currentPage.body"></div>
                        <div class="page_body" v-else v-html="currentPage.body_2"></div>
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
                    currentPage: null
                }
            },
            created() {
                this.updateCurrentPage(this.id);
                this.loadData().then(response => {
                    var temp_repo = this.findRepoByName('Play Banner');
                    if(temp_repo) {
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
                    'property'
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
                        let results = await Promise.all([this.$store.dispatch("getData", "categories"), this.$store.dispatch("getData", "repos")]);
                        
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>