<template lang="pug">
  .hero
    .container-fluid
      h1.title User Manager
      table.table.is-narrow.is-bordered.is-hoverable.is-mobile.is-centered
        thead.subtitle
          tr
            th ID
            th First Name
            th Last Name
            th Role
            th Email
            th(v-bind:style="{ 'text-align': 'center' }") Status
            th &nbsp;
            
        tbody
          tr(v-for="user in users" :key="user.id") 
            td {{user.user_id}}
            td {{user.user_fname}}
            td {{user.user_lname}}
            td {{user.user_role}}
            td {{user.user_email}}
            td.status(v-if="user.user_isdel==0")
              span(class="icon is-medium")
                i(class="fas fa-check", aria-hidden="true")
              
            td.status(v-else-if="user.user_isdel==1" v-bind:style="{ 'color': 'red' }")
              span
                i(class="fas fa-times", aria-hidden="true")
            
            td.text-right
              button.button.is-success.is-outlined.tblbtn.is-rounded.tooltip.is-tooltip-success(v-if="user.user_isdel == 1", :pressed="true", @click.prevent="deleteUser(user.user_id,user.user_isdel)", data-tooltip="Enable")
                span
                  i(class="fas fa-eye")

              button.button.is-danger.is-outlined.tblbtn.is-rounded.tooltip.is-tooltip-danger(v-else :pressed="true", @click.prevent="deleteUser(user.user_id,user.user_isdel)", data-tooltip="Disable")
                span
                  i(class="fas fa-eye-slash")
              button.button.is-primary.is-outlined.tblbtn.is-rounded.tooltip.is-tooltip-info(data-tooltip="Edit", :pressed="true", @click.prevent="openEdit") 
                span
                  i(class="fas fa-edit")
      
    EditUserModal(:openeditmodal='editActive' @closeRequest='close')
      router-view
</template>

<style lang="scss">
.tHead{
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
}
.table{
  border-collapse: collapse;
  max-width: 1920px;
  width: 1620px;
}
  .hero {
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
  }
.status{
  color: green;
  text-align: center;
}
.statbtn{
  margin: 3px;
}
.mdl{
  margin: 3px;
}

.btn {
  border: none;
  color: white;
  padding: 12px 16px;
  font-size: 16px;
  cursor: pointer;
}

.tblbtn{
  margin: 5px;
}
</style>

<script>
		import userService from './userService'
		import EditUserModal from '../Modals/EditUserModal'

export default{
  data () {
    return {
      loading: false,
      users: [],
      model: {},
      displayModel: {},
      updateModel: {},
      deleteModel: {},
      search: '',
      modalShow: false,
      showEditModal: false,
      screen: screen.width,
      editActive: false
    }
	},

  async created () {
    this.refreshUsers()
  },

  components: {
    EditUserModal
  },
	
  methods: {
    async refreshUsers () {
      this.loading = true
      this.users = (await userService.getUsers()).data.allUser
      this.loading = false
    },
    async populateUserToEdit (user) {
      this.model = Object.assign({}, user)
    },
    async saveUser () {
      if (this.model.user_id) {
        this.updateModel = {
          user_fname: this.model.user_fname,
          user_lname: this.model.user_lname,
          user_role: this.model.user_role,
          user_email: this.model.user_email
        }
      if(!this.model.user_fname || !this.model.user_lname || !this.model.user_email || !this.model.user_role){
          alert('Please Fill all the text fields')
          this.modal('show');
          
      }else{
        alert('Successfully created')
        await userService.updateUser(this.model.user_id, this.updateModel)
      this.model = {}
      await this.refreshUsers()
      }
      }

    },
    openEdit(){
        this.editActive = 'is-active'
    },
    close(){
      this.editActive = ''
    },
    async deleteUser (id, isdel) {
      if (isdel === 0 && confirm('Are you sure you want to delete this user?')) {
        this.deleteModel = {
          user_isdel: 1
        }
        await userService.updateUser(id, this.deleteModel)
        await this.refreshUsers()
      } else if (isdel === 1 && confirm('Are you sure you want to retrieve this user?')) {
        this.deleteModel = {
          user_isdel: 0
        }
        await userService.updateUser(id, this.deleteModel)
        await this.refreshUsers()
      }
      // if we are editing a user we deleted, remove it from the form
    },
    async getUser (id) {
      await userService.getUser(id)
      await this.refreshUser
    },
    async clear () {
      this.model = {}
    }
  }

		}
</script>
