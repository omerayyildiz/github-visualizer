<template>
  <div
    class="
      flex
      sm:grid
      items-center
      justify-center
      min-h-screen min-w-screen
      bg-gray-500
    "
  >
    <input
      type="text"
      class="mt-8 h-8 rounded-full p-2 bg-gray-300 text-gray-800"
      placeholder="Search..."
      v-model="query"
      @keypress="getUser"
    />
    <div
      class="
        bg-gray-700
        p-6
        mb-16
        sm:grid
        rounded-lg
        items-start
        justify-items-center
      "
    >
      <div v-if="seen" class="grid md:flex items-center">
        <a
          class="rounded-full w-56 h-56"
          :href="userinfo.avatar_url"
          title="If you want this image click the photo"
        >
          <img :src="userinfo.avatar_url" class="rounded-full w-56 h-56" />
        </a>
        <div class="ml-4">
          <a :href="userinfo.html_url">
            <h1
              class="
                flex
                text-gray-200 text-4xl
                font-semibold
                mt-5
                items-center
              "
            >
              {{ userinfo.login }}

              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="24"
                height="24"
                viewBox="0 0 24 24"
                fill="none"
                class="ml-2 text-gray-200"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <g fill="none" fill-rule="evenodd">
                  <path
                    d="M18 14v5a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8c0-1.1.9-2 2-2h5M15 3h6v6M10 14L20.2 3.8"
                  />
                </g>
              </svg>
            </h1>
          </a>
          <p class="text-gray-300 mt-2">{{ userinfo.bio }}</p>
          <div class="flex space-x-3">
            <h3 class="text-gray-300 text-sm">
              {{ "Followers: " + userinfo.followers }}
            </h3>
            <h3 class="text-gray-300 text-sm">
              {{ "Following: " + userinfo.following }}
            </h3>
          </div>
          <div class="space-x-3 mt-4">
            <a
              class="links"
              :href="getTwitter()"
              v-if="userinfo.twitter_username != null"
            >
              {{ "@" + userinfo.twitter_username }}
            </a>
            <a :href="getRepo()" class="links">
              {{ "Repos: " + userinfo.public_repos }}
            </a>
            <a :href="getWebsite()" class="links" v-if="userinfo.blog != null">
              Website
            </a>
            <a :href="getFollowers()" class="links"> Followers </a>
          </div>
          <div class="flex mt-2">
            <h2 class="text-gray-200">
              {{ "Location: " + getLocation() }}
            </h2>
          </div>
        </div>
      </div>
      <div v-else class="flex justify-center items-center">
        <h1 class="text-gray-200">
          Hello to Github Visualizer. Write textbox to username
        </h1>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userinfo: [],
      query: "",
      seen: false,
    };
  },
  methods: {
    getFollowers: function () {
      return "https://github.com/" + this.userinfo.login + "?tab=followers";
    },
    getTwitter: function () {
      return "https://twitter.com/" + this.userinfo.twitter_username;
    },
    getRepo: function () {
      return "https://github.com/" + this.userinfo.login + "?tab=repositories";
    },
    getWebsite: function () {
      return "http://" + this.userinfo.blog;
    },
    getLocation: function () {
      if (this.userinfo.location != null) {
        return this.userinfo.location;
      } else {
        return "Anywhere In The Universe";
      }
    },
    getUser(e) {
      if (e.key == "Enter") {
        this.axios
          .get("https://api.github.com/users/" + this.query)
          .then((response) => {
            if (response.data.login != "Not Found") {
              this.seen = true;
              this.userinfo = response.data;
            } else {
              this.seen = false;
            }
          });
      }
    },
  },
};
</script>

<style>
p {
  width: 300px;
}
.sm {
  @apply w-96;
}
.links {
  @apply text-lg font-semibold bg-gray-200 rounded-md p-2 items-center;
}
</style>
