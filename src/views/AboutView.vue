<template>
  <div class="about">
    <div class="row" style="justify-content: center">
      <div class="col-md-6 col-sm-6 col-xs-12" v-for="person in contributors" :key="person.name">
        <div class="box_contact">
          <div class="box__image">
            <img :src="person.image" alt="" />
          </div>
          <div class="box__content">
            <p class="name">{{ person.name }}</p>
            <p>{{ person.info }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import source_data from '@/data/contributors.json'

console.log(source_data)
export default {
  data() {
    return {
      contributors: source_data,
    }
  },
}
</script>
