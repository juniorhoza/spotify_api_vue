<template>
  <div  >
  <div :class="{overlay:show}" @click.alt="hidely"  @keypress.esc="hidely">.</div>
  <div class="main">.</div>
  <h1 class="main_title">spotify and youtube</h1>
   <input type="text" class="artist" v-model="cocktail" @keypress.enter="Get_coktail" placeholder="Enter an artist name..." autofocus/>
  <div class="container"  v-if="result!= null"   >
  
  
  
  
  <div class="result">
   <ul v-for=" i,index in special_result" :key="index" class="bio">
   <li>
      <p> <a  :href="i.data.uri">{{i.data.uri}}</a></p>
      
      <p class="artis txtcolor" >{{i.data.profile.name}} <img src="./assets/correct(3).png" alt="" v-if="verified==true"> </p>
      <div v-if="i.data.visuals.avatarImage != null">
      
      <img :src="i.data.visuals.avatarImage.sources[0].url" alt="">
      </div>
      <div v-else>
      <img src="./assets/no-image.jpg" alt="">
      </div>
      <div class="bio">
      <h1>Biography</h1>
       <p class="biography" v-if="artistId.profile.biography.text !=null">
       {{artistId.profile.biography.text}}
       </p>
       <p class="biography" v-else>
      no bio
       </p>
      <div class="stats">
             <p class="followers"><span class="foll">followers:</span>  {{artistId.stats.followers.toString().replace(/\B(?<!\.\d*)(?=(\d{3})+(?!\d))/g, ",")}}</p>
              <p class="monthlyListeners"><span class="monthly">monthlyListeners:</span>  {{artistId.stats.monthlyListeners.toString().replace(/\B(?<!\.\d*)(?=(\d{3})+(?!\d))/g, ",")}}</p>
              <div class="links" v-for=" link,index in artistId.profile.externalLinks.items" :key="index">
              <p><a :href="link.url">{{link.name}}</a></p>
              </div>
            
      </div>
        <div class="albumsOfArtist">
               <div class="" v-if="artistId.discography.albums.items.length">
                 <h1>albums</h1>
 
<div   class="albums">
<div class="albu" v-for="album,inde in artistId.discography.albums.items" :key="inde" >

<p><a  :href="album.releases.items[0].uri"> {{album.releases.items[0].name}}</a> </p>
      <a  :href="album.releases.items[0].uri" ><img :src="album.releases.items[0].coverArt.sources[0].url" alt=""></a>


<p> release date :{{album.releases.items[0].date.day}}/{{album.releases.items[0].date.month}}/{{album.releases.items[0].date.year}}</p>
     
  


</div>
</div>
 <div class="popularTracks ">
  <div class="" v-if="albums.length">
<h1>popular tracks</h1>
 
<div   class="popularTrack albums hehe cleafix" >
<div class="albu" v-for="album,inde in artistId.discography.topTracks.items" :key="inde" >

<p><a  :href="album.track.uri"> {{album.track.name}}</a> </p>
 <p>streams: {{(album.track.playcount).toString().replace(/\B(?<!\.\d*)(?=(\d{3})+(?!\d))/g, ",")}}</p>
 <p>duration: {{(album.track.duration.totalMilliseconds/60000).toFixed(2)}} minutes</p>

      <a  :href="album.track.album.uri" ><img :src="album.track.album.coverArt.sources[0].url" alt=""></a>

      <p class="seeLyrics" @click="lyrics(album.track.id,album.track.name)"> see lyrics</p>

</div>

</div>
 
 </div> 
      <div v-if="show"  class="lyrics" @click.alt="hidely" @keypress.esc="hidely">
      <p class="close">Click + Alt to close </p>
      <iframe width="420" height="345" :src="link">
</iframe>
<div class=" ly" >

     <p class="paro" v-for="v,index in paroles" :key="index">{{v}}</p>

</div>
      
      </div>
 </div>

 </div> 

              
              </div>
      
      </div>
   </li>
   </ul>

  </div> 
 <div class="" v-if="albums.length">
<h1>other albums</h1>
 
<div   class="albums">
<div class="albu"></div>
<div class="albu"></div>

<div class="albu"></div>
<div class="albu last"></div>

<div class="albu" v-for="album,inde in albums" :key="inde" >

