<template>
        <div v-if="loading" class="preloader">
    <div class="preloader__image">
    </div></div>

  <div v-else  class="container mtb">
    <div class="row">
      <div class="col-lg-8">
        <div class="buy" data-aos="zoom-in" data-aos-delay="300">
            <div class="ticket">Дата бронювання: {{format_date(ticket.associatedRecords.find(x=>x.creationDate).creationDate)}}</div>
            <div  v-for="segm in ticket.flightOffers[0].itineraries[0].segments" :key="ticket.flightOffers[0].id">
                <div class="ticket">Звідки: {{segm.departure.iataCode}}</div>
            <h4 class="ticket">Відїзд: {{format_date(segm.departure.at)}}</h4>
            <div class="ticket">Куди: {{segm.arrival.iataCode}}</div>
            <h4 class="ticket">Прибуття: {{format_date(segm.arrival.at)}}</h4>
                </div>
             <hr v-if="ticket.flightOffers[0].itineraries[1]">
             <div v-if="ticket.flightOffers[0].itineraries[1]" v-for="segm in ticket.flightOffers[0].itineraries[1].segments" :key="ticket.flightOffers[0].id">

            <div class="ticket">Звідки: {{segm.departure.iataCode}}</div>
            <h4 class="ticket">Відїзд: {{format_date(segm.departure.at)}}</h4>
            <div class="ticket">Куди: {{segm.arrival.iataCode}}</div>
            <h4 class="ticket">Прибуття: {{format_date(segm.arrival.at)}}</h4>
        </div>
        </div>


        <div  class="buy" data-aos="zoom-in" data-aos-delay="300">
                <div  >
            <h3 class="ticket">Id: {{ticket.travelers[tickets_i['tr']].id}}</h3>
            <h3 class="ticket">Імя: {{ticket.travelers[tickets_i['tr']].name.firstName+" "+ticket.travelers[tickets_i['tr']].name.lastName}}</h3>
            <h3 class="ticket">Тариф: {{ticket.travelers[tickets_i['tr']].contact.purpose}}</h3>
                </div>                        <button v-if="tickets_i['tr']>0" class="btn btn-theme" @click.prevent="tickets_i['tr']--">Минулий пасажир</button>

                        <button v-if="tickets_i['tr']<ticket.flightOffers[0].travelerPricings.length-1" class="btn btn-theme" @click.prevent="tickets_i['tr']++">Наступний пасажир</button>

                <form class="form-detail">

				<div v-if="ticket.travelers[tickets_i['tr']].contact.emailAddress" class="form-row">
					<label for="email">Email</label>
					<input type="email" v-model="ticket.travelers[tickets_i['tr']].contact.emailAddress" name="email" id="email" readonly class="input-text" placeholder="Email" required>
				</div>
				<div v-if="ticket.travelers[tickets_i['tr']].contact.phones[0].number" class="form-row">
					<label for="phone">Телефон</label>
					<input v-model="ticket.travelers[tickets_i['tr']].contact.phones[0].number" type="text" name="phone" id="phone" class="input-text" readonly placeholder="Номер телефона" required>
				</div>
			</form></div>






  <div>
    <vue-final-modal v-model="show" :resize="true" :keep-changed-style="true" :esc-to-close="true"  :resize-directions="['t', 'tr', 'r', 'br', 'b', 'bl', 'l', 'tl']"
 classes="modal-container" content-class="modal-content">
<font-awesome-icon  class="modal__close" @click="show = false" :icon="['fas', 'times']"  />
        <span class="modal__title"><h3>Пошук білетів</h3></span>
      <div class="modal__content">
 <div class="hline"></div>
    <div class="">
          <h4>Звідки </h4>
            <div class="form-group">
              <div class="form-group">
                   <Multiselect :close-on-select="true" :searchable="true" v-model="orl" :options="op" >       <template v-slot:tag="{ option, handleTagRemove, disabled }">
                        <div class="multiselect-tag is-user">
                            {{ option.value }}
                            <i v-if="!disabled" @click.prevent @mousedown.prevent.stop="handleTagRemove(option, $event)"></i>
                        </div>
                    </template>
                    </Multiselect></div>
