<template>
  <div class="person">
    <div class="person__photo">
      <img :src="person.picture" alt="photo" />
    </div>
    <div class="person__info">
      <div class="person__info-name">
        <b>{{ person.name }} ({{ person.age }})</b>
      </div>

      <div class="person__info--item person__info-email">
        <a
          :href="'malito:' + person.email"
          class="person__info--title emailLink"
        >
          : {{ person.email }}
        </a>
      </div>
      <div class="person__info--item person__info-date">
        : {{ formatedDate }}
      </div>
      <div class="person__info-about">
        <p class="person__info--title">О себе:</p>
        {{ person.about }}
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    person: {
      type: Object,
      default: null,
      isUserOpenned: false,
    },
  },
  computed: {
    formatedDate() {
      return this.person.registered;
    },
  },
};
</script>

<style scoped>
.person__info--item {
  display: flex;
  align-items: center;
}

.person__info-email::before {
  content: "";
  width: 24px;
  height: 24px;
  display: inline-block;
  background-image: url("~@/assets/icons/mail_outline_black_24dp.svg");
  background-repeat: no-repeat;
}

.person__info-date::before {
  content: "";
  width: 24px;
  height: 24px;
  display: inline-block;
  background-image: url("~@/assets/icons/date_range_black_24dp.svg");
  background-repeat: no-repeat;
}

.person__info--title {
  font-size: 18px;
  font-weight: bold;
}

.person__photo img {
  max-width: 100%;
  display: block;
  border-radius: 50%;
  margin-bottom: 20px;
}

.person__info {
  display: grid;
  grid-gap: 8px;
}

.person__info-name {
  margin-bottom: 10px;
  font-size: 20px;
  color: #4b74e6;
}

.emailLink {
  text-decoration: none !important;
  color: inherit;
}
</style>