<p><a  :href="album.data.uri"> {{album.data.name}}</a> </p>
      <a  :href="album.data.uri" ><img :src="album.data.coverArt.sources[0].url" alt=""></a>


<p> year :{{album.data.date.year}}</p>
     
  


</div>
</div>
 </div> 

<div class="track" v-if="tracks.length">
<h1>other Tracks you may like</h1>

<div v-for="track,inde in tracks" :key="inde">
<div class="track">

<p><a  :href="track.data.uri"> {{track.data.name}}</a> </p>
<a  :href="track.data.uri" class="line"><img :src="track.data.albumOfTrack.coverArt.sources[0].url" alt=""></a>

<p>album : <a :href="track.data.albumOfTrack.uri">{{track.data.albumOfTrack.name}}</a></p>
      
<p>duration : {{(track.data.duration.totalMilliseconds/60000).toFixed(2) }} Minutes</p>
     
  <p class="seeLyrics" @click="lyrics(track.data.id,track.data.name)"> see lyrics</p>


</div>
</div>
</div>



  </div>
  <div class="con" v-else >
      <p>no artist found</p>
  
  </div>
  </div>


</template>

<script>
import { ref } from '@vue/reactivity'


export default {
  name: 'App',
  setup(){
    let cocktail= ref('')
    let result= ref({})
    let special_result=ref([])
    let albums=ref([])
    let tracks=ref([])
    let artistId=ref('')
    const backColor=ref({})
    let TrackId=ref('')
    let tracklyrics=ref([])
    let actualLyrics=ref('')
    let show=ref(false)
    let verified=ref(false)
    let foward=ref('')
    let back=ref('')
    let VidId=ref('')
    let data_YT=ref([])
    let link=ref('')
    let paroles=ref([])
 const options = {
	method: 'GET',
	headers: {
		'X-RapidAPI-Key': 'c8404eb6cdmsh0cd6a4a9c680db3p185f06jsnf94f13743dd4',
		'X-RapidAPI-Host': 'spotify23.p.rapidapi.com'
	}
};
const options2 = {
	method: 'GET',
	headers: {
		'X-RapidAPI-Key': 'c8404eb6cdmsh0cd6a4a9c680db3p185f06jsnf94f13743dd4',
		'X-RapidAPI-Host': 'youtube138.p.rapidapi.com'
	}
};
   function Get_coktail(){
     result.value={}
     special_result.value=[]
     albums.value=[]
     tracks.value=[]
     verified.value=false
    

        
fetch(`https://spotify23.p.rapidapi.com/search/?q=${cocktail.value}&type=multi&offset=0&limit=1&numberOfTopResults=10`, options)
	.then(response => response.json())
	.then(response => {
        result.value=response
        console.log(result.value)
        special_result.value=result.value.artists.items
        artistId.value=special_result.value[0].data.uri
        artistId.value=artistId.value.split(":")[2]
        console.log(artistId.value)
        fetch(`https://spotify23.p.rapidapi.com/artist_overview/?id=${artistId.value}`, options)
	.then(response => response.json())
	.then(response => {
       artistId.value=response.data.artist
       verified.value=artistId.value.profile.verified
       console.log(verified.value)
          backColor.value=artistId.value.visuals.headerImage.extractedColors.colorRaw.hex
          console.log(backColor.value)
        


  })

        fetch(`https://spotify23.p.rapidapi.com/search/?q=${cocktail.value}&type=multi&offset=0&limit=19&numberOfTopResults=10`, options)
	.then(response => response.json())
	.then(response => {
        result.value=response
               albums.value=result.value.albums.items
               tracks.value=result.value.tracks.items
        


console.log(tracks.value)
  })


  })
	.catch(err => console.error(err));
   }
function lyrics(Id,name){
   actualLyrics.value=" "
     VidId.value=""
     link.value=" "
     paroles.value=[]
          actualLyrics.value=" "
          show.value=true
  fetch(`https://spotify23.p.rapidapi.com/track_lyrics/?id=${Id}`, options)
	.then(response => response.json())
	.then(response => {
    tracklyrics.value=response.lyrics.lines
    
    tracklyrics.value.forEach((lyric)=>{
      actualLyrics.value+=lyric.words+"\n"
      if(lyric.words!=" "||false||null||""){
        paroles.value.push(lyric.words)
        
      }
      })
	
   
    console.log(paroles.value)
      console.log(name)



fetch(`https://youtube138.p.rapidapi.com/search/?q=${name}&hl=en&gl=US`, options2)
	.then(response => response.json())
	.then(response =>{
    data_YT.value=response
    console.log("from youtube")
    console.log(data_YT.value)
    VidId.value=data_YT.value.contents[0].video.videoId
    link.value=`https://www.youtube.com/embed/${VidId.value}`
    console.log(link.value)
    console.log(VidId.value)
  })
	.catch(err => console.error(err));
    })
}
function hidely(){
  show.value=false
}


   return{
     Get_coktail,tracklyrics,options2,
     cocktail,result,options,special_result,albums,tracks,artistId,backColor,TrackId,lyrics,actualLyrics,show,hidely,verified,foward,back,data_YT,VidId,link,paroles
   }
  }
  
}
</script>

