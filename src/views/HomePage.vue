<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>MAMAP</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <GoogleMap
        v-if="!isLoading"
        ref="mapRef"
        :api-key="apiKey"
        style="width: 100%; height: 100%;"
        :center="center"
        :zoom="15"
      >
        <Marker
          v-for="(place, i) in places"
          :key="`place-${i}`"
          :options="{
            position: { lat: Number(place.lat), lng: Number(place.lng) },
            icon: {
              url: `assets/icon/icon_${place.icon_type}.png`,
              scaledSize: { width: 50, height: 50 }
            },
          }"
          @click="selectPlace(i)"
        >
          <InfoWindow>
            <div class="place_name">{{ place.name }}</div>
            <div class="chip_wrapper">
              <div class="chip" :style="{backgroundColor: place.type_color}">{{ place.type_name }}</div>
            </div>
            <div class="place_img_wrapper">
              <img :src="place.image" />
            </div>
            <div class="place_desc">{{ place.desc }}</div>
            <div v-if="place.site_url" class="place_url"><a :href="place.site_url">さらに詳しい情報</a></div>
          </InfoWindow>
        </Marker>
      </GoogleMap>
      <PageLoading
        v-else
      />
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
} from "@ionic/vue";
import { defineComponent } from "vue";
import axios from "axios";
import { GoogleMap, Marker, InfoWindow } from "vue3-google-map";

import PageLoading from '../components/PageLoading.vue'

export default defineComponent({
  name: "HomePage",
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    GoogleMap,
    Marker,
    InfoWindow,
    PageLoading,
  },
  data(): {
    isLoading: boolean;
    places: object[];
    apiKey: string;
    center: object;
    selectedPlace: number | null;
  } {
    return {
      isLoading: false,
      places: [],
      apiKey: "AIzaSyDw9IUIdkHXqPBbCbFF9DuzfP_MGJ2WUWY",
      center: {
        lat: 33.076404,
        lng: 131.861819,
      },
      selectedPlace: null,
    };
  },
  created() {
    this.isLoading = true
    axios
      .get(
        "https://script.google.com/macros/s/AKfycbyGydBlxUX0yN0qe7U97VcJq7Kuu8iSDlvWhtHs_w4zAE8ZF2QhMRJzBOpsShS9NABT7Q/exec"
      )
      .then((res) => {
        const tmpPlaces = res.data
        this.places = tmpPlaces.map((obj: any) => {
          // タイプ番号とタイプ名を分割
          const [type_no, type_name] = obj.type.split('. ')
          obj.type_no = type_no
          obj.type_name = type_name

          // タイプカラーを格納
          const typeColors = [
            'darkred',
            'deeppink',
            'midnightblue',
            'orangered',
            'darkgreen'
          ]
          obj.type_color = typeColors[Number(obj.type_no) - 1]

          // アイコン種類を格納
          const iconTypes = [
            'restaurant',
            'child',
            'lesson',
            'hoby',
            'sightseeing',
          ]
          obj.icon_type = iconTypes[Number(obj.type_no) - 1]

          console.log(obj)
          return obj
        })
        console.log(this.places);
        this.isLoading = false
      });
  },
  methods: {
    selectPlace(i: number) {
      this.selectedPlace = i;
    },
  },
});
</script>

<style scoped>
.place_name {
  font-weight: bold;
  font-size: 1rem;
}

.place_img_wrapper {
  max-width: 200px;
}

.place_img_wrapper img {
  width: 100%;
}

.place_url {
  display: flex;
  justify-content: end;
}

.chip_wrapper {
  display: flex;
}

.chip {
  color: #fff;
  font-weight: bold;
  border-radius: 10px;
  padding: .1rem 1rem;
  font-size: .8em;
  margin: .3rem 0;
}

</style>