</div>
    <h4>Куди</h4>
            <div class="form-group">
                   <Multiselect :close-on-select="true" :searchable="true" v-model="del" :options="op" >       <template v-slot:tag="{ option, handleTagRemove, disabled }">
                        <div class="multiselect-tag is-user">
                            {{ option.value }}
                            <i v-if="!disabled" @click.prevent @mousedown.prevent.stop="handleTagRemove(option, $event)"></i>
                        </div>
                    </template>
                    </Multiselect></div>
        <h4>Коли</h4>


                <v-date-picker mode="dateTime" :is24hr="true" :timezone="timezone" :minute-increment="5"  :model-config="modelConfig"  class="btn" v-model="forward"  > <template v-slot="{ inputValue, togglePopover }">
      <div class="form-group">
        <button
          @click="togglePopover()"
                    class="form-control "

        >
          <font-awesome-icon :icon="['far', 'calendar-alt']" />

        </button>
        <input
          :value="inputValue"
                    class="form-control "

          readonly
        />
      </div>
    </template></v-date-picker>
    <h4>Назад(Не обовязково)</h4>


                <v-date-picker mode="dateTime" :is24hr="true" :timezone="timezone"  :minute-increment="5" :model-config="modelConfig"  class="btn " v-model="back" ><template v-slot="{ inputValue, togglePopover }">
      <div class="form-group">
        <button
          @click="togglePopover()"
          class="form-control "
        >
          <font-awesome-icon :icon="['far', 'calendar-alt']" />
        </button>
        <input
          :value="inputValue"
          readonly
          class="form-control"
        />
      </div>
    </template></v-date-picker>
          <h4>Пасажиры</h4>
          <div class="btn btn-theme">Дорослі
              <vue-number-input class="form-control" v-model="adult" :min="1" :max="9-teen"  controls></vue-number-input>
</div>
          <div class="btn btn-theme" >Діти
              <vue-number-input class="form-control" v-model="teen" :min="0" :max="9-adult"  controls></vue-number-input></div>
              <div class="btn btn-theme" >Немовлята
              <vue-number-input class="form-control" v-model="baby" :min="0" :max="adult"  controls></vue-number-input>
</div>
                   <h4>Клас</h4>
          <div class="btn btn-theme">Эконом <input type="radio" v-model="trip_class " name="popular" value="E"/></div>
          <div class="btn btn-theme">Преміум-эконом <input type="radio" v-model="trip_class " name="popular" value="PE"/></div>
          <div class="btn btn-theme" >Бізнес <input type="radio" name="popular"  v-model="trip_class " value="B"/></div>
          <div class="btn btn-theme" >Перший <input type="radio" name="popular"  v-model="trip_class " value="F"/></div>
          <div><button @click="searchTickets"  class="btn btn-block btn-success">Знайти білети</button></div>
      </div></div>
    </vue-final-modal>
  </div>
      </div>
  <div data-aos="slide-right" data-aos-delay="300" class="col-lg-4">
      <button @click.prevent="show=true" class="find-tickets-btn btn btn-primary">Знайти білети</button>

        <aside v-if="user.username" class="single_sidebar_widget author_widget">
                        <img :src="user.profile_img" alt="Admin" class=" img-rounded rounded-circle profile_img">
          <h4>{{user.username}}</h4>
          <p>{{user.email}}</p>

          <div class="br"></div>
        </aside>

        <div class="spacing"></div>


        <h3 v-if="popular[0]">Популярні пости</h3>
        <ul class="popular-posts">
          <li data-aos="zoom-in" class="popular-post" v-for="post_p in popular" :key="post_p.id" @click.prevent = 'goTo("post",{slug:post_p.slug})'><img style="height: 8vh" :src="post_p.img" />
            <p><a href="">{{post_p.title}}</a></p>
            <em> <font-awesome-icon :icon="['far', 'clock']" />  {{post_p.date}}
             <div><font-awesome-icon :icon="['far', 'user']" />{{post_p.author}}</div><font-awesome-icon :icon="['far', 'comment']" />{{post_p.comments.length}} <font-awesome-icon :icon="['far', 'heart']" />{{post_p.likes.length}} <font-awesome-icon :icon="['far', 'eye']" />{{post_p.views}}</em>
          </li>
        </ul>

      </div>
    </div>
  </div>
</template>

