<template>
  <div id="app">
    <h1>Simple Purchase Calculator</h1>
    <b-list-group>
      <b-list-group-item header-tag="header" class="p-4 bg-secondary text-light" role="tab" v-b-toggle.users>
        Users
      </b-list-group-item>
      <b-collapse id="users" visible accordion="main-accordion" role="tabpanel">
        <b-list-group>
          <b-list-group-item
              v-for="user in users"
              v-bind:key="user.hash"
            >
            <b-row align-v="center">
              <b-col>
                <EmojiPrependedInput :user="user" placeholder="Enter username"/>
              </b-col>
              <b-col>
                ID: {{ user.hash }}
              </b-col>
            </b-row>
          </b-list-group-item>
          <b-list-group-item>
            <b-row align-h="end">
              <b-button
                  variant="success"
                  class="mx-4 px-4"
                  @click="newUser"
                >New
              </b-button>
            </b-row>
          </b-list-group-item>
        </b-list-group>
      </b-collapse>

      <b-list-group-item header-tag="header" class="p-4 bg-secondary text-light" role="tab" v-b-toggle.purchases>
        Purchases
      </b-list-group-item>
      <b-collapse id="purchases" accordion="main-accordion" role="tabpanel">
        <b-list-group>
          <b-list-group-item
              v-for="item in items"
              v-bind:key="item.hash"
            >
            <b-row align-v="center">
              <b-col>
                <b-form-input
                    placeholder="Enter item name"
                    v-model="item.name"
                  >
                </b-form-input>
              </b-col>
              <b-col>
                <b-form-input
                    type="number"
                    placeholder="Enter item value"
                    v-model="item.value"
                    :number="true"
                  >
                </b-form-input>
              </b-col>
              <b-col>
                <UsersSelector :options="getUsersEmojis()" v-model="item.users"/>
              </b-col>
            </b-row>
          </b-list-group-item>
          <b-list-group-item>
            <b-row align-h="around">
              <b-col>
                Total: {{ total() }} <b-form-input v-model="units" class="px-1" style="width: 1.4rem; display: inline"></b-form-input>
              </b-col>
              <b-button
                  variant="success"
                  class="mx-4 px-4"
                  @click="newItem"
                >New
              </b-button>
            </b-row>
          </b-list-group-item>
        </b-list-group>
      </b-collapse>

      <b-list-group-item header-tag="header" class="p-4 bg-secondary text-light" role="tab" v-b-toggle.final>
        Final count
      </b-list-group-item>
      <b-collapse id="final" accordion="main-accordion" role="tabpanel">
        <b-row v-for="i in Array(Math.ceil(users.length / cardsPerRow)).keys()" v-bind:key="users[i*cardsPerRow].hash + '_display'">
          <b-col v-for="user in users.slice(i * cardsPerRow , i * cardsPerRow + cardsPerRow)" v-bind:key="user.hash + '_finalCard'">
            <b-card
                :title="`${user.emoji} ${user.username}`"
              >
              <b-card-text>
                <b-list-group>
                  <b-list-group-item v-for="item in partakes(user)" v-bind:key="`${user.hash}_${item.hash}`">
                    <b-row align-v="center">
                      <h3>{{ item.item.name }}</h3>
                      <b-col></b-col>
                      {{ item.partial.toFixed(2) }}{{ units }} out of {{ (item.item.value)? item.item.value: 0 }}{{ units }}
                    </b-row>
                  </b-list-group-item>
                  <b-list-group-item v-if="partakes(user).length !== 0">
                    <b-row align-v="center">
                      <h2>Total</h2>
                      <b-col></b-col>
                      <h3>{{ partakes(user).reduce((sum, cur) => sum+cur.partial, 0).toFixed(2) }}{{ units }}</h3>
                    </b-row>
                  </b-list-group-item>
                  <b-list-group-item v-else>
                    There's nothing to show yet!
                  </b-list-group-item>
                </b-list-group>
              </b-card-text>
            </b-card>
          </b-col>
        </b-row>
      </b-collapse>

    </b-list-group>
  </div>
</template>

<script>
import * as uuid from 'uuid';
import randomEmoji from './utils/random-emoji';

import EmojiPrependedInput from './components/EmojiPrependedInput';
import UsersSelector from './components/UsersSelector';

export default {
  name: 'App',
  components: {
    EmojiPrependedInput,
    UsersSelector,
  },
  data() {
    return {
      items: [],
      users: [],
      units: '€',
      cardsPerRow: 3,
    };
  },
  methods: {
    newItem() {
      this.items.push({
        hash: uuid.v4(),
        name: '',
        value: undefined,
        users: [],
      })
    },
    newUser() {
      this.users.push({
        hash: uuid.v4(),
        username: '',
        emoji: randomEmoji(),
      })
    },
    changeEmoji(user, emoji) {
      console.log(this.$vnode.user)
      console.log(emoji)
    },
    total() {
      return this.items.reduce((c,item) => (item.value)? c+item.value: c, 0);
    },
    getUsersEmojis() {
      let ret = {}
      for (let i in this.users) {
        let user = this.users[i];
        ret[user.hash] = user.emoji
      }
      return ret;
    },
    partakes(user) {
      let ret = [];
      for (let i in this.items) {
        const item = this.items[i]
        if (item.users.includes(user.hash))
          ret.push({
            item: item,
            partial: ((item.value)? item.value: 0) / item.users.length
          })
      }

      return ret;
    }
  },
  mounted() {
    this.newItem();
    this.newUser();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.list-group-item[role="tab"] {
  cursor: pointer;
  transition: background-color 0.5s ease-in-out;
}

.list-group-item.not-collapsed[role="tab"] {
  background-color: #007bff!important;
}

h1, h2, h3, h4, h5, h6 {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
</style>
