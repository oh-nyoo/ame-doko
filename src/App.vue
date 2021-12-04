<template>
  <v-app>
    <v-app-bar
      v-show="selected != ''"
      app
      color="brown accent-4"
      dense
      dark
    >
      <v-tabs
        centered
        class="ml-n9"
      >
        <v-tab
          v-for="link in links"
          :key="link"
          v-model="selected"
          @click="selected = link; load_storage()"
        >
          {{ link }}
        </v-tab>
      </v-tabs>
    </v-app-bar>


    <v-main v-show="selected == 'Play'" class="ame-color">
      <Main ref="main"/>
    </v-main>

    <v-main v-show="selected == ''" class="splash" >
        <v-scroll-y-transition>
        <div class="splash-reveal">
        <div v-if="tutorial == 0" class="d-flex justify-center">
            <div class="title-left d-flex justify-center" width="100">
                <video playsinline autoplay muted loop> <source src="/img/embunlebah_Pjj9QIZjY3CuR4RN.mp4" type="video/mp4"></video>
            </div>
            <div class="title-box title-right d-flex justify-center flex-column">
                <div class="text-h1 font-weight-bold ma-6 text-center" disabled>Ame doko?</div>
                <div class="text-center">
                    <v-btn
                      x-large
                      :outlined="get_rank() != null"
                      :elevation="(get_rank() == null) ? 2 : 0"
                      @click="tutorial = 1; clear_storage()"
                        class="ma-2"
                      >New Game</v-btn>
                    <v-btn
                      v-if="get_rank()"
                      x-large
                      @click="selected = 'Play';"
                        class="ma-2"
                      >Continue</v-btn>
                </div>
            </div>
        </div>
        </div>
        </v-scroll-y-transition>
        <v-scroll-y-transition>
        <div v-if="tutorial == 1" class="splash-reveal" style="position: absolute !important">
            <div class="title-left d-flex justify-center" width="100">
            </div>
            <div class="title-box title-left d-flex justify-center flex-column">
                <div class="text-body1 ma-6 text-left" disabled v-html="txt.intro_0">{{txt.intro_0}}</div>
                <div class="text-center">
                    <v-btn
                      x-large
                      outlined
                      elevation="2"
                      @click="tutorial = 2"
                        class="ma-2"
                      >Continue</v-btn>
                </div>
            </div>
        </div>
        </v-scroll-y-transition>
        <v-scroll-y-transition>
        <div v-if="tutorial >= 2 && tutorial < 5" class="d-flex justify-center splash-reveal">
            <div class="title-left d-flex justify-center">
              <v-img style="border-radius: 32px;" src="/img/enmaa.jpg" max-width="544"/>
            </div>
            <div v-if="tutorial==2" class="title-box title-right d-flex justify-center flex-column">
                <div class="text-body1 ma-6 text-left" disabled v-html="txt.intro_1">{{txt.intro_0}}</div>
                <div class="text-center">
                    <v-btn
                      x-large
                      outlined
                      elevation="2"
                      @click="tutorial = 3"
                        class="ma-2"
                      >Continue</v-btn>
                </div>
            </div>
            <div v-else-if="tutorial==3" class="title-box title-right d-flex justify-center flex-column">
                <div class="text-body1 ma-6 text-left" disabled v-html="txt.intro_2">{{txt.intro_0}}</div>
                <div class="text-center">
                    <v-btn
                      x-large
                      outlined
                      elevation="2"
                      @click="tutorial = 4"
                        class="ma-2"
                      >Continue</v-btn>
                </div>
            </div>
            <div v-else class="title-box title-right d-flex justify-center flex-column">
                <div class="text-body1 ma-6 text-left" disabled v-html="txt.intro_3">{{txt.intro_0}}</div>
                <div class="text-center">
                    <v-btn
                      x-large
                      outlined
                      elevation="2"
                      @click="tutorial = 5; selected='Play'; reload_play()"
                        class="ma-2"
                      >Continue</v-btn>
                </div>
            </div>
        </div>
        </v-scroll-y-transition>
    </v-main>
    <v-main v-show="selected == 'Amedex'" class="ame-color">
        <div class="text-h2 text-center ma-8">Amedex ({{ compendium.length }}/{{ ames.length }})</div>
        <v-container>
            <v-row class="d-flex justify-center" max-width="800">
                        <v-col x-lg="2" lg="3" md="4" sm="6" x-sm="1" v-for="ame in ames" :key="ame.name">
                            <v-card height="750" style="overflow: hidden;" :elevation="(compendium.includes(ame.name)) ? 5 : 1">
                                <v-img
                                    height="500"
                                    :src="'/img/targets/' +ame.img"
                                    :style="'filter: grayscale(' + ((compendium.includes(ame.name)) ? 0 : 90) + '%)'"
                                />
                                <v-divider></v-divider>
                                <v-card-title>
                                    {{ ame.name }}
                                    <v-icon
                                        v-if="compendium.includes(ame.name)"
                                      right
                                      color="green darken-2"
                                    >
                                      mdi-check-bold
                                    </v-icon>

                                    <v-icon
                                      v-else
                                      right
                                      color="grey lighten-1"
                                    >
                                      mdi-check-bold
                                    </v-icon>
                                </v-card-title>
                                <v-divider class="mx-4"></v-divider>
                                <v-card-text>
                                    <p>Outfit: {{ ame.style }}</p>
                                    <p>Accessories: {{ ame.clothing }}</p>
                                    <p>Hair: {{ ame.hair }}</p>
                                    <p>Equipment: {{ ame.equip }}</p>
                                </v-card-text>
                            </v-card>
                        </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" class="mt-6" style="display:none">
                        <p class="text-h4">
                            Achievements
                        </p>
                        <v-row>
                        <v-col cols="12">
                            <div class="text-h5">
                                Version 0.1
                            </div>
                            <div class="text-body1">
                                Prototype release
                            </div>
                        </v-col>
                        </v-row>
                </v-col>
            </v-row>
        </v-container>
    </v-main>
    <v-main v-show="selected == 'Credits'" class="ame-color">
        <v-container class="grey lighten-5 pa-12" style="height:100%" elevation="5">
            <v-row>
                <v-col cols="12" class="mt-6">
                        <p class="text-h4">
                            Special Thanks
                        </p>
                        <v-row>
                        <v-col cols="3">
                        <div class="text-h5">Amelia Watson</div>
                        <div class="text-body1">for #hologlobe</div>
                        </v-col>
                        <v-col cols="3">
                        <div class="text-h5">Cover Corporation</div>
                        <div class="text-body1">for hololive</div>
                        </v-col>
                        <v-col cols="3">
                        <div class="text-h5">Fan Wikis</div>
                        <div class="text-body1">Hololive English Wiki</div>
                        <div class="text-body1">Virtual YouTuber Wiki</div>
                        </v-col>
                        <v-col cols="3">
                        <div class="text-h5">You</div>
                        <div class="text-body1">for playing</div>
                        </v-col>
                        </v-row>
                </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" class="mt-6">
                        <p class="text-h4">
                            Credits
                        </p>
                        <v-row>
                        <v-col cols="3">
                            <div class="text-h5">
                                Art and Animations
                            </div>
                            <div v-text="name" :key="name" v-for="name in artists" class="text-body1">
                            </div>
                        </v-col>
                        <v-col cols="3">
                        <div class="text-h5">
                            Writing
                        </div>
                        <div v-text="name" :key="name" v-for="name in writers" class="text-body1">
                        </div>
                        </v-col>
                        <v-col cols="3">
                        <div class="text-h5">
                            Programming and Editing
                        </div>
                        <div v-text="name" :key="name" v-for="name in developers" class="text-body1">
                        </div>
                        </v-col>
                        <v-col cols="3">
                        <div class="text-h5">
                            Playtesters
                        </div>
                        <div v-text="name" :key="name" v-for="name in testers" class="text-body1">
                        </div>
                        </v-col>
                        </v-row>
                </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" class="mt-6">
                        <p class="text-h4">
                            Wishlist
                        </p>
                        <v-row>
                        <v-col cols="12">
                            <div class="text-body1">
                                <ul>
                                    <li>More #hologlobe user content: maps, descriptions, flags, clues</li>
                                    <li>More clues, harder clues</li>
                                    <li>Larger Amedex</li>
                                    <li>Custom art, animations, sounds</li>
                                    <li>Custom UI, polishing</li>
                                    <li>Achievements</li>
                                    <li>Easter Eggs</li>
                                    <li>Support for other languages</li>
                                </ul>
                            </div>
                        </v-col>
                        </v-row>
                </v-col>
            </v-row>
            <v-row>
                <v-col cols="12" class="mt-6">
                        <p class="text-h4">
                            Changelog
                        </p>
                        <v-row>
                        <v-col cols="12">
                            <div class="text-h5">
                                Alpha 0.1
                            </div>
                            <div class="text-body1">
                                First prototype
                            </div>
                        </v-col>
                        </v-row>
                </v-col>
            </v-row>
        </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Main from './components/Main';
