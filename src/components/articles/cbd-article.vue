
<template>
   <div style="border:none;margin-top:10px">
        <div v-if="!loading">
        
            <slot name="article-cover-image">
                <div v-if="!hideCoverImage && article && article.coverImage && article.coverImage.url" >
                    <cbd-article-cover-image :cover-image="article.coverImage"></cbd-article-cover-image>
                </div>
            </slot>

            <slot name="article-add-new">
                <div v-if="hasEditRights" class="pull-right">    
                    <cbd-add-new-article :tags="tags" :admin-tags="adminTags" :custom-tags="customTags" :id="(article||{})._id" 
                        :target="showEditTarget" class="btn btn-default"></cbd-add-new-article>
                </div>
            </slot>
            <slot name="article">
                <div v-if="article" v-html="$options.filters.lstring(article.content, $locale)" class="ck-content"></div>
            </slot>

            <slot name="article-empty">
                <div v-if="!article" class="ck-content">No information is available for this section at the moment.</div>
            </slot>

        </div>
        <slot name="article-loading">
            <div v-if="loading">Loading content... <i class="fa fa-spinner fa-spin"></i></div>
        </slot>
    </div>

</template>

<script>

// import axios from 'axios';
import ky from 'ky';

import ArticlesApi from '../../services/api/articles';
import {lstring } from '../../services/filters/lstring'
import cbdAddNewArticle from './cbd-add-new-article.vue';
import cbdArticleCoverImage from './cbd-article-cover-image.vue';