<script>
    import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome";
    import Multiselect from "@vueform/multiselect";
    import VueNumberInput from "@chenfengyuan/vue-number-input";
    import {useToast} from "vue-toastification";
    import f from "./airport.csv";
    import md from "./flightsearch.md";
    import axios from "axios";

    export default {
                props:['slug'],

        name: "ticket_single",
        inheritAttrs: false,
  emits: ['confirm', 'cancel'],
            components:{'font-awesome-icon': FontAwesomeIcon,Multiselect,VueNumberInput},
            data: () => ({
      listPosts: [],
        popular:[],
      user:{},
      total:0,
      date:"",
        adult:1,
        teen:0,
        baby:0,
        forward:'',
        back:'',
                reqs:[],
                tickets_i:{it:0,fl:0,tr:0},
                trip_class :'',

                timezone:"UTC",
                traveler_id:1,
              modelConfig: {
        type: 'string',
        mask: 'YYYY-MM-DD hh:mm:ss',
                        timeAdjust: '24:00:00',

      },
show:false,
      orl:"",
      del:"",
        op:{},
        arar:{},
        ticket:{},

                travalerType:{        "ADULT":"Дорослий",
        "CHILD":"Дитина",
        "SENIOR":"Поважного віку",
        "YOUNG":"Молодий",
        "HELD_INFANT":"Немовля",
        "SEATED_INFANT":"Сидяче немовля",
        "STUDENT":"Ученик"},
                fareType:{        "STANDARD":"Стандарт",
        "INCLUSIVE_TOUR":"Інклюзивний тур",
        "ADULT_WITH_COMPANION":"Дорослий із супутником",
        "COMPANION":"Супутник"},
                travelers_info:[],
                    toast : useToast(),
                  loading:false,
            }),
        created() {
                        this.loading = true

      this.loadTicket()

      this.loadPopularPosts()
      this.user = this.$store.state.cuser
                var data_airports = this.loadCsv(f);
      this.arar, this.op = data_airports

    },
      mounted() {
 console.log(this.$store.state)      },

      methods: {

          loadCsv(allText) {
              var ops = []
              var mdd = md.join("")

              for (var i = 1; i < allText.length; i++) {
                  if (mdd.includes(allText[i][0])) {


                      var st = "";
                      for (var j = 0; j < allText[i].length - 2; j++) {
                          st += allText[i][j] + " "
                      }
                      allText[i].push(st);
                      ops.push(st)
                  }
              }
              allText.shift()
              return allText, ops
          },

          async loadPopularPosts() {


              await axios({
                  url: this.$store.state.backendUrl+`api/v1/blog?limit=3&search=&date=month&ordering=descending`,
                  method: 'GET',
                  headers: {'Content-type': 'application/json',}
              }).then(response => {
                  this.popular = response.data.results
              })
          },
          async loadTicket() {

              var data = "grant_type=client_credentials&client_id=JxlnVpGLZUn7fV3UPNYfT2IJc0lL1GbE&client_secret=t965p0PRI1cCBS9h"
              var token;
              var access = localStorage.getItem('access')
              if (access) {
                  delete axios.defaults.headers.common["Authorization"];
              }
              await axios({
                  url: 'https://test.api.amadeus.com/v1/security/oauth2/token',
                  data: data,
                  method: 'POST',
                  headers: {"Content-type": "application/x-www-form-urlencoded"}
              }).then(response => {
                  token = response.data.token_type + " " + response.data.access_token
              })
                    var id ;
                            var url =  `${this.$store.getters.getServerUrl}api/v1/ticket/${this.slug}`;
        await axios({url: url,
            method: 'GET',
                headers: {"Authorization":"JWT " + localStorage.getItem('access')}

        }).then(response => {id = response.data.text_id, console.log(response)})
       await axios({
                  url: `https://test.api.amadeus.com/v1/booking/flight-orders/${id}`,
                  method: 'GET',
                  headers: {"Authorization": token}
              }).then(response => {this.ticket = response.data.data, this.loading=false
                  console.log(response)
              })



          },


      async searchTickets(){
        var data= "grant_type=client_credentials&client_id=JxlnVpGLZUn7fV3UPNYfT2IJc0lL1GbE&client_secret=t965p0PRI1cCBS9h"
        var token;
var access = localStorage.getItem('access')
if (access) {
delete axios.defaults.headers.common["Authorization"];
    }
    this.loading = true
        await axios({url: 'https://test.api.amadeus.com/v1/security/oauth2/token', data: data, method: 'POST', headers:{"Content-type":"application/x-www-form-urlencoded"} }).then(response => {console.log(response), token = response.data.token_type+" "+response.data.access_token
})
        this.orl = this.orl.slice(0,3)
       this.del= this.del.slice(0,3)
          var date = this.forward.toString().slice(10)
                    var data1 = {"id":1,
                            "originLocationCode":this.orl,
                            "destinationLocationCode":this.del,
                            "departureDateTimeRange": {
                                "date": this.forward.slice(0,10),
                                "time": this.forward.slice(11)
                              }}
if(this.back[0]){
                    var data2 = {"id":2,
                            "originLocationCode":this.del,
                            "destinationLocationCode":this.orl,
                            "departureDateTimeRange": {
                                "date": this.back.slice(0,10),
                                "time": this.back.slice(11)
                              }}
var dd = [data1,data2]
}
else{
    var dd = [data1]
}
var travelers =[]
                    var c =0;
                    for(var i =0;i<this.adult;i++){
                        c++;
                        travelers.push({"id":c,"travelerType": "ADULT"})
                    }
                    for(var i =0;i<this.teen;i++){
                        c++;
                        travelers.push({"id":c,"travelerType": "CHILD"})
                    }
                    for(var i =0;i<this.baby;i++){
                        c++;
                        if(i<this.adult) {
                            travelers.push({"id": c, "travelerType": "HELD_INFANT", "associatedAdultId": i+1})
                        }

                    }
var cabin= ""
if(this.trip_class === "E"){
    cabin= "ECONOMY"
}
else if(this.trip_class ==="PE"){
        cabin= "PREMIUM_ECONOMY"

}
else if(this.trip_class ==="B"){
        cabin= "BUSINESS"

}
else if(this.trip_class ==="F"){
        cabin= "FIRST"

}
          var d = {
  "currencyCode": "USD",
  "originDestinations": dd,
  "travelers": travelers,
  "sources": [
    "GDS"
  ],
  "searchCriteria": {
    "maxFlightOffers": 20,
    "flightFilters": {
      "cabinRestrictions": [
        {
          "cabin": cabin,
          "coverage": "MOST_SEGMENTS",
          "originDestinationIds": [
            "1"
          ]
        }
      ],
      "carrierRestrictions": {
        "excludedCarrierCodes": [
          "AA",
          "TP",
          "AZ"
        ]
      }
    }
  }
}
var that = this
await axios({url: 'https://test.api.amadeus.com/v2/shopping/flight-offers',data:d,  method: 'POST' ,headers:{"Authorization":token}}).then(res=>{
    if(res.data.data[0]){
        data = res.data.data
    this.loading=false,
        data = res.data.data,
         console.log(this.$store.state)
                  this.$store.dispatch('tickets_arr', { data })

         ,this.goTo("tickets",)
    }
    else {
        that.toast.error("Немає білетів"),this.loading = false
    }
})
    .catch(err => {console.log(err), that.toast.error("Введіть валідні данні або спробуйте пізніше"),this.loading = false})
        console.log(this.$store.state.tickets)

          access = localStorage.getItem('access')
if (access) {
  axios.defaults.headers.common['Authorization'] = "JWT " + access
    }
                    },

      goTo(name,params) {
        let r = this.$router.resolve({
          name: name,params: params// put your route information in
        })
        window.location.assign(r.href)
      },
        format_date(data){
            if (data.includes("-")){
        return data.slice(0,10).replaceAll("-",".")+" "+data.slice(11,16)}
            else if(data.includes("P")){
                return data.replaceAll("P","").replaceAll("T","").replaceAll("D"," день(днів) ").replaceAll("H"," год. ").replaceAll("M"," хвилин(а) ")
            }
        },
next_flight(ticket){if (this.tickets_i['fl']<ticket.flightOffers[0].itineraries[this.tickets_i['it']].segments.length-1){
    this.tickets_i['fl']++;
}
        else if(this.tickets_i['fl']>=ticket.itineraries[this.tickets_i['it']].segments.length-1 && this.tickets_i['it']<ticket.itineraries.length-1){
                this.tickets_i['it']++;
                this.tickets_i['fl']=0;

}
        },
prev_flight(ticket){if (this.tickets_i['fl']>0){
    this.tickets_i['fl']--;
}
        else if(this.tickets_i['fl']===0 && this.tickets_i['it']>0){
                this.tickets_i['it']--;
                this.tickets_i['fl']=ticket.itineraries[this.tickets_i['it']].segments.length-1;

}
        },

    }}
