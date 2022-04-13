<template>
  <div class="Home_container">
    <div class="friend_list">
      <div class="title_div"><h3>FRIENDS LIST</h3></div>
      <ul @drop="onDrop(1)" @dragover.prevent @dragenter.prevent>
        <li
          v-for="item in friend_list_data"
          :key="item.title"
          draggable="true"
          @dragstart="startDrag($event, item)"
          :class="{ selected: dragged_id === item.id }"
        >
          <img :src="item.photo_50" alt="logo" class="friend_logo" />
          <p>{{ item.first_name }}</p>
          <p>{{ item.last_name }}</p>
        </li>
      </ul>
      <div class="bottom_div">
        <button v-if="!isAuth" class="login" @click="VkAuthorization">
          login in
        </button>
      </div>
    </div>
    <div class="friend_list">
      <div class="title_div"><h3>SELECTED FRIENDS</h3></div>
      <ul @drop="onDrop(2)" @dragover.prevent @dragenter.prevent>
        <li
          v-for="item in selected_list_data"
          :key="item.title"
          draggable="true"
          @dragstart="startDrag($event, item)"
          :class="{ selected: dragged_id === item.id }"
        >
          <img :src="item.photo_50" alt="logo" class="friend_logo" />
          <p>{{ item.first_name }}</p>
          <p>{{ item.last_name }}</p>
        </li>
      </ul>
      <div class="bottom_div">
        <button class="export_to_console" @click="export_to_console">
          export to console
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import VK from "vk-openapi";

export default {
  data() {
    return {
      dragged_id: -1,
      isAuth: false,
      items: [],
    };
  },
  mounted() {
    VK.init({
      apiId: 8135388,
    });
    VK.Auth.getLoginStatus((response) => { //проверка на авторизацию
      if (response.session) {
        this.isAuth = true;
        this.VkGetFriends();
      } else {
        console.log("Авторизуйтесь");
      }
    });
  },
  computed: {
    friend_list_data() {
      return this.items.filter((item) => item.list !== 2);
    },
    selected_list_data() {
      return this.items.filter((item) => item.list === 2);
    },
  },
  methods: {
    startDrag(evt, item) {
      evt.dataTransfer.dropEffect = "move";
      evt.dataTransfer.effectAllowed = "move";
      this.dragged_id = item.id;
    },
    onDrop(list) {
      const item = this.items.find((item) => item.id == this.dragged_id);
      item.list = list;
      this.dragged_id = -1;
    },
    export_to_console() {
      console.log(this.items.filter((el) => el.list == 2));
    },
    VkGetFriends() {
      VK.Api.call(
        "friends.get",
        { order: "hints", fields: "photo_50", v: 5.131 },
        (data) => {
          this.items = data.response.items;
        }
      );
    },
    VkAuthorization() { //авторизация
      VK.Auth.login((response) => {
        if (response.session) {
          this.isAuth = true;
          this.VkGetFriends();
        } else {
          alert("Авторизуйтесь!");
        }
      });
    },
  },
};
</script>


<style scoped>
.Home_container {
  display: flex;
  align-items: center;
  justify-content: space-around;
  width: 90%;
  height: 90vh;
  border-radius: 15px;
  background: rgb(245, 228, 197);
}
.friend_list {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 48%;
  height: 90%;
  border-radius: 13px;
  background: rgb(230, 230, 230);
}
.title_div {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 13px 13px 0 0;
  width: 100%;
  padding: 15px 0;
  background: white;
}
.bottom_div {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 0 0 13px 13px;
  width: 100%;
  padding: 10px 0;
  background: white;
}
.login {
  border: none;
  background: blue;
  padding: 10px 0;
  width: 60%;
  border-radius: 10px;
  color: white;
  font-size: 16px;
  font-weight: 900;
}
.export_to_console {
  border: none;
  background: rgb(116, 116, 245);
  padding: 10px 0;
  width: 60%;
  border-radius: 10px;
  color: white;
  font-size: 16px;
  font-weight: 900;
}

ul {
  list-style: none;
  height: 100%;
  margin: 10px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow-y: auto;
}
ul::-webkit-scrollbar {
  /* chrome based */
  width: 0px;
  /* ширина scrollbar'a */
  height: 0px;
}
li {
  display: flex;
  flex-direction: row;
  align-items: center;
  transition: background-color 0.5s;
  margin-bottom: 10px;
  padding: 5px;
  width: 95%;
  text-align: center;
  border: 2px solid rgba(0, 0, 0, 0.2);
  border-radius: 10px;
  cursor: move;
  background-color: white;

  transition: background-color 0.5s;
}

li:last-child {
  margin-bottom: 0;
}

li p {
  margin-left: 10px;
}

.selected {
  opacity: 0.6;
}

.friend_logo {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}
</style>