<template>
  <main>
    <section class="min-h-screen w-full flex flex-col items-center">
      <div
        class="w-full h-80 bg-black bg-[url('/images/pattern-bg-desktop.png')] flex flex-col justify-center items-center gap-y-12 bg-cover">
        <div>
          <h2 class="text-white text-3xl">Ip Address Tracker</h2>
        </div>
        <div class="flex justify-center items-end w-full relative">
          <input @keydown.enter="getAddres" v-model="ipUser" type="text"
            class="h-14 w-[75%] lg:w-[25%] rounded-l-[15px] p-5" placeholder="search Ip Address" />
          <div @click="getAddres" class="w-12 h-14 bg-black rounded-r-[15px] flex justify-center items-center">
            <img class="text-white w-5 h-5" src="/images/icon-arrow.svg" alt="" />
          </div>

          <section
            class="w-[60%] h-auto lg:h-32 border absolute bg-white top-[90%] translate-y-[50%] z-50 rounded-[15px] flex flex-col lg:flex-row gap-x-10 justify-between items-center pl-10">
            <div class="w-1/4 flex flex-col justify-center items-start gap-2">
              <span class="text-gray-400 text-lg font-bold">Ip Address</span>
              <p class="font-bold text-xl">{{ ipAddress && ipAddress.ip ? ipAddress.ip : "-" }}</p>
            </div>
            <div class="w-1/4 flex flex-col justify-center items-start gap-2 border-l-[3px] px-3">
              <span class="text-gray-400 text-lg font-bold">LOCATION</span>
              <p class="font-bold text-xl">
                {{ ipAddress && ipAddress.location && ipAddress.location.country ? ipAddress.location.country : "-" }}
              </p>
            </div>
            <div class="w-1/4 flex flex-col justify-center items-start gap-2 border-l-[3px] px-3">
              <span class="text-gray-400 text-lg font-bold">TIMEZONE</span>
              <p class="font-bold text-xl">
                {{ ipAddress && ipAddress.location && ipAddress.location.timezone ? ipAddress.location.timezone + " UTC" :
                  "-" }}
              </p>
            </div>
            <div class="w-1/4 flex flex-col justify-center items-start gap-2 border-l-[3px] px-3">
              <span class="text-gray-400 text-lg font-bold">ISP</span>
              <p class="font-bold text-xl">{{ ipAddress && ipAddress.isp ? ipAddress.isp : '-' }}</p>
            </div>
          </section>

        </div>
      </div>
      <!-- ///////////////////////////////////////map Ip /////////////////////// -->
      <div id="map" class="w-full min-h-screen z-10"></div>
    </section>
  </main>
</template>

<script>
import axios from "axios";
import jsonCountry from "../countries.json";
export default {
  name: "MyAwesomeMap",
  components: {},
  data() {
    return {
      map: null,
      ipAddress: null,
      ipUser: "",
      marker: null,
      iconMarker: "/images/icon-location.svg",
      keyCountry: jsonCountry, /////////// flile Json
      mapUser: null,
      longMap: null,
      latiMap: null,
      icon: '',
      userMarkerClick: null,
      mapUser: null,
      latLngLocal: {
        lat: '',
        lng: '',
      },
      userFullAdressMap: [],
      mapUserLocal:'',
     
    };
  },
  mounted() {
    this.map = L.map("map").setView([51.505, -0.09], 13);
    L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
      maxZoom: 19,
      attribution:
        '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
    }).addTo(this.map);

    this.map.on('click', this.userClick)

    
    
    this.mapUserLocal = JSON.parse(localStorage.getItem('userAddLat'))
    
    // console.log(this.mapUserLocal)
    // this.userFullAdressMap.lat = localStorage.getItem("userAddLat")
    // this.userFullAdressMap.lng = localStorage.getItem("userAddLag")

    // console.log(this.userFullAdressMap.latLngUs , this.userFullAdressMap.lngLangUs);

    // this.marker = L.marker([this.userFullAdressMap.lat, this.userFullAdressMap.lng], this.icon).addTo(this.map);
    // console.log(this.userFullAdressMap);
    this.userForECH()

  },

  computed: {

  },
  methods: {
    test(){
      console.log(this.mapUserLocal)
    },
    userClick(e) {


      this.icon = L.icon(this.iconMarker);
      this.marker = L.marker([e.latlng.lat, e.latlng.lng], this.icon).addTo(this.map);
      this.latLngLocal.lat = e.latlng.lat
      this.latLngLocal.lng = e.latlng.lng

      this.userFullAdressMap.push(this.latLngLocal)
      this.latLngLocal = {
        lat: '',
        lng: '',
      }
      // if(this.latLngLocal.lat && this.latLngLocal.lng){
      //   this.userFullAdressMap.push(this.latLngLocal)
      // }


      localStorage.setItem('userAddLat', JSON.stringify(this.userFullAdressMap))
      
      // localStorage.setItem('userAddLat',JSON.stringify(this.latLocal))
      // localStorage.setItem('userAddLag', JSON.stringify(this.lngLocal))
      // let v = JSON.parse(localStorage.getItem('userAddLat'))
      // let v2 = JSON.parse(localStorage.getItem('userAddLag'))
      // console.log(v, v2);


    },
    getAddres() {
      axios
        .get(`https://geo.ipify.org/api/v1`, {
          params: {
            domain: this.ipUser,
            apiKey: "at_dnDSnc3A5F0CqslsnFgXzG6R3wahs",
          },
        })
        .then((response) => {
          this.ipAddress = response.data;
          // let x = this.keyCountry.filter((e)=>e.key==this.posts.location.country)////// filter file Json
          this.longMap = this.ipAddress.location.lng
          this.latiMap = this.ipAddress.location.lat
          this.icon = L.icon(this.iconMarker);

          // duplicated code  ///////////creat function
          if (this.marker) {
            this.map.removeLayer(this.marker)
            this.creatMarker()
          } else {
            this.creatMarker()
          }



        }).catch(err => {
          console.log(err);
          this.ipUser = ''
          alert(err.response.data.messages);

        });

    },

    creatMarker() {
      this.marker = L.marker([this.latiMap, this.longMap], this.icon).addTo(this.map);
      this.map.setView([this.latiMap, this.longMap]);
    },

    userForECH() {
      this.mapUserLocal.forEach(element => {
        
        this.marker = L.marker([element.lat, element.lng], this.icon).addTo(this.map).on('click', element=>element.target.remove());
        console.log(this.marker);
      });
    }




  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>