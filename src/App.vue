<template>
  <div>
    <div class="container mt-5">
      <div v-if="tab==1">
        <center>
          <h2>Login เพื่อใช้แอป</h2>
          <button @click="loginUser()" type="button" class="btn btn-primary btn-lg">Log in with Spotify</button>
        </center>
      </div>
      <div v-if="tab==2">
        
        <div class="row">
          <div class="col-sm">
            <div class="card text-left">
              <img class="card-img-top" src="holder.js/100px180/" alt="">
              <div class="card-body">
                <h4 class="card-title">List of Playlists</h4>
                <p class="card-text"><button @click="listPlaylists()" type="button" class="btn btn-primary"><i
                      class="fas fa-external-link-alt"></i></button></p>
              </div>
            </div>
          </div>
          <div class="col-sm">
            <div class="card text-left">
              <img class="card-img-top" src="holder.js/100px180/" alt="">
              <div class="card-body">
                <h4 class="card-title">Playlist's Items</h4>
                <p class="card-text"><button @click="playListItem()" type="button" class="btn btn-primary"><i
                      class="fas fa-external-link-alt"></i></button></p>
              </div>
            </div>
          </div>
          <div class="col-sm">
            <div class="card text-left">
              <img class="card-img-top" src="holder.js/100px180/" alt="">
              <div class="card-body">
                <h4 class="card-title">Recently Played Tracks</h4>
                <p class="card-text"><button @click="recentlyPlayed()" type="button" class="btn btn-primary"><i
                      class="fas fa-external-link-alt"></i></button></p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="table-responsive mt-2" v-if="tab==3">
        <center>
          <button @click="changeTab(2)" type="button" class="btn btn-danger mr-1">Back</button>
          <button type="button" @click="tab=5" class="btn btn-primary ml-1">My Playlist Generator</button>
        </center>
        <table class="table mt-3 table-striped">
          <thead class="thead-inverse">
            <tr>
              <th>#</th>
              <th>Image</th>
              <th>name</th>
              <th>Item</th>
              <th>action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item,i) in dataObj" :key="item">
              <td>{{i+1}}</td>
              <td style="padding: 0px;"><img :src="item.images[0].url" width="90" height="90" alt="" srcset=""></td>
              <td>{{item.name}}</td>
              <td>{{item.tracks.total}}</td>
              <td><button type="button" @click="openMusicList(item.id,item.owner.display_name,item.name)"
                  class="btn btn-primary">Track In Playlist Generator</button></td>
            </tr>
          </tbody>
        </table>
      </div>
      <div v-if="tab==4">
        <center>
          <button type="button" @click="tab=3" class="btn btn-danger mr-1">Back</button>


        </center>
        <ListMusicPlaylist class="mt-3" v-bind:access_token="access_token" v-bind:id="compoSend.id"
          v-bind:ownerPlaylist="compoSend.ownerPlaylist" v-bind:textQrCode="compoSend.textQrCode"
          v-bind:playListTitle="compoSend.playListTitle" />
      </div>
      <div v-if="tab==5">
        <center>
          <button type="button" @click="tab=3" class="btn btn-danger mr-1">Back</button>
        </center>
        <MyPlaylistGenerator v-bind:dataObj="dataObj" v-bind:qrCode="dataObj[0].owner.display_name" v-bind:sumItem="compoSend.sumItem"/>
      </div>
    </div>
  </div>
</template>

<script>
  import ListMusicPlaylist from './components/ListMusicPlaylist.vue'
  import MyPlaylistGenerator from './components/MyPlaylistGenerator.vue'
  var axios = require('axios');
  // const self = this;

  export default {
    name: 'App',
    components: {
      ListMusicPlaylist,
      MyPlaylistGenerator
    },
    data() {
      return {
        access_token: "",
        refresh_token: "",
        tab: 1,
        dataObj: [],
        compoSend: {
          id: "",
          ownerPlaylist: "",
          textQrCode: "",
          playListTitle: "",
          sumItem:0
        }
      }
    },
    methods: {
      ItemSum(){
        for (let index = 0; index < this.dataObj.length; index++) {
              this.compoSend.sumItem = this.compoSend.sumItem+this.dataObj[index].tracks.total
            }
      },
      changeTab(tab) {
        this.tab = tab
        this.dataObj = []
      },
      openMusicList(id, ownerPlaylist, playListTitle) {
        this.tab = 4
        this.compoSend.id = id
        this.compoSend.ownerPlaylist = ownerPlaylist
        this.compoSend.playListTitle = playListTitle
      },
      // rediretUrl(url){
      //   window.open(url);
      // },
      listPlaylists() {
        
        this.tab = 3
        var config = {
          method: 'get',
          url: 'https://api.spotify.com/v1/me/playlists',
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json',
            'Authorization': 'Bearer ' + this.access_token
          }
        };
        var self = this;
        axios(config)
          .then(function (response) {
              self.dataObj = response.data.items
              self.compoSend.ownerPlaylist = response.data.items
              // console.log(response.data)
              self.ItemSum();

          })
          .catch(function (error) {
            window.location = "login";
            // self.tab = 1
            console.log(error);
          });
      },
      playListItem() {

      },
      recentlyPlayed() {

      },

      loginUser() {
        window.location = "login";
      },
      getParam() {
        let uri = window.location.href.split('#');
        if (uri.length == 2) {
          let vars = uri[1].split('&');
          let getVars = {};
          let tmp = '';
          vars.forEach(function (v) {
            tmp = v.split('=');
            if (tmp.length == 2)
              getVars[tmp[0]] = tmp[1];
          });
          this.access_token = getVars.access_token
          this.refresh_token = getVars.refresh_token
        }
      }
    },
    created() {
      this.getParam();
      if (this.access_token != "") {
        this.tab = 2
      } else {
        this.tab = 1
      }

    },
  }
</script>