import { english } from './data/text.js'
import { developers, artists, writers, testers } from './data/contrib.js'
import { game } from './data/game.js'

export default {
  name: 'App',

  components: {
    Main,
  },
  methods: {
        get_rank() {
            return localStorage.getItem('rank')
        },
        load_storage() {
            let compendium = localStorage.getItem("compendium")
            if(compendium) this.compendium = JSON.parse(compendium)
        },
        clear_storage() {
            localStorage.clear()
        },
        reload_play() {
            this.$refs.main.loadData()
        }
    },
    computed: {
    },
  data: () => ({
      selected : '',
      links: [
        'Play',
        'Amedex',
        'Credits',
      ],
      compendium : [],
      developers: developers,
      artists: artists,
      writers: writers,
      testers: testers,
      ames: game.ames,
      tutorial: 0,
      txt: english,
  }),
};
</script>

<style>
.splash {
    background-color: #fff6e4;
}
.title-box {
    width: 720px;
    height: 720px;
}
.title-left {
    padding-left:10vw;
}
.title-right {
    padding-right:10vw;
}
Main {
    top: 0px;
    position: absolute !important;
    width: 100%;
    height: 100%;
}
.splash-reveal {
  opacity: 1 !important;
}
.ame-color {
    background-color: #fff6e4 !important;
}
</style>
