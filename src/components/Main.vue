<template>
  <v-main class="main ame-color">
    <v-container class="d-flex ma-2 justify-center mx-auto">
      
    <div class="debug" width="100">
      <p>rank: {{ rank }}</p>
      <p>ranked_cases: {{ ranked_cases }}</p>
      <p>needed: {{ cases_needed }}</p>
      <p>wins: {{ player_wins }}</p>
      <p>target: {{ target }}</p>
      <p>suspect: {{ suspect }}</p>
      <p>game_loss: {{ game_loss }}</p>
      <p>failed_id: {{ failed_id }}</p>
      <p>failed_id: {{ failed_time }}</p>
      <p>elapsed_time: {{ elapsed_time }}</p>
      <p>correct_image: {{ correct_image }}</p>
      <p>correct_blurb: {{ correct_blurb }}</p>
      <p>cases_completed : {{ cases_completed }}</p>
      <p>compendium : {{ compendium }}</p>
      <p>country : {{ country }}</p>
    </div>
<!-- MAIN card -->

      <v-card
        class="ma-2"
        width="408"
        height="725"
      >
        <v-img
          max-height="240"
          :src="getCountryFlag(country)"
        />

        <v-divider></v-divider>

        <v-card-title class="text-h6 font-weight-medium">
          {{ getCountryName(country) }}
        </v-card-title>


        <v-divider></v-divider>

        <v-card-actions class="justify-center float-bottom">
          <v-btn
            class="ma-2"
            outlined
            fab
            color="blue"
            @click="enterInvestigate()"
          >
            <v-icon>
              mdi-map-search
            </v-icon>
          </v-btn>
          <v-btn
            class="ma-2"
            outlined
            fab
            color="blue"
            @click="enterDepartures()"
          >
            <v-icon>
              mdi-airplane-takeoff
            </v-icon>
          </v-btn>
          <v-btn
            class="ma-2"
            outlined
            fab
            color="blue"
            @click="enterWarrant()"
          >
            <v-icon>
              mdi-target-account
            </v-icon>
          </v-btn>
          <v-btn
            class="ma-2"
            outlined
            fab
            color="blue"
            @click="summary = true"
          >
            <v-icon>
              mdi-briefcase
            </v-icon>
          </v-btn>
        </v-card-actions>
        <v-divider></v-divider>
        <v-card-subtitle class="text-subtitle-1 text--primary font-weight-medium">
          {{ getCountryType(country) + ' of ' + getCountryIdol(country) }}
        </v-card-subtitle>
        <v-card-text class="text--primary">
          <p>{{ getCountryDescription(country) }}</p>

          <p v-for="(source, index) in getCountrySources(country)" :key="source">{{['Source', 'Vexillologist', 'Cartographer'][index] }}: {{source}}</p>
        </v-card-text>




        <v-expand-transition>

<!-- INVESTIGATE card (sub of main v-expand-transition) -->

<v-card
  v-if="investigate"
  class="transition-in-out v-card--reveal"
