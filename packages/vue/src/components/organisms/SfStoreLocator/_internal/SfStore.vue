<template>
  <div class="sf-store">
    <slot name="distance">
      <div :class="{ 'display-none': !distance }" class="sf-store__distance">
        <span>{{ distance }}km</span> away from you
      </div>
    </slot>
    <div :class="{ 'sf-store__media': picture }" @click="centerOn(latlng)">
      <slot name="media">
        <SfImage
          v-if="picture"
          :src="picture"
          :alt="`${name} picture`"
          :width="pictureWidth"
          :height="pictureHeight"
        />
        <SfIcon v-else icon="marker" class="sf-store__icon" />
      </slot>
    </div>
    <div class="sf-store__info">
      <div class="sf-store__heading">
        <slot name="heading">
          <div class="sf-store__name">
            {{ name }}
          </div>
        </slot>
      </div>
      <slot>
        <div :class="{ 'display-none': !address }" class="sf-store__address">
          {{ address }}
        </div>
        <SfCharacteristic
          :class="{ 'display-none': !phone }"
          icon="phone"
          size-icon="16px"
          class="sf-store__property"
        >
          <template #text>
            <a
              v-focus
              :href="`tel:${phone}`"
              tabindex="0"
              class="sf-store__property-link"
              >{{ phone }}</a
            >
          </template>
        </SfCharacteristic>
        <SfCharacteristic
          :class="{ 'display-none': !email }"
          icon="mail"
          size-icon="16px"
          class="sf-store__property"
        >
          <template #text>
            <a
              v-focus
              :href="`mailto:${email}`"
              tabindex="0"
              class="sf-store__property-link"
              >{{ email }}</a
            >
          </template>
        </SfCharacteristic>
      </slot>
    </div>
  </div>
</template>
<script>
import { focus } from "../../../../utilities/directives";
import SfImage from "../../../atoms/SfImage/SfImage.vue";
import SfIcon from "../../../atoms/SfIcon/SfIcon.vue";
import SfCharacteristic from "../../../molecules/SfCharacteristic/SfCharacteristic.vue";
export default {
  name: "SfStore",
  inject: [
    "registerStore",
    "removeStore",
    "centerOn",
    "locatorData",
    "getGeoDistance",
  ],
  components: {
    SfImage,
    SfCharacteristic,
    SfIcon,
  },
  directives: {
    focus: focus,
  },
  props: {
    latlng: {
      type: Array,
      default: () => [],
    },
    name: {
      type: String,
      default: "",
    },
    picture: {
      type: String,
      default: null,
    },
    pictureWidth: {
      type: [String, Number],
      default: null,
    },
    pictureHeight: {
      type: [String, Number],
      default: null,
    },
    address: {
      type: String,
      default: "",
    },
    phone: {
      type: String,
      default: null,
    },
    email: {
      type: String,
      default: null,
    },
  },
  computed: {
    distance() {
      if (this.locatorData && this.locatorData.userPosition) {
        const dictLatLng = {
          lat: this.latlng[0],
          lng: this.latlng[1],
        };
        return this.getGeoDistance(this.locatorData.userPosition, dictLatLng);
      }
      return null;
    },
  },
  created() {
    if (this.registerStore) {
      this.registerStore(this.$props);
    }
  },
  beforeDestroy() {
    if (this.removeStore) {
      this.removeStore(this.$props);
    }
  },
};
</script>
