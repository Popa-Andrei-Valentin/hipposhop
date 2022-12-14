<template>
  <div class="register-container">
    <div class="registerComponent">
      <div class="form">
        <button class="closeModal" @click="closeRegister()">X</button>
        <div class="title">Hello !</div>
        <div class="subtitle">Create your account here...</div>

        <div class="input-container ic2">
          <input
            id="email"
            :class="['input', checkEmail(email)]"
            type="email"
            placeholder=" "
            v-model="email"
          />
          <div class="cut cut-short"></div>
          <label for="email" class="placeholder">E-mail</label>
        </div>
        <div class="input-container ic2">
          <input
            id="password"
            :class="['input', checkPassword(password)]"
            type="password"
            placeholder=" "
            v-model="password"
          />
          <div class="cut"></div>
          <label for="password" class="placeholder">Password</label>
        </div>
        <div class="input-container ic3">
          <input
            id="passwordConfirm"
            :class="['input', checkPasswordMatch(passwordCheck)]"
            type="password"
            placeholder=" "
            v-model="passwordCheck"
          />
          <div class="cut cut-long2"></div>
          <label for="password" class="placeholder">Confirm password</label>
        </div>
        <div class="input-container">
          <p
            :style="[
              password.length > 0 && checkPassword(password) === 'valid'
                ? 'color:green'
                : '',
              password.length > 0 && checkPassword(password) === 'invalid'
                ? 'color:red'
                : '',
            ]"
          >
            <span>{{
              password.length > 0 && checkPassword(password) === "valid"
                ? "&#9745;"
                : "&#9744;"
            }}</span>
            Password must be at least eight characters, one letter capital
            letter and a number.
          </p>
          <p
            :style="[
              passwordCheck.length > 0 &&
              checkPasswordMatch(passwordCheck) === 'valid'
                ? 'color:green'
                : '',
              passwordCheck.length > 0 &&
              checkPasswordMatch(passwordCheck) === 'invalid'
                ? 'color:red'
                : '',
            ]"
          >
            <span>{{
              passwordCheck.length > 0 &&
              checkPasswordMatch(password) === "valid"
                ? "&#9745;"
                : "&#9744;"
            }}</span>
            The passwords match.
          </p>
        </div>
        <p class="errorMsg" v-if="this.errorMessage !== ''">
          Error: {{ errorMessage }}
        </p>
        <button
          type="text"
          :class="[
            checkEmail(email) === 'valid' &&
            checkPassword(password) === 'valid' &&
            checkPasswordMatch(passwordCheck) === 'valid'
              ? 'submit'
              : 'submitOff',
          ]"
          @click="submitFirebaseRegister()"
        >
          Sign Up
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import validatorEmail from "@/Libraries/validatorEmail";
import validatorPassword from "@/Libraries/validatorPassword";
import { mapActions, mapGetters } from "vuex";
import { createUserWithEmailAndPassword } from "@firebase/auth";
import { firebaseInit, firebaseAuthInit } from "/src/main";

export default {
  name: "RegisterModalComp",
  emits: {
    // null -> No validation needed
    closeRegister: null,
  },
  data() {
    return {
      userName: "",
      email: "",
      password: "",
      passwordCheck: "",
      validEmail: false,
      validPass: false,
      matchPass: false,
      register: false,
      errorMessage: "",
    };
  },
  computed: {
    ...mapGetters({
      getUser: "user/getUser",
      getNewUser: "user/getNewUser",
    }),
  },
  methods: {
    ...mapActions({
      loadUser: "user/loadUser",
      loadAdmin: "user/loadAdmin",
      saveUserLocal: "user/saveUserLocal",
      deleteUserLocal: "user/deleteUserLocal",
      loadNewUser: "user/loadNewUser",
      resetNewUser: "user/resetNewUser",
      loadRegisterMessage: "message/loadRegisterMessage",
    }),
    closeRegister() {
      this.$emit("closeRegister");
    },
    /**
     * IMPORTANT: Register function for Firebase.
     */
    async submitFirebaseRegister() {
      let credentials = await createUserWithEmailAndPassword(
        firebaseAuthInit(firebaseInit),
        this.email,
        this.password
      ).catch((error) => {
        this.errorMessage = error;
        setTimeout(() => (this.loginError = ""), 3500);
        console.warn(error);
      });
      if (credentials) {
        this.closeRegister();
        this.loadRegisterMessage();
      }
    },
    /**
     * Checks email requirements (@, .com/.co etc.)
     * @param arg {String}
     * @returns {String|*}
     */
    checkEmail(arg) {
      this.validEmail = validatorEmail(arg) === "valid";
      return validatorEmail(arg);
    },
    checkPassword(arg) {
      this.validPass = validatorPassword(arg) === "valid";
      return validatorPassword(arg);
    },
    checkPasswordMatch(arg) {
      arg.length > 0 && arg === this.password
        ? (this.matchPass = true)
        : (this.matchPass = false);
      return this.matchPass ? "valid" : "invalid";
    },
  },
};
</script>

