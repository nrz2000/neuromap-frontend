<template>
  <v-card>
    <v-card-title>{{ meet.name }}</v-card-title>
    <v-card-subtitle>Дата проведения (с/до):
      {{ meet.date.from | getFormattedDate }} / {{ meet.date.to | getFormattedDate }}</v-card-subtitle>
    <v-card-subtitle class="pt-0">Статус встречи: {{ status }}</v-card-subtitle>
    <v-card-text v-if="meet.facilitator && meet.secretary">
      <v-row>
        <v-col v-if="meet.facilitator" cols="6">
          <v-card-text class="pb-1 pl-0">Facilitator:</v-card-text>
          <UserCard
            :user="userById(meet.facilitator)"
          >
          </UserCard>
        </v-col>
        <v-col v-if="meet.secretary" cols="6">
          <v-card-text class="pb-1 pl-0">Secretary:</v-card-text>
          <UserCard
            :user="userById(meet.secretary)"
          >
          </UserCard>
        </v-col>
      </v-row>
    </v-card-text>
    <v-list
      v-if="meet.members.length > 0"
      class="pb-5"
    >
      <v-card-text class="pb-1">Members:</v-card-text>
      <v-list-item class="flex flex-column align-start">
        <UserCard
          v-for="(members, index) in meet.members"
          :key="index"
          :user="userById(members)"
          :class="{ 'mt-3': index > 0 }"
        ></UserCard>
      </v-list-item>
    </v-list>
  </v-card>
</template>

<script>
import { mapGetters } from 'vuex';
import UserCard from '@/components/UserCard.vue';

export default {
  filters: {
    getFormattedDate(msec) {
      if (!msec) return '';
      const time = new Date(msec);
      const months = [
        'Января',
        'Февраля',
        'Марта',
        'Апреля',
        'Мая',
        'Июня',
        'Июля',
        'Августа',
        'Сентября',
        'Октября',
        'Ноября',
        'Декабря',
      ];
      const month = time.getMonth();
      const day = time.getDate();
      const hours = time.getHours();
      const minutes = time.getMinutes();
      return `${
        day < 10 ? `0${day}` : day
      } ${
        months[month]
      } ${
        hours < 10 ? `0${hours}` : hours
      }:${
        minutes < 10 ? `0${minutes}` : minutes
      }`;
    },
  },
  props: {
    meet: Object,
  },
  components: {
    UserCard,
  },
  computed: {
    ...mapGetters(['userById']),
    status() {
      if (!this.meet.date?.from || !this.meet.date?.to) return '';
      const now = Date.now();
      if (now < this.meet.date.from) return 'Запланирована';
      if (now > this.meet.date.to) return 'Завершена';
      return 'Проходит';
    },
  },
};
</script>
