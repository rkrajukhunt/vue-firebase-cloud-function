<template>
    <div class="col-span-1 lg:col-span-6">
    <h4 class="text-3xl text-gray-700 mb-5">Vue firestore Demo</h4>
    <br />
    <div class="notification" v-if="formComplete">Form completion - {{formComplete}}</div>
    <div class="notification is-success" v-if="state === 'synced'">Form is synced with Firestore</div>
    <div
      class="notification is-link"
      v-else-if="state === 'modified'"
    >From data changed, will sync with Firebase</div>
    <div
      class="notification is-warning"
      v-else-if="state === 'revoked'"
    >From data and Firebase revoked to original data</div>
    <div
      class="notification is-danger"
      v-else-if="state === 'error'"
    >Failed to save to Firestore. {{ errorMessage }}</div>
    <div class="notification is-info" v-else-if="state === 'loading'">Loading...</div>
    <br />
    <form @input="fieldUpdate">
        <div class="p-1 rounded-md shadow-md bg-white">
            <div class="flex justify-center">
                <div class="w-1/3 px-3">
                    <label class="block mb-3 text-gray-600" for="">First name</label>
                    <!-- <input type="tel" class="border border-gray-500 rounded-md inline-block py-2 px-3 w-full text-gray-600 tracking-widest" /> -->
                    <input class="border border-gray-500 rounded-md inline-block py-2 px-3 w-full text-gray-600 tracking-widest" type="text" id="fname" name="fname" v-model="userdata.firstname" />
                </div>
            </div>
        </div>
        <div class="p-1 rounded-md shadow-md bg-white">
            <div class="flex justify-center">
                <div class="w-1/3 px-3">
                    <label class="block mb-3 text-gray-600" for="">Last name</label>
                    <input class="border border-gray-500 rounded-md inline-block py-2 px-3 w-full text-gray-600 tracking-widest" type="text" id="fname" name="fname" v-model="userdata.lastname" />
                </div>
            </div>
        </div>
        <div class="p-1 rounded-md shadow-md bg-white">
            <div class="flex justify-center">
                <div class="w-1/3 px-3">
                    <label class="block mb-3 text-gray-600" for="">User name</label>
                    <input class="border border-gray-500 rounded-md inline-block py-2 px-3 w-full text-gray-600 tracking-widest" type="text" id="fname" name="fname" v-model="userdata.username" />
                </div>
            </div>
        </div>
        <div class="p-1 rounded-md shadow-md bg-white">
            <div class="flex justify-center">
                <div class="w-1/3 px-3">
                    <label class="block mb-3 text-gray-600" for="">State</label>
                    <input class="border border-gray-500 rounded-md inline-block py-2 px-3 w-full text-gray-600 tracking-widest" type="text" id="fname" name="fname" v-model="userdata.state" />
                </div>
            </div>
        </div>
        <div class="p-1 rounded-md shadow-md bg-white">
            <div class="flex justify-center">
                <div class="w-1/3 px-3">
                    <label class="block mb-3 text-gray-600" for="">country</label>
                    <input class="border border-gray-500 rounded-md inline-block py-2 px-3 w-full text-gray-600 tracking-widest" type="text" id="fname" name="fname" v-model="userdata.country" />
                </div>
            </div>
        </div>
        <div class="p-5 rounded-md shadow-md bg-white">
            <div class="flex justify-center">
                <div class="w-1/3 px-3">
                <button class="shadow bg-purple-500 hover:bg-purple-400 focus:shadow-outline focus:outline-none text-white font-bold py-2 px-4 rounded" type="button" @click="resetValues()">Reset Form</button>
            </div>
          </div>
      </div>
    </form>    
</div>

</template>

<script>
import _ from "lodash";
import firebase from "../firebaseInit";
const db = firebase.firestore();
const documentPath = "users/user1";
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data: () => {
    return {
      state: "loading", // synced, modified, revoked, error
      userdata: {},
      formComplete: ""
    };
  },
  methods: {
    async updateFirebase() {
      console.log("called");
      try {
        await db.doc(documentPath).set(this.userdata);
        this.state = "synced";
        this.formComplete = `${Object.keys(this.userdata).length * 20} %`;
      } catch (error) {
        console.log(error);
        this.state = "error";
      }
    },
    fieldUpdate() {
      this.state = "modified";
      this.debouncedUpdate();
    },
    debouncedUpdate: _.debounce(function() {
      this.updateFirebase(); // used lodash debounce time for writing values on firestore
    }, 2000),
    resetValues: async function() {
      try {
        await db.doc(documentPath).set({});
        this.userdata = {};
        this.formComplete ="";
      } catch (e) {
        console.log(e);
      }
    }
  },
  async mounted() {
    try {
      await db.doc(documentPath).set({});
    } catch (e) {
      console.log(e);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.notification {
  font-weight: bold;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