>
  <v-toolbar
    color="blue"
    dark
  >
    <v-btn
      icon
      @click="exitInvestigate()"
    >
      <v-icon>mdi-arrow-left</v-icon>
    </v-btn>
    <v-toolbar-title>Investigate</v-toolbar-title>
  </v-toolbar>

  <v-list two-line>
    <v-list-item-group
      active-class="blue--text"
    >
      <div v-for="(fanname, index) in fannames.slice(0,2)" :key="fanname">
        <v-list-item @click="selectInvestigation(index+1)">
          <v-list-item-content>
            <v-list-item-title v-text="fanname"></v-list-item-title>
            <v-list-item-subtitle class="text--primary">
              Question a witness
            </v-list-item-subtitle>
            <v-list-item-subtitle>
              What can you tell me about this detective?
            </v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-avatar right size="64" >
            <v-img right height="72" :src="getLandmarkImage(index+1)"></v-img>
          </v-list-item-avatar>
        </v-list-item>
        <v-divider
          v-if="index < 2"
          :key="index"
        ></v-divider>
      </div>
        <v-list-item @click="selectInvestigation(0)"> 
          <v-list-item-content>
            <v-list-item-title v-text="landmarks[0].name"></v-list-item-title>
            <v-list-item-subtitle class="text--primary">
              Visit a landmark
            </v-list-item-subtitle>
            <v-list-item-subtitle>
              Do you know where this detective went?
            </v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-avatar right size="64" >
            <v-img right height="72" :src="getLandmarkImage(0)"></v-img>
          </v-list-item-avatar>
        </v-list-item>
        <v-divider></v-divider>
    </v-list-item-group>
  </v-list>


  <v-expand-transition>
    <v-card
      v-show="investigating >= 0"
      class="transition-in-out v-card--reveal"
      @click="investigating = -1"
      :ripple="false"
    >
      <v-img  max-height="200" :src="getLandmarkImage(investigating)"></v-img>
      <v-divider></v-divider>
      <div class="text--primary text-center pa-1" style="background-color: #f0f0f0">
        <v-progress-circular
          :rotate="12"
          :size="48"
          :width="6"
          :value="elapsed_time / timelimit * 100"
          :color="hoursColor"
          class="ma-2"
        >
        </v-progress-circular>
        <div class="text-subtitle-2 text-center text--primary">
          Spent {{ cost - 1 }} hour{{ (cost - 1 > 1) ? 's' : ''}}
        </div>
      </div>
      <v-divider></v-divider>

      <v-card-text>
        <div  class="text-center text--secondary font-weight-bold text-caption">
          {{ country.fannames[(investigating) % fannames.length] }}
        </div>
        <div class="text-center text--secondary text-caption">
          <b>From</b> {{ country.name }}
        </div>
      </v-card-text>
      <div
        v-for="clue in getClue()"
        :key=clue
      >
        <div style="padding-left: 1em; max-width: 100%">
          <v-chip
            class="ma-1 text-subtitle-1 font-weight-medium"
            style="max-width: 75%; height: auto; white-space: normal"
          >
            <span class="ma-2 text-wrap font-weight-medium" style="line-height: 1.2em">{{ clue }}{{ country.gobi ? ' ' + country.gobi : '' }}</span>
          </v-chip>
        </div>
      </div>

    </v-card>
  </v-expand-transition>
  <v-expand-transition>
    <v-card
      v-if="correct"
      class="transition-in-out v-card--reveal"
      @click="correct = false"
    >
      <v-card-title class="text-h6 font-weight-medium">
        She was here!
      </v-card-title>
      <v-card-subtitle>
        {{ correct_blurb }}
      </v-card-subtitle>
      <v-card-text>
        <v-img :src="correct_image" max-height="376"></v-img>
        <v-card-text class="text-h5 text--primary font-weight-medium">
          Progress:
        </v-card-text>
        <v-progress-linear
          height="25"
          :value="phase / places * 100"
          color="blue"
        >
          {{ phase }} / {{ places }}
        </v-progress-linear>
        <v-card-text v-show="phase + 1 == places && suspect == null" class="text-h6 text--primary font-weight-medium">
          You must have a valid ID before you investigate the next country!!!
        </v-card-text>
      </v-card-text>
    </v-card>
  </v-expand-transition>

  <v-fab-transition>
    <v-card
      v-show="game_win"
      class="transition-in-out v-card--reveal"
      @click="endGame()"
    >
      <v-card-title>
        You found Amelia Watson!
      </v-card-title>
      <v-card-subtitle>
        Target ID: {{ target.name }} has been located. 
      </v-card-subtitle>
      <v-divider></v-divider>
      <v-img :src="'/img/targets/' + this.target.img" max-height="465" />
      <v-divider></v-divider>
      <v-card-text>
        <div v-show="this.ca.rank >= this.rank">
          <v-card-title>
            Promotion Progress:
          </v-card-title>
          <div>
            <p>{{ this.rank_name }}<span class="float-right">{{ this.next_rank_name }}</span></p>
          </div>
          <v-progress-linear 
            height="25"
            :value="ranked_cases / cases_needed * 100"
            color="amber"
          >
            {{ ranked_cases }} / {{ cases_needed }}
          </v-progress-linear>
        </div>
      </v-card-text>
    </v-card>
  </v-fab-transition>


  <v-fab-transition>
    <v-card
      v-show="game_loss"
      dark
      color="red"
      class="transition-in-out v-card--reveal"
    >
      <v-card-title  class="text-h6 font-weight-medium" >
        Mission Failed
      </v-card-title>
      <v-card-subtitle v-if="failed_time"  class="text-subtitle-1 font-weight-medium">
          You ran out of time. The timetraveler eludes you.
      </v-card-subtitle>
      <v-card-subtitle v-if="failed_id"  class="text-subtitle-1 font-weight-medium" >
          Ya got an ID for that detective, mate? You'll never find her without a proper one.
      </v-card-subtitle>
      <v-card-text>
        <p v-if="failed_time">
        Avoid spending too much time unnecessarily in one place.
        </p>
        <p v-if="failed_time">
        Traveling to the wrong country will waste a lot of time.
        </p>
        <p v-if="failed_id">
        Make sure you generate an ID for your target before you reach the end.
        </p>
        <p v-if="failed_id">
        Spend more time in each place to gather more clues.
        </p>
        <p v-if="failed_id">
        You might need to guess if you are unlucky.
        </p>
        <v-btn outlined @click="endGame()">
        Exit
        </v-btn>
      </v-card-text>
    </v-card>
  </v-fab-transition>