<style>
html{
  scroll-behavior: smooth;
}
*{
  font-family:"poppins";
  font-size: 16px;
}
iframe{
  width: 70vw;
  height:80vh;
  
}
.lyrics{
width: 80vw;
height:100vh;
  position: relative;
  top: 50%;
  left: 50%;
  display:flex;
  flex-direction: column;
  gap: 30px;
  color: white;
  transform: translate(-50%,-50%);
  -ms-transform: translate(-50%,-50%);
  overflow:auto;
  
  z-index: 10;
}
.overlay{
  
  position:fixed;
  width: 100vw; /* Full width (cover the whole page) */
  height: 100vh; /* Full height (cover the whole page) */
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  color: #111111;
   background-color: rgba(5, 5, 5, 0.945); /* Black background with opacity */
  z-index: 5; /* Specify a stack order in case you're using a different order for other elements */
  cursor: pointer; /* Add a pointer on hover */
}
.ly{
  height:100%;
  font-size:19px;
  
  top: 50%;
  left: 50%;
  display:flex;
  flex-direction: column;;
  gap: 15px;
  color: white;
  
  margin-top: 650px;
  transform: translate(-50%,-50%);
  -ms-transform: translate(-50%,-50%);
  position: absolute;
}
.clearfix{overflow:hidden}
.clearfix:after {
clear: both;
content: " ";
display: block;

}
.paro{
  
}
.biography{
  text-align: left;
  font-size:14px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  
  
}
.stats{
  text-align: left;
}
.stats>p>span{
  font-weight:700;
text-transform: capitalize;
}
.links{
  display: flex;
  gap:10px;
}
.main{
  width: 70vw;
  height:20%;
  margin: auto;
  background:v-bind(backColor);
  color: v-bind(backColor);
}
.txtcolor{
  color: v-bind(backColor);

}
a{
  text-decoration: none;
  color:rgb(20, 82, 5);
}
.albu{
margin-bottom:10px;
border-radius: 10px;

transition:0.3s all ease-in-out;
}
.albu:hover{

  box-shadow:  20px 20px 60px #bebebe,
             -20px -20px 60px #111111;
  background: black;
}
.albu:hover>p,.albu:hover>p>a{

  color:white;
}
.close{
  font-size: 30px;
  color:white;
  margin-bottom: 90px;
  text-transform: capitalize;
}
li{
  list-style-type:none;
}
.last{
width: 900px;
height: 80px;
}
.artist{
  width: 80vw;
  height: 8vh;
}
.popularTrack{
  position: relative;
  width: 120%;
}

.albums{
 width: 110vw;
margin-right: auto;
position: relative;
left: -10%;
  display:flex;
  flex-direction:row;
  align-items: center;
  justify-content: center;
  gap:10px ;
  
  overflow-x: auto;
  
margin-bottom: 100px;
}

.main_title{
  font-size: 50px;
}
.artis{
font-size: 35px ;
font-weight: bold;
}
.track{
  position: relative;
}
.line::before{
content:"";
position:absolute;
top: 20px;
width: 0;
right:619px;
height:1px;
background-color:white;
transition:.7s all ease-in-out;
}
.line:hover::before{
  top: 20px;
width: 80px;
right:619px;
height:1px;
background-color:rgb(20, 82, 5);
}
.bio{
  position: relative;
}
input{
border-radius:3px ;
background:black;
color:white;
text-align: center;


}
input:focus{
  border:2px solid rgb(51, 184, 17) ;
}


</style>
