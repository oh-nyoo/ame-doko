<template>
  <v-main class="main grey lighten-3">
    <v-container class="d-flex ma-2 justify-center mx-auto">


<v-card
  class="ma-2"
  width="408"
  height="725"
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
      {{ cases_complete }}/{{ cases_needed }} ranked cases until next promotion
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
          <v-list-item two-line k="selected=ca" :disabled="!debug && ca.rank > rank">
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


<!-- RIGHT card -->

<v-card
  class="ma-2"
  width="544"
  height="725"
>
  <v-img height="725" style="border-radius: 4px;" src="/img/enmaa.jpg"/>
</v-card>

      </v-container>
    </v-main>
</template>

<script>
//import { holodata } from '../data/hololive.js'
import { game } from '../data/game.js'

export default {
    name: 'Menu',
    components: {

    },
    created: function () {
    },
    methods: {
    },
    computed: {
        rank_name() {
            return this.txt.ranks[this.rank]
        },
        cases_complete () { return this.player_wins.filter( x => x >= this.rank ).length },
        cases_needed () {
          return (this.rank+1 < game.promotions.length) ? game.promotions[this.rank+1]
                                                        : game.promotions[this.rank]
        },
        cases () { return game.cases },

    },
    props: [
              'rank',
              'player_wins',
              'cases_completed',
              'compendium',
              'txt',
              'selected',
    ],
    data: () => ({
      debug: false,
    })
}
</script>

<style>
.main {
  height: 100%;
}
</style>
