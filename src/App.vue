<template>
  <header class="center-text">
    <hgroup>
      <h1>गोप्य</h1>
      <h3>Generate passwords that are secure AF!</h3>
    </hgroup>
  </header>
  <main class="container">
    <article class="form-wrapper">
      <h2 id="password-display" class="center-text">{{ password() }}</h2>
      <!-- Length Slider -->
      <div class="grid">
        <div>
          <label for="length"
            >Length: {{ length }}
            <input id="length" type="range" v-model="length" min="3" max="50" />
          </label>
        </div>
        <div></div>
        <!-- Extra div for grid -->
      </div>
      <!-- Checkboxes -->
      <fieldset>
        <!-- Lowercase Characters -->
        <label for="lowercase">
          <input
            type="checkbox"
            id="lowercase"
            name="lowercase"
            role="switch"
            v-model="lowercase"
          />
          Lowercase Characters (a-z)
        </label>
        <!-- Uppercase Characters -->
        <label for="uppercase">
          <input
            type="checkbox"
            id="uppercase"
            name="uppercase"
            role="switch"
            v-model="uppercase"
          />
          Uppercase Characters (A-Z)
        </label>
        <!-- Special Characters -->
        <label for="special">
          <input
            type="checkbox"
            id="special"
            name="special"
            role="switch"
            v-model="special"
          />
          Special Characters (!@#$%^&*)
        </label>
        <!-- Digits -->
        <label for="digits">
          <input
            type="checkbox"
            id="digits"
            name="digits"
            role="switch"
            v-model="digits"
          />
          Digits (0-9)
        </label>
      </fieldset>
    </article>
  </main>
  <footer class="center-text">Designed with heart in Nepal</footer>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      special: true, // ASCII 33-42
      lowercase: true, // ASCII 97-122
      uppercase: true, // ASCII 65-90
      digits: true, // ASCII 48-57
      length: 10,
      specialArray: ["!", "@", "#", "$", "%", "^", "&", "*"],
    };
  },
  computed: {},
  methods: {
    rng(left, right) {
      // Generates a random number between left and right, inclusive both ends
      let rand = Math.random(); // Gives 0-1, inclusive 0 exclusive 1
      // Scaling rand to desired range
      rand = rand * (right - left) + left; // Gives a random number between left and right, exclusive right and containing floats
      rand = Math.round(rand);
      return rand;
    },
    password() {
      let pass = "";
      // We will now insert ${this.length} characters to ${this.password}
      for (let i = 0; i < this.length; i++) {
        // We decide what the i'th character should be randomly
        let rand = this.rng(0, 3);
        let range = [];
        // If rand is 0, we insert a special character
        // If rand is 1, we insert a lowercase character
        // If rand is 2, we insert a uppercase character
        // If rand is 3, we insert a digit
        if (rand == 0) {
          if (this.special) range = [33, 42];
          else rand++;
        }
        if (rand == 1) {
          if (this.lowercase) range = [97, 122];
          else rand++;
        }
        if (rand == 2) {
          if (this.uppercase) range = [65, 90];
          else rand++;
        }
        if (rand == 3) {
          if (this.digits) range = [48, 57];
          else rand++;
        }

        let charNum = this.rng(range[0], range[1]);
        let char = String.fromCharCode(charNum);
        pass += char;
        console.log(range, char);
      }
      return pass;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
}
.center-text {
  text-align: center;
}
</style>
