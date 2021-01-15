<template>
    <div class="ListMusicPlaylist">
        <div id="receipt">
            <div class="receiptContainer">
                <h2 class="logo">
                    RECEIPTIFY
                </h2>
                <p class="period">
                    My PlayList Track
                </p>
                
                <p class="date">
                    OWNER: {{UpperCase(ownerPlaylist)}}
                </p>
                <p class="date">
                    PLAYLISTTITLE: {{UpperCase(playListTitle)}}
                </p>
                <p class="date">
                    {{thaiDate().dayThai}}, {{thaiDate().monthThai}} {{thaiDate().dayNum}}, {{thaiDate().yearEN}}
                </p>
                
                <table class="tracks">
                    <thead>
                        <tr>
                            <td class="begin">
                                QTY
                            </td>
                            <td>
                                ITEM
                            </td>
                            <td class="length">
                                <center>AMT</center>
                            </td>
                        </tr>
                    </thead>

                    <tbody>
                        <tr v-for="(item,i) in dataObj" :key="item">
                            <!-- <td v-if="i.length < 2" class="begin">
                                df{{i+1}} werwer
                            </td> -->
                            <td class="begin">
                                {{getlength(i+1)}}
                            </td>
                            <td class="name">
                                {{UpperCase(item.track.name)}} -


                                <span>
                                    {{UpperCase(item.track.artists[0].name)}}
                                </span>

                            </td>
                            <td class="length">
                                <center>{{millisToMinutesAndSeconds(item.track.duration_ms)}}</center>
                            </td>
                        </tr>
                        <tr class="total-counts">
                            <td class="begin" colspan="2">
                                ITEM COUNT:
                            </td>
                            <td class="length">
                                {{getlength(countMusic)}}
                            </td>
                        </tr>
                        <tr class="total-counts-end">
                            <td class="begin" colspan="2">
                                TOTAL:
                            </td>
                            <td class="length">
                                {{millisToMinutesAndSeconds(sumDuration_ms)}}
                            </td>
                        </tr>
                    </tbody>
                </table>
                <p class="date">
                    CARD #: **** **** **** {{thaiDate().yearEN}}
                </p>
                <p class="date">
                    AUTH CODE: {{UpperCase(id)}}
                </p>
                <p class="date">
                    OWNER: {{UpperCase(ownerPlaylist)}}
                </p>
                <div class="thanks">
                    <p>
                        ขอบคุณสำหรับการเยี่ยมชม!!
                    </p>
                    <img width="90" height="90" :src="qrCode" alt="" srcset="">
                    <p class="website">
                    </p>
                </div>
            </div>
        </div>
            
            <center><button type="button mt-3" @click="capShot()" class="btn btn-primary ml-1">Download</button></center>
            

    </div>
</template>

