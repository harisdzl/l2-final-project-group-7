<template>
    <div id="screenfiller">
        <div class="login-container">
            <h2 id="login-statement">Create a WorkWise account</h2>
            <form>
            <input class="form-control" type="text" v-model="name" required placeholder="Name">
            <br><br>
            <input class="form-control" type="email" v-model="email" required placeholder="Email">
            <br><br>
            <input class="form-control" type="password" v-model="password" required placeholder="Password">
            <br><br>
            <div v-show = "error" class="error">
                {{ this.errorMsg }}
            </div>
            <button class="btn btn-primary" type="submit" @click.prevent="signUp">Sign Up</button>
            </form>
            <div class="text-divider">
                or
            </div>
            <button class="google-btn" type="submit" @click.prevent="signUpWithGoogle">Sign up with Google</button>
            <div class="login-bottom">
                Have an account?
                <router-link to="/login">Log in here</router-link> 
            </div>
        </div>        
    </div>

  </template>

<script>
import app from "../firebase/init.js"
import { getAuth, createUserWithEmailAndPassword, GoogleAuthProvider, signInWithPopup} from "firebase/auth";
import { collection, getDocs, getFirestore, doc, addDoc } from "firebase/firestore"
import { auth, db } from "../firebase/init.js"
import { getDoc, setDoc } from "firebase/firestore"
import { useRouter } from "vue-router";


export default {
  data() {
    return {
        name: '', 
        email: '',
        password: '',
        points : 0,
        projects : [],
        error: false,
        errorMsg: ''
    };
  },
  methods: {
    async signUp() {
        try {
            const createUser = await createUserWithEmailAndPassword(auth, this.email, this.password);
            console.log("user created")
            const collectionRef = collection(db, "users");
            const userDoc = {
                uid : createUser.user.uid,
                name : this.name,
                email : this.email,
                points : this.points,
                projects : this.projects
            }
            await addDoc(collectionRef, userDoc);
            const user = createUser;

            console.log("doc created")
            this.$router.push('/login')
            return;
        } catch(err) {
            this.errorMsg = err.message
            this.error = true;
        }
    },
    async signUpWithGoogle() {
        const provider = new GoogleAuthProvider(); 
        signInWithPopup(auth, provider)
        .then((result) => {
            const credential = GoogleAuthProvider.credentialFromResult(result);
            const token = credential.accessToken;
            const user = result.user;
            this.$router.push('/signupdetails')
        }).catch((err) => {
            this.errorMsg = err.message;
            this.error = true; 
        });
    }
},
}

</script>


<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Yeseva+One&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans&display=swap');

html, body {
    margin: 0;
    padding: 0;
}
#screenfiller {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;
    background-color: #130D6F;
    background-image: url("@/assets/ellipsis.png");
    background-size: 100% 100%;
    position: absolute;
    top: 0; right: 0; bottom: 0; left: 0;
    
}
.login-container {
    display: block;
    width: 345px;
    height: 580px;
    text-align:center;
    background: #FFFFFF;
    mix-blend-mode: normal;
    border-radius: 12px;
}
#login-statement {
    font-family: 'Yeseva One', cursive;
    color: #FF9190;
    font-style: normal;
    font-weight: 400;
    font-size: 42px;
    line-height: 49px;
    text-align: center;
}
.form-control {
    width: 301px;
    height: 43px;
    border: 1px solid #9D9D9D;
    border-radius: 50px;
    line-height: normal;
}

input {
    text-indent: 10px;
}

::placeholder {
    font-family: 'Josefin Sans', sans-serif;
}

.btn {
    font-family: 'Josefin Sans', sans-serif;
    font-size: large;
    color: white;
    width: 136px;
    height: 43px;
    left: 652px;
    top: 640px;
    background: #5E72EB;
    border: #130D6F;
    border-radius: 49px;
}

.btn:hover {
    cursor: pointer;
}

.login-bottom {
    font-family: 'Josefin Sans', sans-serif;
    font-size: small;
    color: #FF9190;
    padding-top: 0.5rem;
}

.text-divider {
  display: flex;
  align-items: center;
  color: #9D9D9D;
  padding-right: 1.5rem;
  padding-left: 1.5rem;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;

}

.text-divider::before,
.text-divider::after {
  content: '';
  height: 1px;
  background-color: #9D9D9D;
  flex-grow: 1;
}

.google-btn {
    font-family: 'Josefin Sans', sans-serif;
    font-size: large;
    color: white;
    width: 300px;
    height: 43px;
    left: 652px;
    top: 640px;
    background: #eb5e5e;
    border: #130D6F;
    border-radius: 49px; 
    cursor: pointer;
}
</style>