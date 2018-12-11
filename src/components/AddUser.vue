<template>
<form class="is-expanded is-vcentered is-desktop" @submit.prevent = "saveUser" >
<div class="field">
  <label class="label">First Name</label>
  <div class="control">
    <input class="input" type="text" placeholder="First Name" v-model = "model.user_fname">
  </div>
</div>
<div class="field">
  <label class="label">Last Name</label>
  <div class="control">
    <input class="input" type="text" placeholder="Last Name" v-model = "model.user_lname" >
  </div>
</div>
<div class="field">
  <label class="label">Role</label>
  <div class="control">
    <input class="input" type="text" placeholder="Role" v-model = "model.user_role">
  </div>
</div>
<div class="field">
  <label class="label">Email</label>
  <div class="control">
    <input class="input" type="text" placeholder="Email" v-model = "model.user_email">
  </div>
</div>


<div class="field is-grouped is-centered" >
  <div class="control">
    <button class="button is-success">
    <span>
        <i class="fa fa-check"></i>
        Save User
    </span>
    </button>
  </div>
  <div class="control">

    <button class="button is-info" @click.prevent="clear()">
        <span>
        <i class="fas fa-undo"></i> Clear
        </span>
    </button>

  </div>
</div>
</form>

</template>


<style lang="scss">

</style>

<script>
import userService from './userService'

export default {
data(){
    return{
        model: {}
    }
},

methods: {
    async saveUser () {
        if(!this.model.user_fname || !this.model.user_lname || !this.model.user_email || !this.model.user_role){
            alert('Please Fill all the text fields')
        }else{
        alert(this.model.user_fname + ' ' + this.model.user_lname + ' is Successfuly added')
        await userService.createUser(this.model)
                this.model = {}
        await this.refreshUsers()
        }
        }
,
    async clear () {
      this.model = {}
    }
}
}
</script>