</v-card>


<!-- DEPARTURES card (sub of main v-expand-transition) -->


<v-card
  v-if="departures"
  class="transition-in-out v-card--reveal"
>
  <v-toolbar
    color="light-blue"
    dark
  >
    <v-btn
      icon
      @click="exitDepartures()"
    >
      <v-icon>mdi-arrow-left</v-icon>
    </v-btn>
    <v-toolbar-title>Departures</v-toolbar-title>
  </v-toolbar>

  <v-list>
    <v-list-item-group>

      <div v-for="(destination, index) in connections"
        :key="'destination_' + index"
      >
        <v-list-item @mouseover="hoverDestination(index)" @click="selectDestination(index)">
          <v-list-item-content>

            <v-list-item-title wrap> {{ getCountryName(destination) }} </v-list-item-title>
            <v-list-item-subtitle wrap>{{ getCountryType(destination) + ' of ' + getCountryIdol(destination) }}</v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-avatar right rounded size="85" >
            <v-img right max-width="100" :src="getCountryFlag(destination)"></v-img>
          </v-list-item-avatar>
        </v-list-item>

        <v-divider
          :key="index"
        ></v-divider>

      </div>

    </v-list-item-group>
  </v-list>
  <v-expand-transition>
    <v-card
      v-if="departing > 0"
      class="transition-in-out v-card--reveal"
    >
      <v-card-title>
        Travel in progress...
      </v-card-title>
      <v-card-subtitle>
        Please wait patiently.
      </v-card-subtitle>
      <v-card-text class="text-center">
        <v-img src="/img/investigate/f.gif"></v-img>
        <v-progress-circular
          :rotate="12"
          :size="96"
          :width="12"
          :value="elapsed_time / timelimit * 100"
          :color="hoursColor"
          class="ma-2"
        >
        </v-progress-circular>
        </v-card-text>
          <div class="text-subtitle-2 text-center text--primary">
            Spent {{ cost - 1 }} hour{{ (cost - 1 > 1) ? 's' : ''}}
          </div>
      </v-card>
  </v-expand-transition>

</v-card>


<!-- WARRANT card (sub of main v-expand-transition) -->