<style scoped>
@import "../assets/css/fontKanit.css";
@import "../assets/css/fontRoboto.css";
@import "../assets/css/fontPoppins.css";

.closeModal {
  position: absolute;
  background-color: #eee;
  border: none;
  cursor: pointer;
  align-self: flex-end;
  right: 5%;
}

.closeModal:hover {
  background-color: #939393;
}

.closeModal:active {
  background-color: #484848;
}

.register-container {
  top: 0;
  display: flex;
  position: absolute;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  z-index: 1000;
  background-color: rgba(255, 255, 255, 0.3);
  -webkit-backdrop-filter: blur(5px);
  backdrop-filter: blur(5px);
}

.form {
  background-color: #15172b;
  border-radius: 20px;
  box-sizing: border-box;
  height: 560px;
  padding: 20px;
  width: 320px;
  position: relative;
}

.title {
  color: #eee;
  font-family: sans-serif;
  font-size: 36px;
  font-weight: 600;
  margin-top: 30px;
}

.subtitle {
  color: #eee;
  font-family: sans-serif;
  font-size: 16px;
  font-weight: 600;
  margin-top: 10px;
}

.input-container {
  height: 50px;
  position: relative;
  width: 100%;
}

.input-container p {
  font-size: 13px;
  color: #eee;
  font-family: sans-serif;
  padding-left: 0.2rem;
  width: 100%;
}

.validReq {
  color: green;
}

invalidReq {
  color: red;
}

.ic1 {
  margin-top: 40px;
}

.ic2 {
  margin-top: 30px;
}

.ic3 {
  margin-top: 30px;
}
.input {
  background-color: #303245;
  border-radius: 12px;
  border: 0;
  box-sizing: border-box;
  color: #eee;
  font-size: 18px;
  height: 100%;
  outline: 0;
  padding: 4px 20px 0;
  width: 100%;
}

.cut {
  background-color: #15172b;
  border-radius: 10px;
  height: 20px;
  left: 20px;
  position: absolute;
  top: -20px;
  transform: translateY(0);
  transition: transform 200ms;
  width: 75px;
}

.cut-short {
  width: 60px;
}

.cut-long {
  width: 80px;
}

.cut-long2 {
  width: 120px;
}

.input:focus ~ .cut,
.input:not(:placeholder-shown) ~ .cut {
  transform: translateY(8px);
}

.placeholder {
  color: #65657b;
  font-family: sans-serif;
  left: 20px;
  line-height: 14px;
  pointer-events: none;
  position: absolute;
  transform-origin: 0 50%;
  transition: transform 200ms, color 200ms;
  top: 20px;
}

.input:focus ~ .placeholder,
.input:not(:placeholder-shown) ~ .placeholder {
  transform: translateY(-30px) translateX(10px) scale(0.75);
}

.input:not(:placeholder-shown) ~ .placeholder {
  color: #808097;
}

.input:focus ~ .placeholder {
  color: #d6d6de;
}

.submit {
  background-color: #08d;
  border-radius: 12px;
  border: 0;
  box-sizing: border-box;
  color: #eee;
  cursor: pointer;
  font-size: 18px;
  height: 50px;
  margin-top: 38px;
  outline: 0;
  text-align: center;
  width: 100%;
}

.submit:active {
  background-color: #06b;
}

.submitOff {
  background-color: #4f4f4f;
  border-radius: 12px;
  border: 0;
  box-sizing: border-box;
  color: #eee;
  cursor: not-allowed;
  font-size: 18px;
  height: 50px;
  margin-top: 38px;
  outline: 0;
  text-align: center;
  width: 100%;
}

.login-container {
  top: 0;
  display: flex;
  position: absolute;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  z-index: 1000;
  background-color: rgba(255, 255, 255, 0.3);
  -webkit-backdrop-filter: blur(5px);
  backdrop-filter: blur(5px);
}

.valid {
  background-color: rgba(39, 229, 23, 0.15);
}

.invalid {
  background-color: rgba(255, 0, 0, 0.15);
}

.errorMsg {
  color: red;
  text-align: center;
  right: 28%;
  position: absolute;
}
</style>
