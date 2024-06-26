<template>
    <form @submit.prevent="makeRequest">
        <input type="text" placeholder="Search the username" v-model="inputText"/>
        <button>
            <img :src="searchIcon" alt="" />
        </button>
    </form>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import animateOnError from "../utils/animateOnError";

let userData: Object;
let userReposData: Object;

type errorMessage = "An error occurred!" | "Type the user!";

import searchIcon from "../assets/search-icon.svg";
import axios from "../services/axios";

const inputText = ref("");
const searchErrorOccurred = ref(false);
const githubUsernameRegex = /^[a-z\d](?:[a-z\d]|-(?=[a-z\d])){0,38}$/i;
const errorMessage: Ref<errorMessage> = ref("An error occurred!");

const makeRequest = async () => {
    inputText.value = inputText.value.trim();

    if(inputText.value === "") {
        animateOnError();
        errorMessage.value = "Type the user!";
        searchErrorOccurred.value = true;
    } else {
        if (inputText.value.match(githubUsernameRegex)) {
            try {
                userData = await axios.get(inputText.value);
                userReposData = await axios.get(`${inputText.value}/repos`);
                console.log(userData);
                searchErrorOccurred.value = false;
            } catch (error) {
                console.error(error);
                errorMessage.value = "An error occurred!";
                searchErrorOccurred.value = true;
            }
        } else {
            console.error(error);
        }
    }
}
</script>

<style scoped lang="scss">
@use "../app.scss";

form {
  width: min(95%, 600px);
  margin: auto;
  @include app.flex(row);

  input {
    width: 65%;
    height: 32px;
    padding: 0 32px 0 7px;
    border: app.$border;
    background: app.$main-bg;
    color: app.$text;
    border-radius: 6px;

    &::placeholder {
      color: app.$input-placeholder;
      font-size: 0.9rem;
    }

    &:focus {
      outline: 0;
      border: 1px solid app.$input-focus;
      &::placeholder {
        color: app.$input-focus-placeholder;
      }
    }
  }

  button {
    border-radius: 0 6px 6px 0;
    height: 32px;
    width: 40px;
    margin-left: -28px;
    background: app.$input-focus;
    border: 0;

    @include app.flex();
    
    img {
      height: 65%;
    }
  }
}
</style>