<script>
    var html2canvas = require('html2canvas');
    var axios = require('axios');
    var QRCode = require('qrcode')
    

    export default {
        name: 'ListMusicPlaylist',
        props: {
            access_token: String,
            id: String,
            ownerPlaylist: String,
            playListTitle:String
        },
        data() {
            return {
                dataObj: [],
                sumDuration_ms: 0,
                countMusic: 0,
                qrCode: ""
            }
        },
        mounted() {
            this.genOrCode(this.id)
        },
        methods: {
            getlength(num){
                if ((num.toString()).length < 2) {
                    return "0"+num
                }else{
                    return num
                }
                
            },
            genOrCode(text) {
                var opts = {
                    errorCorrectionLevel: 'H',
                    type: 'image/png',
                    quality: 0.3,
                    margin: 0,
                    color: {
                    }
                }
                QRCode.toDataURL(text,opts)
                    .then(url => {
                        this.qrCode = url
                    })
            },
            thaiDate() {
                var now = new Date();
                var thday = new Array("อาทิตย์", "จันทร์",
                    "อังคาร", "พุธ", "พฤหัส", "ศุกร์", "เสาร์");
                var thmonth = new Array("มกราคม", "กุมภาพันธ์", "มีนาคม",
                    "เมษายน", "พฤษภาคม", "มิถุนายน", "กรกฎาคม", "สิงหาคม", "กันยายน",
                    "ตุลาคม", "พฤศจิกายน", "ธันวาคม");
                    var obj = {
                        dayThai:thday[now.getDay()],
                        dayNum:now.getDay(),
                        monthThai:thmonth[now.getMonth()],
                        monthNum:now.getMonth(),
                        yearThai:now.getFullYear()+543,
                        yearEN:now.getFullYear()
                    }
                return obj
            },
            // thaiDate() {
            //     var now = new Date();
            //     var thday = new Array("อาทิตย์", "จันทร์",
            //         "อังคาร", "พุธ", "พฤหัส", "ศุกร์", "เสาร์");
            //     var thmonth = new Array("มกราคม", "กุมภาพันธ์", "มีนาคม",
            //         "เมษายน", "พฤษภาคม", "มิถุนายน", "กรกฎาคม", "สิงหาคม", "กันยายน",
            //         "ตุลาคม", "พฤศจิกายน", "ธันวาคม");
            //     return thday[now.getDay()] + ", " + thmonth[now.getMonth()] + " " + now.getDay() + ", " + now.getFullYear()
            // },
            UpperCase(text) {
                return text.toUpperCase();
            },
            capShot() {
                var offScreen = document.querySelector(".receiptContainer");
                window.scrollTo(0, 0);
                var clone = this.hiddenClone(offScreen);
                // Use clone with htm2canvas and delete clone
                html2canvas(clone, {
                    scrollY: -window.scrollY
                }).then((canvas) => {
                    var dataURL = canvas.toDataURL("image/png", 1.0);
                    document.body.removeChild(clone);
                    var link = document.createElement("a");
                    // console.log(dataURL);
                    link.href = dataURL;
                    link.download = 'MyPlayListTrack.png';
                    document.body.appendChild(link);
                    link.click();
                    document.body.removeChild(link);
                });
            },
            hiddenClone(element) {
                // Create clone of element
                var clone = element.cloneNode(true);

                // Position element relatively within the
                // body but still out of the viewport
                var style = clone.style;
                style.position = "relative";
                style.top = window.innerHeight + "px";
                style.left = 0;
                // Append clone to body and return the clone
                document.body.appendChild(clone);
                return clone;
            },
            millisToMinutesAndSeconds(millis) {
                var minutes = Math.floor(millis / 60000);
                var seconds = ((millis % 60000) / 1000).toFixed(0);
                // if (minutes > 60) {
                //     // var house = (minutes* 0.016667).toFixed(2)

                //     // return house+" : "+minutes + ":" + (seconds < 10 ? '0' : '') + seconds;
                // } else {
                //     return minutes + ":" + (seconds < 10 ? '0' : '') + seconds;
                // }
                return minutes + ":" + (seconds < 10 ? '0' : '') + seconds;
            },
            getData() {
                var config = {
                    method: 'get',
                    url: 'https://api.spotify.com/v1/playlists/' + this.id + '/tracks',
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

                        for (let index = 0; index < response.data.items.length; index++) {
                            self.countMusic = self.countMusic + 1
                            self.sumDuration_ms = self.sumDuration_ms + response.data.items[index].track
                                .duration_ms;
                        }
                    })
                    .catch(function (error) {
                        window.location = "login";
                        console.log(error);
                    });
            }
        },
        created() {
            this.getData()
        }
    }
