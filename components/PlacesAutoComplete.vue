<template>
  <input
    ref="autocomplete"
    placeholder="Search"
    class="search-location appearance-none block w-full bg-grey-lighter text-grey-darker border rounded py-3 px-4 mb-3 leading-tight focus:outline-none focus:bg-white"
    onfocus="value = ''"
    type="text" >
</template>

<script>
import _ from 'lodash'

export default {

  mounted () {
    /* global google */

    const autocomplete = new google.maps.places.Autocomplete(
      (this.$refs.autocomplete),
      {types: ['geocode']}
    )

    autocomplete.addListener('place_changed', () => {
      const place = autocomplete.getPlace()
      const address = this.toAddressObject(place)

      this.$emit('input', address)
    })
  },
  methods: {
    toAddressObject (place) {
      const ac = place.address_components
      const loc = place.geometry.location

      const components = ac.reduce((acc, addressComponent) => {
        if (addressComponent.types.length) {
          acc[_.camelCase(addressComponent.types[0])] = {
            shortName: addressComponent.short_name,
            longName: addressComponent.long_name
          }
        }

        return acc
      }, {})

      const formattedAddress = place.formatted_address
      const location = {
        lat: loc.lat(),
        lng: loc.lng()
      }

      return {
        id: place.place_id,
        components,
        formattedAddress,
        location
      }
    }
  }
}
</script>