<v-card
v-if="warrant"
class="transition-in-out v-card--reveal"
>
    <v-toolbar
        color="cyan"
        dark
    >
            <v-btn
              icon
              @click="exitWarrant()"
            >
              <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
        <v-toolbar-title>Database Search</v-toolbar-title>
        </v-toolbar>

    <v-card-text>
    <v-select
      :items="style"
      v-model="selectStyle"
     label="Outfit"
    ></v-select>
    <v-select
      :items="clothing"
     label="Accessories"
      v-model="selectClothing"
    ></v-select>
    <v-select
      :items="hair"
     label="Facial Hair"
      v-model="selectHair"
    ></v-select>
    <v-select
      :items="equipment"
      v-model="selectEquipment"
     label="Equipment"
    ></v-select>
    <v-btn dark large color="cyan" @click="searchDatabase()" class="ma-2">
      <v-icon dark left>
         mdi-database-search
      </v-icon>
      <span right>
    Submit Query
      </span>
    </v-btn> 
  </v-card-text>
  <v-divider>
  </v-divider>


  <v-alert dense v-if="suspect != null" :value="suspect != null" type="info" class="ma-4" transition="scale-transition">
  Target ID: "{{ suspect.name }}"
  </v-alert>
  <v-alert dense :value="searched" :type="getAlertType()" class="ma-4" transition="scroll-x-transition">
  Your query returned {{ results.length }} results.  
  </v-alert>


</v-card>

<!-- SUMMARY card (sub of main v-expand-transition) -->

<v-card
v-if="summary"
class="transition-in-out v-card--reveal"
style="height: 100%;"
>
    <v-toolbar
        color="green"
        dark
    >
            <v-btn
              icon
              @click="summary = false"
            >
              <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
        <v-toolbar-title>Case Summary</v-toolbar-title>
        </v-toolbar>
      <v-card-text>
        <v-card-text v-if="summary" class="text--primary">
          <p>---- Case name: {{ ca.name }} -----</p>
          <p>
          <p>Good morning <b>{{ rank_name }}</b>,</p>
          <p>
          You have been tasked with finding a missing Amelia Watson.
          </p>
          <p>She promised that she would do a <b>{{ topic }}</b> stream next week. It's up to you to make sure that it happens.
          </p>
          <p>Amelia was last seen vacationing in <b>{{first_country.name}}</b>.
          </p>
        </v-card-text>
        <v-card-subtitle class="text-subtitle-1 text--primary font-weight-medium">
          Progress:
        </v-card-subtitle>
        <v-progress-linear
          :height="25"
          :value="phase / places * 100"
          color="blue"
        >
          {{ phase }} / {{ places }}
        </v-progress-linear>
        <v-card-subtitle class="text-subtitle-1 text--primary font-weight-medium">
          Time Elapsed:
        </v-card-subtitle>
          <v-progress-linear
            height="25"
            :value="elapsed_time / timelimit * 100"
            :color="hoursColor"
          >
            {{ elapsed_time }} / {{ timelimit}} hrs
          </v-progress-linear>
      </v-card-text>
      <v-card-text>
        <v-btn large block primary dark color="blue-grey" @click="summary = false">
        Close Summary
        </v-btn> 
        <p></p>
        <div class="text-right">
          <v-btn dark outlined color="red" @click="summary = false; endGame()">
          Give up
          </v-btn> 
        </div>
      </v-card-text>


</v-card>

<!-- HOME card (sub of main v-expand-transition) -->

<v-card
v-if="home"
class="transition-in-out v-card--reveal"
style="height: 100%;"
>
    <v-toolbar
        color="brown accent-4"
        dark
    >
        <v-toolbar-title>Amelia's Detective Office</v-toolbar-title>
        </v-toolbar>

      <div>
        <v-card-title class="text-h6 font-weight-medium">
            Current Rank: {{ rank_name }}
        </v-card-title>
        <v-card-subtitle class="text--primary">
        {{ ranked_cases }}/{{ cases_needed }} ranked cases until next promotion
        </v-card-subtitle>
        <v-divider></v-divider>
        <v-list two-line  class="overflow-y-auto" max-height="570" subheader>
          <v-list-item-group>
          <div  v-for="(ca, index) in cases" :key="ca.name">
            <div v-if=" index == 0 || cases[index-1].rank < ca.rank" >
              <v-divider v-if="index > 0"></v-divider>
              <v-subheader>
                Rank required: {{ txt.ranks[cases[index].rank] }}
              </v-subheader> 
            </div>
            <v-list-item two-line @click="startGame(ca)" :disabled="!debug && ca.rank > rank">
              <v-list-item-content>
                <v-list-item-title>{{ ca.name }}</v-list-item-title>
                <v-list-item-subtitle>{{ ca.subtitle }}</v-list-item-subtitle>
              </v-list-item-content>

              <v-list-item-action>
                <v-icon
                  v-if="cases_completed.filter(x=>x==ca.name).length > 0"
                  color="green darken-2"
                >
                  mdi-check-bold
                </v-icon>

                <v-icon
                  v-else-if="!debug && ca.rank == rank"
                  color="grey lighten-1"
                >
                  mdi-check-bold
                </v-icon>
              </v-list-item-action>

            </v-list-item>
          </div>
          <v-divider></v-divider>
              <v-subheader class="font-weight-bold">
                To be continued...
              </v-subheader>
            <v-list-item two-line :disabled="1==1">
              <v-list-item-content>
                <v-list-item-title>Inspector</v-list-item-title>
                <v-list-item-subtitle>More ranks and difficulties in the future</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-group>

        </v-list>
      </div>
