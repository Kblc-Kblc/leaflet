<template>
  <q-layout view="lHh Lpr lff">
    <q-header>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />
        <q-toolbar-title>
        </q-toolbar-title>
        <q-btn
        flat
        @click="drawerRight = !drawerRight"
        round
        dense
        icon="menu"
        />
      </q-toolbar>
      <div class="q-px-lg q-pt-sm q-mb-md">
        <div class="text-h3">Map</div>
        <div class="text-subtitle1">{{ todaysDate }}</div>
      </div>
      <q-img
      src="../statics/victory.jpg"
      class="header-image absolute-top"/>
    </q-header>

    <q-drawer
        v-model="leftDrawerOpen"
        show-if-above
        :width="250"
        :breakpoint="600"
      >
        <q-scroll-area style="height: calc(100% - 152px); margin-top: 152px; border-right: 1px solid #ddd">
          <q-list padding>
            <q-item
            to="/"
            exact
            clickable
            v-ripple>
              <q-item-section avatar>
                <q-icon name="list" />
              </q-item-section>

              <q-item-section>
                Map
              </q-item-section>
            </q-item>

              <q-item
              to="/help"
              exact
              clickable
              v-ripple>
              <q-item-section avatar>
                <q-icon name="help" />
              </q-item-section>

              <q-item-section>
                Help
              </q-item-section>
            </q-item>

          </q-list>
        </q-scroll-area>

        <q-img class="absolute-top" src="../statics/victory.jpg" style="height: 152px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="../statics/moi.svg">
            </q-avatar>
            <div class="text-weight-bold">Kapkaev Roman</div>
            <div><a class="telegram" href="tg://resolve?domain=kblckblc">Telegram</a></div>
          </div>
        </q-img>
      </q-drawer>

       <q-drawer
        side="right"
        v-model="drawerRight"
        bordered
        :width="200"
        :breakpoint="500"
      >
        <q-scroll-area class="fit">
          <div class="q-pa-sm">

            <q-list>
              <q-select
                v-model.trim="model"
                use-input
                input-debounce="0"
                label="Filter"
                :options="options"
                @filter="filterFn"
                @input="setEmmittedValue"
                style="width: 180px"
              >
                <template v-slot:no-option>
                  <q-item>
                    <q-item-section class="text-grey">
                      No results
                    </q-item-section>
                  </q-item>
                </template>
              </q-select>
            <q-item
              v-for="item of filteredList"
              :key="item.id"
              exact
              clickable
              show-if-above
              v-ripple
              :active="item.id === activeItem"
              @click="clickItem(item.latitude, item.longitude, item.id)"
            >

            <q-item-section>
              {{ item.name }}
            </q-item-section>
          </q-item>

          </q-list>
          </div>
        </q-scroll-area>
      </q-drawer>

    <q-page-container>
      <keep-alive>
      <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink.vue'
import { date } from 'quasar'
const stringOptions = []

export default {
  name: 'MainLayout',
  data () {
    return {
      leftDrawerOpen: false,
      drawerRight: true,
      model: null,
      options: stringOptions,
      emittedValue: null,

    }
  },
  computed: {
    todaysDate() {
      let timeStamp = Date.now()
      return date.formatDate(timeStamp, 'dddd D MMMM')
    },
    markerLatLng: {
      get () {
        return this.$store.state.mapData.markerLatLng
      },
      set (val) {
        this.$store.commit('mapData/updateMarkerLatLng', val)
      }
    },
    activeItem: {
      get () {
        return this.$store.state.mapData.activeItem
      },
      set (val) {
        this.$store.commit('mapData/updateId', val)
      }
    },
    filteredList() {
      let vm = this;
      if (vm.model) {
        return this.markerLatLng.filter(item => { return item.name === this.emittedValue })
      } else {
        return this.markerLatLng.filter(item => { return item.name })
      }

    }
  },
  created() {
    this.markerLatLng.forEach((value, _) => {
      stringOptions.push(value.name)
    });
  },
  methods: {
    clickItem(latitude, longitude, id) {
      this.$root.$emit('centerMapMarker', latitude, longitude)
      this.$store.dispatch('mapData/updateId', id)
    },
    filterFn (val, update) {
      if (val === '') {
        update(() => {
          this.options = stringOptions
          this.model === null
        })
        return
      }

      update(() => {
        const needle = val.toLowerCase()
        this.options = stringOptions.filter(v => v.toLowerCase().indexOf(needle) > -1)
      })
    },
    setEmmittedValue (val) {
      this.emittedValue = val
    }
  }
}
</script>

<style lang="scss">
.header-image {
  height: 100%;
  z-index: -1;
  opacity: 0.2;
  filter: grayscale(100%);
}

.telegram {
  color: #fff;
}
</style>
