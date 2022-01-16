<template>
  <!-- Dark mode toggle -->
  <div id="bottom-right">
    <kbd @click="openAboutModal"><i class="fas fa-question"></i></kbd>&nbsp;
    <kbd
      id="dark-mode-toggle"
      @click="toggleDarkMode"
      onselectstart="return false"
      >{{ themeText }}</kbd
    >
  </div>
  <dialog id="about-modal">
    <article>
      <header>
        <a @click="closeAboutModal" aria-label="Close" class="close"></a>
        About गोप्य
      </header>
      <p>
        गोप्य is a password generator that runs on your browser. It's built with
        Vue.js. The passwords generation is done locally on your machine and
        isn't sent to a server. This makes it a secure choice for generating
        passwords.
      </p>
      <p>
        Made by<a
          href="http://aabhusanaryal.com.np"
          target="_blank"
          rel="noopener noreferrer"
        >
          Aabhusan Aryal</a
        >.
      </p>
    </article>
  </dialog>
  <header class="center-text">
    <hgroup>
      <h1>गोप्य</h1>
      <h3>Generate passwords that are secure AF!</h3>
    </hgroup>
  </header>
  <main class="container">
    <article class="form-wrapper">
      <h2 id="password-display" class="center-text">
        {{ password }}
      </h2>
      <!-- Length Slider -->
      <div class="grid">
        <div></div>
        <div>
          <label for="length"
            >Length: {{ length }}
            <input id="length" type="range" v-model="length" min="4" max="40" />
          </label>
        </div>
        <div></div>
        <!-- Extra div for grid -->
      </div>
      <!-- Checkboxes -->
      <div class="grid">
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
            Special Characters (!"#$%&'()*)
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
        <div>
          <button @click="copyToClipboard()" id="copyBtn">Copy Password</button>
          <button @click="generatePassword">Regenerate Password</button>
        </div>
      </div>
    </article>
  </main>
  <footer class="center-text">Designed with ❤️ in Nepal</footer>
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
      password: "", // Used to store pass so that it can be copied to clipboard
      has: {
        special: false,
        lowercase: false,
        uppercase: false,
        digits: false,
      },
      themeText: "",
      isModalOpen: false,
    };
  },

  mounted() {
    this.generatePassword();
    this.setThemeText();
    window
      .matchMedia("(prefers-color-scheme: dark)")
      .addEventListener("change", () => {
        this.setThemeText();
      });
    document.addEventListener("keypress", (e) => {
      if (e.code == "Space" && e.shiftKey) this.toggleDarkMode();
    });
  },
  methods: {
    openAboutModal() {
      this.isModalOpen = true;
      let modal = document.querySelector("#about-modal");
      document.querySelector("html").classList.add("modal-is-opening");
      modal.setAttribute("open", "");
      setTimeout(() => {
        document
          .querySelector("html")
          .classList.replace("modal-is-opening", "modal-is-open");
      }, 400);
      addEventListener("keydown", (event) => {
        if (event.key === "Escape" && this.isModalOpen) {
          this.closeAboutModal();
        }
      });
      addEventListener("click", (e) => {
        let modal = document.querySelector("#about-modal");
        if (this.isModalOpen && modal === e.target) {
          // This works becasue e.target is some children of modal whenever we click inside the
          // main dialog card. If we click outside the dialog card, the target is the modal
          // itself and hence modal===e.target evaluates to true. We want to close the modal
          // when we click outside any of modal's children.
          this.closeAboutModal();
        }
      });
    },
    closeAboutModal() {
      this.isModalOpen = false;
      let modal = document.querySelector("#about-modal");
      document.querySelector("html").classList.remove("modal-is-open");
      document.querySelector("html").classList.add("modal-is-closing");
      setTimeout(() => {
        modal.removeAttribute("open");
        document.querySelector("html").classList.remove("modal-is-closing");
      }, 400);
    },
    setThemeText() {
      let htmlEl = document.querySelector("html");
      if (
        window.matchMedia &&
        window.matchMedia("(prefers-color-scheme: dark)").matches
      ) {
        this.themeText = "Light Mode";
        htmlEl.attributes["data-theme"].nodeValue = "dark";
      } else {
        this.themeText = "Dark Mode";
        htmlEl.attributes["data-theme"].nodeValue = "light";
      }
    },
    toggleDarkMode() {
      let htmlEl = document.querySelector("html");
      if (htmlEl.attributes["data-theme"].nodeValue == "light") {
        htmlEl.setAttribute("data-theme", "dark");
        this.themeText = "Light Mode";
      } else {
        htmlEl.setAttribute("data-theme", "light");
        this.themeText = "Dark Mode";
      }
    },
    copyToClipboard() {
      navigator.clipboard.writeText(this.password);
      // Showing a tooltip message
      let copyBtn = document.querySelector("#copyBtn");
      copyBtn.setAttribute("data-tooltip", "Copied!");
      // Removing the tooltip after 1 second
      setTimeout(() => {
        console.log(1);
        copyBtn.removeAttribute("data-tooltip");
      }, 1000);
    },
    rng(left, right) {
      // Generates a random number between left and right, inclusive both ends
      let rand = Math.random(); // Gives 0-1, inclusive 0 exclusive 1
      // Scaling rand to desired range
      rand = rand * (right - left) + left; // Gives a random number between left and right, exclusive right and containing floats
      rand = Math.round(rand);
      return rand;
    },
    generatePassword() {
      let pass = "";
      if (!(this.special || this.lowercase || this.uppercase || this.digits)) {
        // If all the switches are off
        pass = "Whom are we fooling here?";
        this.tempPass = pass;
        return pass;
      }
      this.has = {
        special: false,
        lowercase: false,
        uppercase: false,
        digits: false,
      };
      // We will now insert ${this.length} characters to ${this.password}
      for (let i = 0; i < this.length; i++) {
        // We decide what the i'th character should be randomly
        let rand = this.rng(0, 3);
        let range = this.generateRange(rand); // Holds the range of ASCII values

        let charNum = this.rng(range[0], range[1]);
        let char = String.fromCharCode(charNum);
        pass += char;
      }
      // Recursively creating a password that MUST satisfy all checked params
      if (
        (this.lowercase && !this.has.lowercase) ||
        (this.uppercase && !this.has.uppercase) ||
        (this.special && !this.has.special) ||
        (this.digits && !this.has.digits)
      ) {
        pass = this.generatePassword();
      }
      this.password = pass;
      return pass;
    },
    generateRange(rand) {
      // If rand is 0, we insert a special character
      // If rand is 1, we insert a lowercase character
      // If rand is 2, we insert a uppercase character
      // If rand is 3, we insert a digit
      let range = [];
      if (rand == 0) {
        if (this.special) {
          range = [33, 42];
          this.has.special = true;
        } else rand++;
      }
      if (rand == 1) {
        if (this.lowercase) {
          range = [97, 122];
          this.has.lowercase = true;
        } else rand++;
      }
      if (rand == 2) {
        if (this.uppercase) {
          range = [65, 90];
          this.has.uppercase = true;
        } else rand++;
      }
      if (rand == 3) {
        if (this.digits) {
          range = [48, 57];
          this.has.digits = true;
        } else range = this.generateRange(0); // If this.digits is off, recursively finding a new range
      }
      return range;
    },
  },
  computed: {
    allProperties() {
      return `${this.lowercase}|${this.uppercase}|${this.special}|${this.digits}|${this.length}`;
    },
  },
  watch: {
    allProperties() {
      this.generatePassword();
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
#bottom-right {
  position: absolute;
  bottom: 20px;
  right: 20px;
}
#dark-mode-toggle {
  width: 120px;
  text-align: center;
}
</style>