</v-card>

    </v-expand-transition>
</v-card>


<!-- RIGHT card -->

<v-card
  class="ma-2"
  width="544"
  height="725"
>
  <v-img v-show="game_win" height="725" style="border-radius: 4px;" src="/img/bubba.gif"/>
  <div v-show="game_loss && failed_time" height="725" style="border-radius: 4px; overflow: hidden; display:block; background-color:#34311a">
  <video playsinline autoplay muted loop style="margin-left: -400px"> <source src="/img/1e_ver2_E3IWsUpVkAAZnFe.mp4" type="video/mp4"></video>
  </div>
  <v-img v-show="game_loss && failed_id" height="725" style="border-radius: 4px;" src="/img/who.jpg"/>
  <v-img v-show="!game_win && !game_loss && home" height="725" style="border-radius: 4px;" src="/img/enmaa.jpg"/>
  <Globe
      v-show="!game_win && !game_loss && !home"
      :investigate="investigate"
      :departures="departures"
      :departing="departing"

      :destination="destination"
      :country="country"
      ref="globe"/>

<v-expand-x-transition>
    <v-img
      v-show="!game_win && !failed_id && investigate"
      max-height="725"
      color="grey darken-4"
      class="v-card-x-reveal"
      onerror="this.style.display='none'"
      contain height="725" :src="getCountryMap(country)"
    />
</v-expand-x-transition>
</v-card>

      </v-container>
    </v-main>
</template>

<script>
import Globe from '../components/Globe';
import { english } from '../data/text.js'
import { holodata } from '../data/hololive.js'
import { game } from '../data/game.js'

let all_clues = Object.keys(holodata).map(key=>holodata[key]).flat().map(c=>c.clues).flat(Infinity)
console.log(all_clues.length + ' clues loaded')


function randomPick(list) {
    return list[Math.floor(Math.random() * list.length)]
}

function distance(pointA, pointB) {
  var lat1 = pointA.pointsData.lat
  var lon1 = pointA.pointsData.lng
  var lat2 = pointB.pointsData.lat
  var lon2 = pointB.pointsData.lng
  var p = 0.017453292519943295;    // Math.PI / 180
  var c = Math.cos;
  var a = 0.5 - c((lat2 - lat1) * p)/2 + 
          c(lat1 * p) * c(lat2 * p) * 
          (1 - c((lon2 - lon1) * p))/2;

  return 12742 * Math.asin(Math.sqrt(a)); // 2 * R; R = 6371 km
}