export default {
    name: 'cbdArticle',
    components : { 
        cbdAddNewArticle, 
        cbdArticleCoverImage 
    },
    props: {
        hideCoverImage  : { type: Boolean, required: false, default:false        },
        showEdit        : { type: Boolean, required: false, default:true         },
        showEditTarget  : { type: String , required: false, default: '_self'     },
        showEditRoles   : { type: Array  , required: false, default:[]           }, // [] of scope roles
        article         : { type: Object,  required: false, default:undefined    },
        query           : { type: Object,  required: false                       },
        tags 		    : { type: Array  , required: false, default:[]           }, // [] of tag id's
        customTags 	    : { type: Array  , required: false, default:[]           }, // [] of customTag id's
        adminTags 	    : { type: Array  , required: false, default:[]           }, // [] of adminTag text
    },
    data() {
        return {
            returnUrl       : window.location.href,
            hasEditRights   : false,
            loading         : false
        }
    },
    created() {
       this.articlesApi = new ArticlesApi();
    },
    mounted() {

    // if(!this.article && this.query)
            this.loadArticle();
    },
    methods: {
        async loadArticle() {
            try{
                console.log("this.ArticlesApi", this.articlesApi)  
                const articleResult = await this.articlesApi.getArticleById("619c556b4f1f30000140ef06");
                console.log("articleResult articleResult articles data", articleResult);
                // const axiosResponse = await axios.get('https://api.cbddev.xyz/api/v2017/articles?ag=[%7B%22$match%22:%7B%22_id%22:%7B%22$in%22:[%7B%22$oid%22:%22619c553658029700017ff43b%22%7D,%7B%22$oid%22:%22619c55594f1f30000140eeec%22%7D,%7B%22$oid%22:%22619c553c0c1ff000011c1c22%22%7D,%7B%22$oid%22:%22619c553e0c1ff000011c1c24%22%7D,%7B%22$oid%22:%22619c553f4f1f30000140eeba%22%7D,%7B%22$oid%22:%22619c554e58029700017ff467%22%7D,%7B%22$oid%22:%22619c554258029700017ff447%22%7D,%7B%22$oid%22:%22619c555c58029700017ff475%22%7D,%7B%22$oid%22:%22619c553d58029700017ff445%22%7D,%7B%22$oid%22:%22619c555d4f1f30000140eef4%22%7D,%7B%22$oid%22:%22619c55374f1f30000140eeae%22%7D,%7B%22$oid%22:%22619c554558029700017ff44d%22%7D,%7B%22$oid%22:%22619c55464f1f30000140eec8%22%7D,%7B%22$oid%22:%22619c55384f1f30000140eeb0%22%7D,%7B%22$oid%22:%22619c554858029700017ff45f%22%7D,%7B%22$oid%22:%22619c554d4f1f30000140eed6%22%7D,%7B%22$oid%22:%22619c554e58029700017ff465%22%7D,%7B%22$oid%22:%22619c554f4f1f30000140eeda%22%7D,%7B%22$oid%22:%22619c553a4f1f30000140eeb2%22%7D,%7B%22$oid%22:%22619c555058029700017ff46b%22%7D,%7B%22$oid%22:%22619c556b4f1f30000140ef06%22%7D,%7B%22$oid%22:%22619c555258029700017ff46d%22%7D,%7B%22$oid%22:%22619c55534f1f30000140eee0%22%7D,%7B%22$oid%22:%22619c55440c1ff000011c1c2e%22%7D,%7B%22$oid%22:%22619c55390c1ff000011c1c20%22%7D,%7B%22$oid%22:%22619c55550c1ff000011c1c44%22%7D,%7B%22$oid%22:%22619c55564f1f30000140eee6%22%7D,%7B%22$oid%22:%22619c55400c1ff000011c1c28%22%7D,%7B%22$oid%22:%22619c53c148780824013af838%22%7D,%7B%22$oid%22:%22619c55410c1ff000011c1c2a%22%7D,%7B%22$oid%22:%22619c553958029700017ff43d%22%7D,%7B%22$oid%22:%22619c55640c1ff000011c1c4e%22%7D,%7B%22$oid%22:%22619c553a58029700017ff441%22%7D,%7B%22$oid%22:%22619c55564f1f30000140eee8%22%7D,%7B%22$oid%22:%22619c55574f1f30000140eeea%22%7D,%7B%22$oid%22:%22619c555758029700017ff471%22%7D,%7B%22$oid%22:%22619c55580c1ff000011c1c46%22%7D,%7B%22$oid%22:%22619c553f4f1f30000140eeba%22%7D,%7B%22$oid%22:%22619c554758029700017ff45d%22%7D,%7B%22$oid%22:%22619c55474f1f30000140eeca%22%7D,%7B%22$oid%22:%22619c554858029700017ff45f%22%7D,%7B%22$oid%22:%22619c554a0c1ff000011c1c40%22%7D,%7B%22$oid%22:%22619c554b4f1f30000140eed2%22%7D,%7B%22$oid%22:%22619c554c4f1f30000140eed4%22%7D,%7B%22$oid%22:%22619c553a4f1f30000140eeb2%22%7D,%7B%22$oid%22:%22619c553c58029700017ff443%22%7D,%7B%22$oid%22:%22619c55430c1ff000011c1c2c%22%7D]%7D%7D%7D,%7B%22$project%22:%7B%22title%22:1,%22summary%22:1%7D%7D,%7B%22$limit%22:47%7D]');                    
                // console.log("axiosResponse", axiosResponse) 
                
                // const kyResponse = await ky.get('https://api.cbddev.xyz/api/v2017/articles?ag=[%7B%22$match%22:%7B%22_id%22:%7B%22$in%22:[%7B%22$oid%22:%22619c553658029700017ff43b%22%7D,%7B%22$oid%22:%22619c55594f1f30000140eeec%22%7D,%7B%22$oid%22:%22619c553c0c1ff000011c1c22%22%7D,%7B%22$oid%22:%22619c553e0c1ff000011c1c24%22%7D,%7B%22$oid%22:%22619c553f4f1f30000140eeba%22%7D,%7B%22$oid%22:%22619c554e58029700017ff467%22%7D,%7B%22$oid%22:%22619c554258029700017ff447%22%7D,%7B%22$oid%22:%22619c555c58029700017ff475%22%7D,%7B%22$oid%22:%22619c553d58029700017ff445%22%7D,%7B%22$oid%22:%22619c555d4f1f30000140eef4%22%7D,%7B%22$oid%22:%22619c55374f1f30000140eeae%22%7D,%7B%22$oid%22:%22619c554558029700017ff44d%22%7D,%7B%22$oid%22:%22619c55464f1f30000140eec8%22%7D,%7B%22$oid%22:%22619c55384f1f30000140eeb0%22%7D,%7B%22$oid%22:%22619c554858029700017ff45f%22%7D,%7B%22$oid%22:%22619c554d4f1f30000140eed6%22%7D,%7B%22$oid%22:%22619c554e58029700017ff465%22%7D,%7B%22$oid%22:%22619c554f4f1f30000140eeda%22%7D,%7B%22$oid%22:%22619c553a4f1f30000140eeb2%22%7D,%7B%22$oid%22:%22619c555058029700017ff46b%22%7D,%7B%22$oid%22:%22619c556b4f1f30000140ef06%22%7D,%7B%22$oid%22:%22619c555258029700017ff46d%22%7D,%7B%22$oid%22:%22619c55534f1f30000140eee0%22%7D,%7B%22$oid%22:%22619c55440c1ff000011c1c2e%22%7D,%7B%22$oid%22:%22619c55390c1ff000011c1c20%22%7D,%7B%22$oid%22:%22619c55550c1ff000011c1c44%22%7D,%7B%22$oid%22:%22619c55564f1f30000140eee6%22%7D,%7B%22$oid%22:%22619c55400c1ff000011c1c28%22%7D,%7B%22$oid%22:%22619c53c148780824013af838%22%7D,%7B%22$oid%22:%22619c55410c1ff000011c1c2a%22%7D,%7B%22$oid%22:%22619c553958029700017ff43d%22%7D,%7B%22$oid%22:%22619c55640c1ff000011c1c4e%22%7D,%7B%22$oid%22:%22619c553a58029700017ff441%22%7D,%7B%22$oid%22:%22619c55564f1f30000140eee8%22%7D,%7B%22$oid%22:%22619c55574f1f30000140eeea%22%7D,%7B%22$oid%22:%22619c555758029700017ff471%22%7D,%7B%22$oid%22:%22619c55580c1ff000011c1c46%22%7D,%7B%22$oid%22:%22619c553f4f1f30000140eeba%22%7D,%7B%22$oid%22:%22619c554758029700017ff45d%22%7D,%7B%22$oid%22:%22619c55474f1f30000140eeca%22%7D,%7B%22$oid%22:%22619c554858029700017ff45f%22%7D,%7B%22$oid%22:%22619c554a0c1ff000011c1c40%22%7D,%7B%22$oid%22:%22619c554b4f1f30000140eed2%22%7D,%7B%22$oid%22:%22619c554c4f1f30000140eed4%22%7D,%7B%22$oid%22:%22619c553a4f1f30000140eeb2%22%7D,%7B%22$oid%22:%22619c553c58029700017ff443%22%7D,%7B%22$oid%22:%22619c55430c1ff000011c1c2c%22%7D]%7D%7D%7D,%7B%22$project%22:%7B%22title%22:1,%22summary%22:1%7D%7D,%7B%22$limit%22:47%7D]').json();
                // console.log("kyResponse", kyResponse);

                this.loading = true;
                // const query = this.query;
            
                if(articleResult.length){
                    let lArticle = articleResult[0];

                    this.preProcessOEmbed();

                    if(lArticle.coverImage?.url){
                        //sometime the file name has space/special chars, use new URL's href prop which encodes the special chars
                        const url = new URL(lArticle.coverImage.url)
                        lArticle.coverImage.url = url.href;

                        lArticle.coverImage.url_1200  = lArticle.coverImage.url.replace(/attachments\.cbd\.int\//, '$&1200x600/')
                    }
                    this.article = lArticle;
                    this.$emit('load', { ...this.article });   
                }
                else {
                    this.$emit('load');
                }

                this.$auth.fetchUser().then(()=>{
                    if(this.showEdit || this.showEdit == 'true' || this.hasOwnProperty(this.showEdit)){

                        let roles = [...this.showEditRoles, 'oasisArticleEditor', 'Administrator']
                        this.hasEditRights = this.$auth.hasScope(roles);
                    }
                })
            }
            catch(e){
                console.error(e)
            }
            finally{
                this.loading = false;
            }
        },
        preProcessOEmbed() {

            setTimeout(function(){

                document.querySelectorAll( 'oembed[url]' ).forEach(async function(element) {
                    var url = element.attributes.url.value;
                    var params = {
                        url : encodeURI(url),
                    }

                    const response = await ky.get('/api/v2020/oembed', {params:params});                    
                    var embedHtml = '<div class="ck-media__wrapper" style="width:100%">' + response.data.html +'</div>'
                    element.insertAdjacentHTML("afterend", embedHtml);
                    
                });

            }, 500)
        }
    },
    filters:{
        lstring
    }
}
</script>
    
<style>

    @import url('https://cdn.jsdelivr.net/npm/@scbd/ckeditor5-build-inline-full@35.0.0/build/content-style.css');

</style>