</script>
<style src="@vueform/multiselect/themes/default.css"></style>

<style scoped lang="scss">
 .multiselect-tag.is-user {
        padding: 5px 8px;
        border-radius: 22px;
        background: #35495e;
        margin: 3px 3px 8px;
    }
    .multiselect-tag.is-user i:before {
        color: #ffffff;
        border-radius: 50%;;
    }
.buy{
border-color: lightgrey;
    border-radius: 5px;
    border-style: solid;
    border-width: 1px;
    padding: 1%;
    margin:2%;
}
.ticket{
    display: inline-block;
    margin: 1%;
}
    #upload_pr {
   opacity: 0;
   position: absolute;
   z-index: -1;
}

.btn{
  margin: 1%;
}
  .preloader {
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    overflow: hidden;
    background: #fff;
    z-index: 1001;
  }

  .preloader__image {
    position: relative;
    top: 50%;
    left: 50%;
    width: 64px;
    height: 64px;
    margin-top: -32px;
    margin-left: -32px;
    background: url('../assets/Walk.gif') no-repeat 50% 50%;
    /*расположение (url) изображения gif и др. параметры*/
  }

  .loaded_hiding .preloader {
    transition: 0.3s opacity;
    opacity: 0;
  }

  .loaded .preloader {
    opacity: 50%;
  }
    *,*:before,*:after {
  box-sizing: border-box;
}
html {
  font-size: 16px;
}