export default {
    name: 'MyMain',
      components: {
        Globe,
      },
    mounted: function () {
      this.loadData()
    },
    methods: {
        loadData() {
          let rank = localStorage.getItem('rank')
          let player_wins = localStorage.getItem('player_wins')
          let cases_completed = localStorage.getItem('cases_completed')
          let compendium = localStorage.getItem('compendium')
          this.rank = (rank) ? JSON.parse(rank) : 0
          this.player_wins = (player_wins) ? JSON.parse(player_wins) : []
          this.cases_completed = (cases_completed) ? JSON.parse(cases_completed) : []
          this.compendium = (compendium) ? JSON.parse(compendium) : []
          console.log("Main: Loaded from localstorage.")
          console.log("rank: ", +this.rank)
          console.log("player_wins: ",+ this.player_wins)
          console.log("cases_completed: "+ this.cases_completed)
          console.log("compendium: "+ this.compendium)
        },
        startGame(ca) {
          if(!this.debug && ca.rank > this.rank) return;
          this.summary = true
          this.ca = ca
          this.topic = this.txt.topics[Math.floor(Math.random() * this.txt.topics.length)]

          this.places = ca.places 
          this.timelimit = ca.timelimit 
          this.difficulty = ca.difficulty
          this.cost = 1
          this.interval = 'start'

          
          this.countries = []
          ca.set.forEach( (id) => {
            this.countries = this.countries.concat(holodata[id])
          })

          this.phase = 0
          this.elapsed_time = 0

          this.connections_db = {}
          this.countries.forEach( (country) => {
                this.connections_db[country.idol] = []
          })
          this.countries.forEach( (country) => {
            let others = this.countries
                .filter(x=>x!=country)
                .filter(x=>this.connections_db[x.idol].length < 6)
                .filter(x=> this.connections_db[x.idol].length > 0 ? this.connections_db[x.idol].indexOf(country) == -1 : true)

            while(this.connections_db[country.idol].length < 6 && others.length > 0) {
                let pick = randomPick(others)
                others = others.filter(x=>x!=pick)
                this.connections_db[country.idol].push(pick)
                this.connections_db[pick.idol].push(country)
            }
          })

          this.target = this.randomTarget()
          this.first_country = this.valid_country = this.country = this.randomCountry()
          this.next_country = this.nextCountry()
          this.newClues()


          this.home = false
        },
        endGame() {
          this.summary = false
          this.investigate = false
          this.investigating = -1
          this.correct = false
          this.departures = false
          this.departing = -1
          this.arrived = false
          this.warrant = false
          this.selectStyle = false
          this.selectClothing = false
          this.selectHair = false
          this.selectEquipment = false

          this.home = true
          //this.country = null

          if(this.ranked_cases >= this.cases_needed) {
            this.setRank(this.rank + 1)
          }
          this.phase = 0
          this.elapsed_time = 0
          this.timelimit = 99
          this.places = -1
          this.suspect = null
          if(this.interval)
            clearInterval(this.interval)
        },
        randomTarget() {
          return randomPick(game.ames)
        },
        randomCountry() {
          return this.countries[Math.floor(Math.random() * this.countries.length)]

        },
        nextCountry() {
          return this.connections ? this.connections
            .sort(() => 0.5 - Math.random())[0] : null

        },
        incrementHours(hrs) {
            let next = this.elapsed_time + hrs
            this.interval = setInterval(() => {
                    // don't increment yet if 'Correct' screen is visible
                    if(!this.correct  && this.elapsed_time < next) {
                        this.elapsed_time += hrs
                        clearInterval(this.interval)
                        this.interval = null
                    }
            }, 500 )
        },
        getCountryName(country) {
            if(country)
              return country.name.length > 0 ? country.name : country.idol.split(' ')[0] + 'land'
            return 'TemplateLand'
        },
        getCountryType(country) {
            if(country)
              return country.type.length > 0 ? country.type : this.txt.types[this.countries.indexOf(country) % this.txt.types.length];
            return 'Domain'
        },
        getCountryMap(country) {
            if(country)
              return '/img/' + country.key + '/map.png';
            else
              return '';
        },
        getCountryFlag(country) {
            if(country)
              return '/img/' + country.key + '/flag.png';
            else
              return '/img/template/flag.png';
        },
        getCountryDescription(country) {
            if(country && country.description.length > 0)
              return country.description
            return "An unexplored land. An unmapped territory. All that can be said about " + this.getCountryName(country) +  " is that it is inhabited by " + this.getCountryIdol(country) + "."
        },
        getCountrySources(country) {
          return country ? country.source : []
        },
        getCountryIdol(country) {
          return country ? country.idol : 'Nanashi'
        },
        setDestination(location) {
            this.country = location
            this.departures = false
        },
        getLandmarkImage(landmark_idx) {
            return '/img/' + this.country.key + '/' + landmark_idx + '.png'
        },
        setRank(rank) {
          this.rank = rank
          localStorage.setItem('rank', rank)
          console.log('setRank: ' + rank)
        },


// MOUSE handlers
        enterInvestigate() {
           this.investigate = true
        },
        exitInvestigate() {
           this.investigate = false
        },
        enterDepartures() {
           this.departures = true
        },
        exitDepartures() {
           this.departures = false
        },
        enterWarrant() {
           this.warrant = true
        },
        exitWarrant() {
           this.warrant = false
           this.searched = false
        },
        selectInvestigation(index) {
            if(this.interval && this.interval != 'start') return
            this.incrementHours(this.cost)
            this.cost += 1
            this.investigating = index
            if( this.country == this.next_country ) {
                this.correct = true
                this.incrementPhase()
            }
        },
        hoverDestination(index) {
          if(this.departures && this.departing < 0)
            this.destination = this.connections[index]
        },
        selectDestination(index) {
          if(this.interval && this.interval != 'start') return
          if(this.departures && this.departing < 0) {
            this.destination = this.connections[index]

            this.departing = index+1
            this.arrived = false
            this.cost = 2 + Math.ceil(distance(this.destination, this.country) / 1000)
            this.incrementHours(this.cost)

            this.arrival_interval = setInterval(() => {
                    if(!this.arrived) {
                        this.cost = 1
                        this.arrived = true
                        clearInterval(this.arrival_interval)
                        this.exitDeparting()
                    }
            }, 2500)
          }
        },
        exitDeparting() {
          if(this.arrived) {
            this.departures = false
            this.departing = -1
            this.country = this.destination
            this.destination = null
          }
        },
        incrementPhase() {
          this.valid_country = this.country
          this.next_country = this.nextCountry()
          this.newClues()

          this.phase = this.phase + 1

          if(this.game_win) {
              this.player_wins.push(this.ca.rank)
              this.cases_completed.push(this.ca.name)
              this.compendium.push(this.target.name)
              localStorage.setItem('player_wins', JSON.stringify(this.player_wins))
              localStorage.setItem('cases_completed', JSON.stringify(this.cases_completed))
              localStorage.setItem('compendium', JSON.stringify(this.compendium))
              console.log('playerWins: ' + this.player_wins)
              console.log('cases: ' + this.cases_completed)
              console.log('compendium: ' + this.compendium)
          }
          
        },
        newClues() {
          let clueA = []
          let clueB = []
          let clueC = []
          let a = Math.floor(Math.random() * 5)
          let b = Math.floor(Math.random() * 5)
          let target = this.target
          let target_traits = [target.style, target.clothing, target.hair,  target.equip]

          clueA = randomPick(this.next_country.clues.slice(0, this.difficulty+1).flat(Infinity))
          clueB = randomPick(this.next_country.clues.slice(0, this.difficulty+1).flat(Infinity))
          clueC = randomPick(this.next_country.clues.slice(0, this.difficulty+1).flat(Infinity))

          if(a == 0) {
            clueA = [clueA]
          }
          else if (a < 3) {
            clueA = [randomPick(game.traits[randomPick(target_traits)])]
          }
          else {
            clueA = [clueA,
                    randomPick(game.traits[randomPick(target_traits)])]
          }
          if(b == 0) {
            clueB = [clueB]
          }
          else if (b < 3) {
            clueB = [randomPick(game.traits[randomPick(target_traits)])]
          }
          else {
            clueB = [clueB,
                     randomPick(game.traits[randomPick(target_traits)])]
          }
          clueC = [clueC]

          // investigating 0 is a landmark investigation. it only returns directions
          this.clues = [
            clueC,
            clueA,
            clueB,
          ]
        },
        getClue() {
            if (this.country == this.valid_country) {
                return this.clues[this.investigating]

            }
            else {
                return [this.txt.incorrect[this.investigating]]
            }
        },
        searchDatabase() {
          this.searched = true

          this.results = game.ames
            .filter( a => !this.selectStyle || a.style == this.selectStyle)
            .filter( a => !this.selectClothing || a.clothing == this.selectClothing)
            .filter( a => !this.selectHair || a.hair == this.selectHair)
            .filter( a => !this.selectEquipment || a.equip == this.selectEquipment)
          if(this.results.length == 1) {
            this.suspect = this.results[0]
          }
        },
        getAlertType() {
          if(this.results.length == 0) {
            return 'error'
          }
          if(this.results.length == 1) {
            return 'success'
          }
          else {
            return 'warning'
          }
        },
        getAlertText() {
          if(this.results.length == 0) {
            return 'Returne'
          }
          if(this.results.length == 1) {
            return 'success'
          }
          else {
            return 'warning'
          }
        },
    },
    computed: {
        cases: function () {
          return game.cases
        },
        ranked_cases: function () {
          return this.player_wins.filter( x => x >= this.rank ).length
        },
        cases_needed: function () {
          return (this.rank+1 < game.promotions.length) ? game.promotions[this.rank+1] : game.promotions[this.rank]
        },
        rank_name: function () {
            return this.txt.ranks[this.rank]
        },
        next_rank_name: function () {
            let idx = 1 + this.rank
            idx = idx < this.txt.ranks.length ? idx : this.txt.ranks.length
            return this.txt.ranks[idx]
        },
        game_win: function () {
            return this.phase == this.places && this.target == this.suspect
        },
        failed_time: function () {
            return this.elapsed_time >= this.timelimit
        },
        failed_id: function () {
            return this.phase == this.places && this.target != this.suspect
        },
        game_loss: function () {
            return !this.game_win && (this.failed_time || this.failed_id)
        },
        connections: function () {
          return this.connections_db[this.country.idol]
        },
        correct_blurb : function () {
          let idx = (this.investigating + this.phase) % this.txt.correct.length
          return this.txt.correct[idx]
        },
        correct_image : function () {
          let idx = (this.investigating + this.phase) % this.txt.correct.length
          return '/img/investigate/' + idx + '.gif'
        },
        hoursColor: function () {
            if( this.elapsed_time / this.timelimit  < 0.5 ) {
                return 'green'
            }
            else if( this.elapsed_time / this.timelimit < 0.75 ) {
                return 'orange'
            }
            return 'red lighten-2'

        },
        landmarks: function () {
            return this.country.landmarks;
        },
        fannames: function () {
            return this.country.fannames;
        },
    },
    props: [],
    data: () => ({
// player state
      rank: 0,
      player_wins: [],
      cases_completed: [],
      compendium: [],

// game settings ( win_phase * 9 <= timelimit )
      ca : null,
      topic : '',
      places: -1,            // countries visited to win
      timelimit: 99,         // time elapsed before loss (must be multiple of 3)
      difficulty: 0,        // clues: trivial, normal, hard
      cost: 1,

// game state -- initialize on case start
      phase : 0,            // phase
      elapsed_time: 0,      // lose condition

      suspect: null,       // ame suspect
      target: null,         // ame target

      first_country: null,
      country: null,
      valid_country: null,
      next_country: null,
      countries: [],
      clues: [],

      connections_db: [
      ],

// globe state
      destination: null,

// menu state
      summary: false,       // clicked info

      investigate: false,   // clicked investigate
      investigating: -1,    // clicked landmark
      correct: false,       // clicked landmark in correct country

      departures: false,    // clicked departures
      departing: -1,        // clicked destination
      arrived: false,       // reached destination

      warrant: false,       // clicked warrant
      searched: false,      // clicked warrant
      selectStyle: false,
      selectClothing: false,
      selectHair: false,
      selectEquipment: false,
      results: [],
      home: true,

// settings
      txt: english,    // ui language
      debug: false,

// database
      style: [...new Set(game.ames.map( a => a.style ))],
      clothing: [...new Set(game.ames.map( a => a.clothing ))],
      hair: [...new Set(game.ames.map( a => a.hair ))],
      equipment: [...new Set(game.ames.map( a => a.equip ))],
    }),
  }
</script>

<style>
.main {
  height: 100%;
}
.v-card--reveal {
  bottom: 0;
  opacity: 1 !important;
  position: absolute !important;
  width: 100%;
  height: 100%;
}
.v-card-x-reveal {
  left: 0;
  top: -100%;
  opacity: 1 !important;
}
.debug {
  display: none
}
.ame-color {
    background-color: #efe6d4 !important;
}
</style>
