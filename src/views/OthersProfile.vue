
<template>
    <main>
    <!-- Profil -->
    <section v-if="user.createdAt">
      <h1>Bonjour {{user.username}} !</h1>
      <div class="profile__pic">
        <div class="profile__img">
          <img v-if="user.imageUrl" :src="user.imageUrl"   alt="image postée" />
        </div>
      </div>
      <div class="profile__date">
        <h2>Ancienneté</h2>
        <p>Membre depuis le {{ user.createdAt.slice(0,10) }}</p>
      </div>
      <div class="profile__date">
        <h2>Adresse Email</h2>
        <p>{{ user.email }}</p>
      </div>
      <div class="profile__bio">
        <h2>Bio</h2>
        <p ref="input_bio">{{ user.bio }}</p>
      </div>
      <!-- Modifier le profil  -->
      <section v-show="profileId == userId">
        <form @submit.prevent="updateProfile(event)" >
          <h2>Modifier vos informations</h2>
          <div v-show="invalid!=null" class="invalid_txt" key="invalid">
            <p>{{invalid}}</p>
          </div>
          <div>
            <input
              v-model="inputUsername"
              type="text"
              aria-describedby="usernameHelp"
              placeholder="Nouveau pseudo"
              />
          </div>
          <div>
            <input
              v-model="inputEmail"
              type="email"
              aria-describedby="emailHelp"
              placeholder="Nouveau email"            
            />
          </div>
          <div>
            <input
              v-model="inputPassword"
              type="password"
              id="inputPassword"
              aria-describedby="passwordHelp"
              placeholder="Nouveau mot de passe"
              autocomplete="new-password"
            />
          </div>
          <div>
            <textarea class="box__message" v-model="inputBio" type="text" placeholder="Nouvelle bio" />
          </div>
          <button type="submit">Remplacer</button>
        </form>
      </section>
      <section v-show="isAdmin || profileId == userId">
        <!-- Supprimer son compte -->
        <div class="delete__section">
            <p class="italic">Depuis cette page vous pouvez supprimer votre compte. La suppression de votre compte entrainera également la suppression de tous les commentaires et les images que vous avez postés.</p>
            <button class="delete__account" @click="deleteMyAccount(profileId)">SUPPRIMER VOTRE COMPTE</button>
        </div>
      </section>
    </section>
  </main>
</template>

<script>
import axios from "axios";
import router from "@/router";
export default {
    name: "Profile",
    data() {
        return {
            inputUsername: "",
            inputEmail: "",
            inputPassword: "",
            inputBio:"",
            invalid: "",
            user:[],
            profileId:JSON.parse(localStorage.getItem('profileId')),
            isAdmin:JSON.parse(localStorage.getItem('isAdmin')),
            userId:JSON.parse(localStorage.getItem('userId')),
        }
    },
    created: function() { 
      var vm = this;
        let id = localStorage.getItem('profileId');
         axios.get("http://localhost:3000/api/user/" + + id, { headers: {"Authorization": "Bearer " + localStorage.getItem("token")} })
        .then(res => {  
            vm.user = res.data;
            console.log(this.user) 
        })
        .catch((error)=> { console.log(error) 
        });    
    },
    methods: {
        localClear() {
            localStorage.clear();
            router.push({ path : "/" });
        },
        deleteMyAccount(id) {
            let confirmUserDeletion = confirm("voulez-vous vraiment supprimer votre compte ?");
            if (confirmUserDeletion == true) {
                axios.delete("http://localhost:3000/api/user/" + id, {headers: { "Authorization": "Bearer " + localStorage.getItem("token") },})
                .then((res)=> {
                    console.log(res);
                    router.push({ path : "/feed" });
                })
                .catch((error) => { 
                    console.log(error)
                })
            } else {
                return 
            }
        },
    }
}

</script>

<style scoped>
@media screen and (min-width: 850px) {
  main{
    display: flex;
    justify-content: center;
    width:50em;
  }
  form{
    width:20em;
  }
  .delete__account{
    max-width: 20em;
  }
  .profile__date{
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  
}
input, textarea{
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
  outline: none;
  border-radius: 2em;
  border:none;
  padding:0.5em;
  margin-bottom:1em;
  width: 100%;
  border: 0.05em solid rgba(0, 0, 0, 0.267);
  color:#2c3e50;
}
.profile__pic{
  display: flex;
  justify-content: center;
}
input:focus{
  box-shadow: 0 0 0.2em 0.1em #fd2b017a;
}
button{
  border-radius: 2em;
  border:none;
  background-color:#fd2d01;
  color:white;
  font-weight: 600;
  width: 100%;
  margin-bottom: 1em;
  padding: 1em 0em 1em 0em;
}
.img-upload>input {
  display:none;
}
.img-upload{
  position:absolute;
  width:10em;
}
.profile__pic{
  position:relative;
}
.fa-edit{
  position:absolute;
  font-size:2em;
  right:0em;
  z-index: 2;
  color:#fa7a60;
}
.delete__account{
  font-size:0.8em;
  margin-top: 1em;
  color:#ffffff;
  border:none;
  background-color:#fd2b01ab;
  border-radius:2em;
  padding:0.8em;
  margin-bottom:2em;
}
.profile__img{
  margin:0 auto;
  width:10em;
  height:10em;
  border-radius: 50%;
  overflow: hidden;
}
.profile__img img{
  height:100%;
}
h1{
  font-size: 1.6em;
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
  align-self: center;
  text-align: center;
  margin-bottom: 1em;
}
.profile__date{
  margin-top:1em;
  margin-bottom:1em;
}
.profile__bio{

  margin-bottom:1em;
}
h2{
  margin-bottom:0.5em;
  font-size: 1.2em;
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
}
main{
  margin:auto;
  padding:0em 2em 0em 2em;
}
.italic{
  margin-top:3em;
  font-style: italic;
  font-size:0.8em;
}
a,button{
  cursor: pointer;
}
#signout {
  width: 2em;
}
</style>