.plane {
  width: 350px;
    margin-right:auto;
    margin-left: auto;
}
.close {
  position: absolute;
  right: 32px;
  top: 32px;
  width: 32px;
  height: 32px;
  opacity: 0.3;
}
.close:hover {
  opacity: 1;
}
.close:before, .close:after {
  position: absolute;
  left: 15px;
  content: ' ';
  height: 33px;
  width: 2px;
  background-color: #333;
}
.close:before {
  transform: rotate(45deg);
}
.close:after {
  transform: rotate(-45deg);
}
.cockpit {
  height: 250px;
  position: relative;
  overflow: hidden;
  text-align: center;
  border-bottom: 5px solid #d8d8d8;
  &:before {
    content: "";
    display: block;
    position: absolute;
    top: 0;
    left: 0;
    height: 500px;
    width: 100%;
    border-radius: 50%;
    border-right: 5px solid #d8d8d8;
    border-left: 5px solid #d8d8d8;
  }
  h1 {
    width: 60%;
    margin: 100px auto 35px auto;
  }
}
.exit {
  position: relative;
  height: 50px;
  &:before,
  &:after {
    content: "EXIT";
    font-size: 14px;
    line-height: 18px;
    padding: 0px 2px;
      margin: -20px;
    font-family: "Arial Narrow", Arial, sans-serif;
    display: block;
    position: absolute;
    background: green;
    color: white;
    top: 50%;
    transform: translate(0, -50%);
  }
  &:before {
    left: 0;
  }
  &:after {
    right: 0;
  }
}

.fuselage {
  border-right: 5px solid #d8d8d8;
  border-left: 5px solid #d8d8d8;
}

ol {
  list-style :none;
  padding: 0;
  margin: 0;
}

.row {

}

.find-tickets-btn{
    padding: 10%;
    border-radius: 10px;
    font-weight: bold;
    width: 100%;
}

.plane-desc{    margin-left: 40px;
    margin-top:10px;
    display: inline-block;
    max-width: 400px;
    position: absolute;
}
.c{
    margin-bottom: 10%;
}
.seats {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: center;

}