</script>
<style scoped>
    @import url("https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600&display=swap");
    @import url("https://fonts.googleapis.com/css2?family=Anonymous+Pro&display=swap");
    @import url('https://fonts.googleapis.com/css2?family=Kanit:wght@300&display=swap');

    h1 {
        /* font-family: "Poppins", sans-serif; */
        font-family: 'Kanit', sans-serif;
        font-weight: 600;
        font-size: 4.5rem;
        text-align: center;
        margin-bottom: 0px;
    }

    h2 {
        font-family: 'Kanit', sans-serif;
        font-size: 2.5rem;
        margin-top: 0px;
    }

    p {
        font-family: Helvetica, Geneva, Tahoma, sans-serif;
    }

    .logo {
        font-family: "Helvetica Neue", Helvetica, sans-serif;
        font-weight: 600;
        font-size: 2.5rem;
        text-align: center;
        padding-top: 15px;
    }

    .info {
        font-family: "Helvetica Neue", Helvetica, sans-serif;
        text-align: center;
    }

    .period {
        /* font-family: "Helvetica Neue", Helvetica, sans-serif;
         */
         font-family: 'Kanit', sans-serif;
        font-size: 1.5rem;
        text-align: center;
        padding-bottom: 15px;
    }

    .container {
        width: 80%;
        margin: auto;
        display: flex;
        flex-direction: column;
        justify-content: center;
    }

    .login-btn {
        margin: 2vh auto;
        text-align: center;
        text-decoration: none;
        padding: 2vh 5vh;
        background-color: #1db954;
        font-size: 20px;
        cursor: pointer;
        border-radius: 10px;
        color: white;
        font-family: "Poppins", Helvetica, Geneva, Tahoma, sans-serif;
    }

    .time-btn {
        text-align: center;
        text-decoration: none;
        padding: 1vh 5vh;
        background-color: #0056ff;
        font-size: 20px;
        cursor: pointer;
        border-radius: 10px;
        color: white;
        font-family: "Poppins", Helvetica, Geneva, Tahoma, sans-serif;
        margin: 0vh 1vh;
        width: auto;
    }

    .login-btn:hover {
        text-decoration: none;
        color: white;
    }

    #options {
        margin: 0px auto;
        display: flex;
        flex-direction: row;
        justify-content: center;
    }

    .hidden {
        display: none;
    }

    #loggedin,
    #login {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .date {
        margin: 0vh;
    }

    .tracks {
        margin: 5px 0vh;
    }

    #receipt {
        clear: both;
        display: flex;
        flex-direction: column;
        align-items: center;
        align-content: center;
        justify-content: center;
        margin: 25px auto;
    }

    .username-input {
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: center;
        padding: 2vh;
    }

    .username-input * {
        margin: 2%;
    }

    .table-responsive {
        background-image: url("../assets/wrinkled-paper-texture-7.jpg");
    }

    .receiptContainer {
        
        background-image: url("../assets/wrinkled-paper-texture-7.jpg");
        background-position: center;
        position: center;
        width: 340px;
        filter: brightness(110%);
        padding: 20px;
    }

    .begin {
        font-size: 15px;
        padding-left: 0vh;
    }

    #download {
        margin-top: 2vh;
    }

    p,
    table,
    .date {
        font-family: 'Kanit', sans-serif;
        /* font-family: "Anonymous Pro", "Courier New", Courier, monospace; */
    }

    table {
        border: none;
        font-size: 1.5rem;
    }

    .total-counts {
        border-top: 1px dashed;
    }

    .total-counts-end {
        border-bottom: 1px dashed;
    }

    thead {
        border-top: 1px dashed;
        border-bottom: 1px dashed;
    }

    td {
        font-size: 15px;
        padding: 2px 10px;
        vertical-align: top;
    }

    .name {
        font-size: 15px;
        width: 225px;
        overflow: auto;
    }

    .length {
        text-align: right;
        padding-left: 10px;
        padding-right: 0px;
    }

    .website {
        margin-bottom: 0px;
    }

    .thanks {
        text-align: center;
        padding: 15px 0px;
    }



    @media only screen and (min-width: 768px) and (max-width: 1280px) {

        #loggedin,
        #login {
            width: 100%;
        }

        .container {
            width: 100%;
        }
    }

    @media only screen and (max-width: 767px) {
        #options {
            flex-direction: column;
        }

        #loggedin,
        #login {
            width: 100%;
        }

        .time-btn {
            margin: 1vh auto;
        }

        .container {
            width: 100%;
        }
    }
</style>