<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
        			<div class="site_container">
        				<div class="header_content caps">
        					<h1>{{ $t("programs_page.programs_header") }}</h1>
        				</div>
        			</div>
        		</div>
        		<div class="site_container">
                    <div class="program_header_container hidden_phone">
        				<div class="program_button_container active">
    				        {{ $t("programs_page.gift_title") }}
        				</div>
        				<router-link to="/kids-club">
        				    <div class="program_button_container animated_btn">
        				        {{ $t("programs_page.kids_club_title") }}
        				    </div>
    				    </router-link>
        			</div>
        			<div class="visible_phone margin_20">
        			    <label style="display: none;" for="pageSelect">Select a Page</label>
        				<v-select v-model="selected" :options="pageOptions" :searchable="false" :on-change="selectPage" class="category-select" inputId="pageSelect"></v-select>
        			</div>
        			<!--<div v-if="pageBody" class="row">-->
        			<!--    <div class="col-md-12">-->
        			<!--        <h4 class="margin_40 center bold" v-html="pageBody.body"></h4>-->
        			<!--    </div>-->
        			<!--</div>-->
        			<div class="row">
        			    <div :class="{'col-md-8': pageImage, 'col-md-12': !pageImage}" v-if="pageBody">
        			        <div class="" v-html="pageBody.body"></div>
        			    </div>
        			   <div class="col-md-4" v-if="pageImage">
        			       <img class="max_width" :src="pageImage" alt="" />
        			   </div>
        		    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "json!site.json"], function(Vue, Vuex, Site) {
        return Vue.component("giftcards-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: false,
                    pageBanner: null,
                    // pageHeader: null,
                    pageBody: null,
                    pageImage: null,
                    pageOptions: [
                        { 'label': 'Gift Cards', 'value': 'gift_cards', 'disabled': true },
                        { 'label': 'Kids Club', 'value': 'club', 'disabled': false }
                    ],
                    selected: { 'label': 'Southland Scoop', 'value': 'gift_cards', 'disabled': true },
                }
            },
            created(){
                this.loadData().then(response => {
                    var temp_repo = this.findRepoByName('Kids Club Banner');
                    if (temp_repo) {
                        try {
                            this.pageBanner = temp_repo.images[0];
                        } catch(e) {
                            
                        }
                    } else {
                        this.pageBanner = { "image_url": "https://via.placeholder.com/1920x300" }
                    }
                    
                    if (response && response[1]) {
                        try {
                            this.pageBody = response[1].data
                            // this.pageBody = response[1].data.subpages[0]
                            if(this.pageBody.image_url){
                                this.pageImage = "//www.mallmaverick.com" + this.pageBody.image_url;
                            }
                        } catch (e) {
                            
                        }
                    }
                    
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ])
            },
            methods: {
                loadData: async function() {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "repos"), this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "/pages/"+Site.subdomain+"-gift-cards.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                selectPage(item) {
                    var item = item.value
                    if (_.includes(item, "club")) {
                        this.$router.push("/kids-club");
                    }
                    
                }
            }
        });
    });
</script>
