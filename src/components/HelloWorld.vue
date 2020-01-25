<template>
  <div class="container">
      <div class="row justify-content-center">
         <div class="col-8">
            <div class="form-group">
               <button class="btn-block btn btn-primary mx-auto" @click="addUser" v-if="!showForm">Add User</button>
            </div>
            <form @submit.prevent="submitForm" v-if="showForm">

               <div class="input-group" v-for="field in fieldsData">

                  <div v-if="field.type === 'info_html'" v-html="field.content"></div>


                  <!-- Name Field -->
                  <div v-if="field.type === 'text'">
                     <div class="form-group name">
                        <label :for="field.name">{{field.label}}</label>
                        <input v-model="newUser.name" class="form-control" :type="field.type" :id="field.name" :placeholder="field.placeholder" :require="field.required" :class="{ 'is-invalid':  $v.newUser.name.$error }">

                        <div v-if=" !$v.newUser.name.required" class="invalid-msg">{{ field.validation_message }}</div>
                     </div>
                  </div>


                  <!-- Email Field -->
                  <div v-if="field.type === 'email'">
                     <div class="form-group email">
                        <label :for="field.name">{{field.label}}</label>
                        <input v-model="newUser.email" class="form-control" :type="field.type" :id="field.name" :placeholder="field.placeholder" :require="field.required" :class="{ 'is-invalid':  $v.newUser.email.$error }">
                        <div v-if=" !$v.newUser.email.required" class="invalid-msg">{{ field.validation_message }}</div>
                     </div>
                  </div>


                  <!-- Ocupation Field -->
                  <div v-if="field.type === 'multi-select'">
                     <div class="form-group ocupation">
                        <label :for="field.name">{{field.label}}</label>
                        <select v-model="newUser.ocupation" class="form-control" name="field.name" :id="field.name":class="{ 'is-invalid':  $v.newUser.ocupation.$error }">
                           <option v-for="option in getOcuOptions" :value="option">{{option}}</option>
                        </select>
                        <div v-if=" !$v.newUser.ocupation.required" class="invalid-msg">{{ field.validation_message }}</div>
                     </div>
                  </div>


                  <!-- Status Field -->
                  <div v-if="field.type === 'radio'">
                     <div class="form-group status">
                        <label :for="field.name">{{field.label}}</label> <br>

                        <div class="form-check-inline" v-for="option in getStatusOptions" :class="{ 'is-invalid':  $v.newUser.status.$error }">
                           <label class="form-check-label" :for="option"> {{option}}
                              <input v-model="newUser.status" :value="option"  class="form-check-input" :type="field.type" :name="field.name" :id="option" >
                           </label>
                        </div>
                        <div v-if=" !$v.newUser.status.required" class="invalid-msg">{{ field.validation_message }}</div>
                     </div>
                  </div>


                  <!-- Internal Server Field -->
                  <div v-if="field.type === 'select'">
                     <div class="form-group internal-server">
                        <label :for="field.name">{{field.label}}</label>
                        <select v-model="newUser.internal_status" class="form-control"  name="field.name" :id="field.name" :class="{ 'is-invalid':  $v.newUser.internal_status.$error }">
                           <option v-for="option in getInternalOptions" :value="option">{{option}}</option>
                        </select>
                        <div v-if=" !$v.newUser.internal_status.required" class="invalid-msg">{{ field.validation_message }}</div>
                     </div>
                  </div>
               </div>

                  <!-- Submit Button -->
               <div class="form-group">
                  <button class="btn-block btn btn-success mx-auto" type="submit">Submit</button>
               </div>
            </form>
            <p class="form-success text-center" v-show="successMsg">Form Submited Successfully</p>

            <table class="table table-bordered" v-if="!showForm">
               <thead>
                  <tr>
                     <th v-for="header in getTableHeader">{{header.label}}</th>
                  </tr>
               </thead>
               <tbody>
                  <tr v-for="data in getData">
                     <td>{{ data.name }}</td>
                     <td>{{ data.email }}</td>
                     <td>{{ data.ocupation }}</td>
                     <td>{{ data.status }}</td>
                     <td>{{ data.internal_status }}</td>
                  </tr>
               </tbody>
            </table>
         </div>
      </div>
   </div>
</template>

<script>
import { required } from 'vuelidate/lib/validators';
export default {
  name: 'HelloWorld',
   data () {
      return {
         fieldsData: [],
         submited: false,
         successMsg: false,
         newUser: {},
         getData: [],
         showForm: false
      }
   },
   validations: {
      newUser: {
         name: { required },
         email: { required },
         ocupation: { required },
         status: { required },
         internal_status: { required }
      }
  },
   computed: {
      getOcuOptions(){
         var tempOpt = this.fieldsData.filter( field => field.type ==='multi-select')
         var option = tempOpt[0].options[0]
         return option;
      },
      getStatusOptions(){
         var tempOpt = this.fieldsData.filter( field => field.type ==='radio')
         var option = tempOpt[0].options[0]
         return option;
      },
      getInternalOptions(){
         var tempOpt = this.fieldsData.filter( field => field.type ==='select')
         var option = tempOpt[0].options[0]
         return option;
      },
      getTableHeader(){
         var tempHeader = this.fieldsData.filter( field => field.type !=='info_html')
         var header = tempHeader
         return header;
      },
      usernameError(){
         var errors = [];
         if(!this.$v.name.$dirty) return errors;
         !this.$v.name.required && errors.push('suer name required')
      }
   },
   methods:{
      addUser(){
         this.showForm = true
      },
      submitForm(){
         this.$v.$touch();
         if (this.$v.$invalid) {
            return;
         }
         var mydata = {
            name: this.newUser.name,
            email: this.newUser.email,
            ocupation: this.newUser.ocupation,
            status: this.newUser.status,
            internal_status: this.newUser.internal_status
          }
         this.getData.push(mydata)
         this.successMsg = true

         setTimeout(() => {
            this.showForm = false;
            this.successMsg = false
            this.newUser = {}
         }, 2000);
      }
   },
   mounted(){
      this.$http.get('http://localhost:3000/fields')
      .then((response)=>{
         this.fieldsData = response.body
      })
      .catch()
  }
}
</script>


<style scoped>
form{
   border: 1px solid #eee;
   padding: 20px;
   box-shadow: 0 0 5px rgba(0,0,0,.2);
}
button{
  width: 400px;
}
.invalid-msg{
   color: red;
   font-size: 12px;
   display: none;
}
.is-invalid ~ .invalid-msg{
   display: block;
}
</style>
