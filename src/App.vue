<template>
  <div>
    <div class="container mt-5">
      <div v-if="tab==1">
        <div class="card">
          <div class="card-body">
            <center>
              <h3>TEPIFY.HEROKUAPP.COM</h3>
              <button @click="loginUser()" type="button" class="btn mt-2 btn-spotify btn-lg "><i
                  class="fab fa-spotify"></i> ลงชื่อเข้าใช้ด้วย Spotify</button>
              <p class="mt-3">
                Modify By: Teerut<br>
                Some Source Code By: Michellexliu
              </p>
            </center>
          </div>
        </div>

      </div>
      <div v-if="tab==2">

        <div class="row">
          <div class="col-sm">
            <div class="card text-left">
              <img class="card-img-top" src="holder.js/100px180/" alt="">
              <div class="card-body">
                <h4 class="card-title"><i class="fas fa-list-ul"></i> รายการ Playlist</h4>
                <p class="card-text"><button @click="listPlaylists()" type="button" class="btn btn-co1 btn-block"><i
                      class="fas fa-external-link-alt"></i></button></p>
              </div>
            </div>
          </div>
          <!-- <div class="col-sm">
            <div class="card text-left">
              <img class="card-img-top" src="holder.js/100px180/" alt="">
              <div class="card-body">
                <h4 class="card-title">Playlist's Items</h4>
                <p class="card-text"><button @click="playListItem()" type="button" class="btn btn-primary"><i
                      class="fas fa-external-link-alt"></i></button></p>
              </div>
            </div>
          </div> -->
          <div class="col-sm">
            <div class="card text-left">
              <img class="card-img-top" src="holder.js/100px180/" alt="">
              <div class="card-body">
                <h4 class="card-title"><i class="far fa-clock"></i> แทร็กที่เล่นล่าสุด</h4>
                <p class="card-text"><button @click="recentlyPlayed()" type="button" class="btn btn-co1 btn-block"><i
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
        <table class="table mt-3 table-striped table-bordered">
          <thead>
            <tr>
              <th>#</th>
              <th>รูปภาพ</th>
              <th>ชื่อ Playlist</th>
              <th>จำนวนเพลงที่อยู่ใน Playlist</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item,i) in dataObj" :key="item">
              <td>{{i+1}}</td>
              <td v-if="item.images[0]" style="padding: 10px;"><img :src="item.images[0].url" width="90" height="90" alt="" srcset=""></td>
              <td v-else style="padding: 10px;"><img src="" alt="" srcset=""></td>
              <td>{{item.name}}</td>
              <td>{{item.tracks.total}}</td>
              <td><button type="button" @click="openMusicList(item.id,item.owner.display_name,item.name,i+1)"
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
          v-bind:playListTitle="compoSend.playListTitle" v-bind:order="order"/>
      </div>
      <div v-if="tab==5">
        <center>
          <button type="button" @click="tab=3" class="btn btn-danger mr-1">Back</button>
        </center>
        <MyPlaylistGenerator v-bind:dataObj="dataObj" v-bind:qrCode="dataObj[0].owner.display_name"
          v-bind:sumItem="compoSend.sumItem" />
      </div>
      <div v-if="tab==6">
        <center>
          <button type="button" @click="tab=2" class="btn btn-danger mr-1">Back</button>
          
        </center>
        <RecentlyPlayed v-bind:display_name="compoSend.display_name" v-bind:access_token="access_token"/>
      </div>
    </div>
  </div>
</template>

<script>
  import ListMusicPlaylist from './components/ListMusicPlaylist.vue'
  import MyPlaylistGenerator from './components/MyPlaylistGenerator.vue'
  import RecentlyPlayed from './components/RecentlyPlayed'
  var axios = require('axios');
  // const self = this;

  export default {
    name: 'App',
    components: {
      ListMusicPlaylist,
      MyPlaylistGenerator,
      RecentlyPlayed
    },
    data() {
      return {
        order:0,
        access_token: "",
        refresh_token: "",
        tab: 1,
        dataObj: [],
        compoSend: {
          id: "",
          ownerPlaylist: "",
          textQrCode: "",
          playListTitle: "",
          sumItem: 0,
          display_name:""
        }
      }
    },
    methods: {
      ItemSum() {
        for (let index = 0; index < this.dataObj.length; index++) {
          this.compoSend.sumItem = this.compoSend.sumItem + this.dataObj[index].tracks.total
        }
      },
      changeTab(tab) {
        this.tab = tab
        this.dataObj = []
      },
      openMusicList(id, ownerPlaylist, playListTitle,order) {
        this.tab = 4
        this.compoSend.id = id
        this.compoSend.ownerPlaylist = ownerPlaylist
        this.compoSend.playListTitle = playListTitle
        this.order = order
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
        this.tab = 6
        var config = {
          method: 'get',
          url: 'https://api.spotify.com/v1/me',
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json',
            'Authorization': 'Bearer '+this.access_token
          }
        };
        var self = this;
        axios(config)
          .then(function (response) {
            self.compoSend.display_name = response.data.display_name
          })
          .catch(function (error) {
            console.log(error);
          });
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
<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Kanit:wght@400&display=swap');

  .card {
    font-family: 'Kanit', sans-serif;
    margin-top: 10%;
  }

  .btn-spotify {
    background-color: #1DD05D;
    color: white;
  }

  .btn-co1 {
    background-color: #1DD05D;
    color: white;
  }

  /* .container{
      background-image: linear-gradient(90deg, rgba(25, 136, 247, 1) 0%, rgba(247, 25, 136, 1) 100%);
    border-radius: 11px;
    


    } */
</style>