.seat {
  display: flex;
  flex: 0 0 14.28571428571429%;
  padding: 5px;
  position: relative;

  input[type=radio] {
    position: absolute;
    opacity: 0;
  }
  input[type=radio]:checked {
    + label {
      background: #F42536;
color: #EBEBEB;
      -webkit-animation-name: rubberBand;
          animation-name: rubberBand;
      animation-duration: 300ms;
      animation-fill-mode: both;
    }
  }
  input[type=radio]:disabled {
    + label {
      background: #dddddd;
        color:white;
      text-indent: -9999px;
      overflow: hidden;
      &:after {
        content: "X";
        text-indent: 0;
        position: absolute;
        top: 4px;
        left: 50%;
        transform: translate(-50%, 0%);
      }
      &:hover {
        box-shadow: none;
        cursor: not-allowed;
      }
    }
  }
  label {
              color:white;

    display: block;
    position: relative;
    width: 100%;
    text-align: center;
    font-size: 14px;
    font-weight: bold;
    line-height: 1.5rem;
    padding: 4px 0;
    background: #2e6da4;
    border-radius: 5px;
    animation-duration: 300ms;
    animation-fill-mode: both;

    &:before {
      content: "";
      position: absolute;
      width: 75%;
      height: 75%;
      top: 1px;
      left: 50%;
        color: gray;
      transform: translate(-50%, 0%);
          border-radius: 3px;
    }
    &:hover {
      cursor: pointer;
      box-shadow: 0 0 0px 2px #5C6AFF;
    }

  }
}

@-webkit-keyframes rubberBand {
  0% {
    -webkit-transform: scale3d(1, 1, 1);
            transform: scale3d(1, 1, 1);
  }

  30% {
    -webkit-transform: scale3d(1.25, 0.75, 1);
            transform: scale3d(1.25, 0.75, 1);
  }

  40% {
    -webkit-transform: scale3d(0.75, 1.25, 1);
            transform: scale3d(0.75, 1.25, 1);
  }

  50% {
    -webkit-transform: scale3d(1.15, 0.85, 1);
            transform: scale3d(1.15, 0.85, 1);
  }

  65% {
    -webkit-transform: scale3d(.95, 1.05, 1);
            transform: scale3d(.95, 1.05, 1);
  }

  75% {
    -webkit-transform: scale3d(1.05, .95, 1);
            transform: scale3d(1.05, .95, 1);
  }

  100% {
    -webkit-transform: scale3d(1, 1, 1);
            transform: scale3d(1, 1, 1);
  }
}

@keyframes rubberBand {
  0% {
    -webkit-transform: scale3d(1, 1, 1);
            transform: scale3d(1, 1, 1);
  }

  30% {
    -webkit-transform: scale3d(1.25, 0.75, 1);
            transform: scale3d(1.25, 0.75, 1);
  }

  40% {
    -webkit-transform: scale3d(0.75, 1.25, 1);
            transform: scale3d(0.75, 1.25, 1);
  }

  50% {
    -webkit-transform: scale3d(1.15, 0.85, 1);
            transform: scale3d(1.15, 0.85, 1);
  }

  65% {
    -webkit-transform: scale3d(.95, 1.05, 1);
            transform: scale3d(.95, 1.05, 1);
  }

  75% {
    -webkit-transform: scale3d(1.05, .95, 1);
            transform: scale3d(1.05, .95, 1);
  }

  100% {
    -webkit-transform: scale3d(1, 1, 1);
            transform: scale3d(1, 1, 1);
  }
}

.rubberBand {
  -webkit-animation-name: rubberBand;
          animation-name: rubberBand;
}
.img-rounded{
  max-height: 9vh;
  float: left;
  margin-right: 1%;
  border-radius: 50%;
}
</style><style scoped>
::v-deep .modal-container {
  display: flex;
  justify-content: center;
  align-items: center;
}
::v-deep .modal-content {
  position: relative;
  display: flex;
  flex-direction: column;
  max-height: 60%;
  margin: 0 1rem;
  padding: 1rem;
  border: 1px solid #e2e8f0;
  border-radius: 20px;
    margin-top:auto;
    margin-bottom: auto;
}
.modal__title {
  margin: 0 2rem 0 0;
  font-size: 1.5rem;
  font-weight: 700;
}
.modal__content {
  flex-grow: 1;
  overflow-y: auto;
    padding: 3%;
}
.modal__action {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-shrink: 0;
  padding: 1rem 0 0;
}
.modal__close {
  position: absolute;
  top: 2rem;
  right: 2rem;
    cursor: pointer;
}
</style>

<style scoped>
.dark-mode div::v-deep .modal-content {
  border-color: #2d3748;
  background-color: #1a202c;
}
</style>