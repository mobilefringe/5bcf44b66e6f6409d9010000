<template>
    <footer>
		<div class="site_container">
		    <div class="footer_logo">
	            <div class="property_logo">
			        <router-link to="/">
			            <img src="//codecloud.cdn.speedyrails.net/sites/5bcf44b66e6f6409d9010000/image/png/1562336708799/CCM-Logo-ColorHQ.png" alt="Property Logo"/>
		            </router-link>
		        </div>    
		        <div class="center-block">
		            <nav id="footer_nav">
						<ul>
						    <li v-for="item in footer_menu_items" class="menu_item">
						        <router-link :to="item.href">{{$t(item.name)}}</router-link>
						    </li>
						</ul>
					</nav>    
		        </div>
		        <hr class="visible_phone">
	            <div class="social_icons">
                    <span v-for="item in social_media">
                        <a :href="item.url" target="_blank">
                            <i :class="item.iconClass" aria-hidden="true"></i>
                            <p class="accessibility">{{ item.name }}</p>
                        </a>
                    </span>
                </div>    
		    </div>
			<div class="footer_terms">
				<p>&copy; {{copyright_year}} <span class="caps">{{property.name}}</span> | {{$t("footer.all_rights")}}<br class="visible_phone"/> <router-link :to="'/pages/'+site.subdomain+'-privacy-policy'">{{$t("footer.privacy_policy")}}</router-link> | <span> {{$t("footer.powered_by")}} <a href="https://www.mallmaverick.com" target="_blank">Mall Maverick</a></span></p>
			</div>
		</div>
    </footer>
</template>

<script>
var _extends=Object.assign||function(e){for(var t=1;t<arguments.length;t++){var n=arguments[t];for(var o in n)Object.prototype.hasOwnProperty.call(n,o)&&(e[o]=n[o])}return e};define(["Vue","vuex","moment","moment-timezone","vue-moment","json!site.json"],function(e,t,n,o,r,a){return e.component("footer-component",{template:template,data:function(){return{site:a}},props:["footer_menu_items","social_media"],computed:_extends({},t.mapGetters(["property"]),{copyright_year:function(){return n().year()}})})});
</script>