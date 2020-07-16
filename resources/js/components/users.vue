
<template>
    <div class="container">
       <div class="col-xs-12">
          <div class="box">
            <div class="box-header">
              <h3 class="box-title">Listes users :</h3>

              <div class="box-tools">

              <button class="btn btn-success" @click="newModal">Add user <i class="fas fa-user-plus fa-fw"></i></button>
              </div>
            </div>
            <!-- /.box-header -->
            <div class="box-body table-responsive no-padding">
              <table class="table table-hover">
                <tbody><tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                   <th>Etat</th>
                  <th>Action</th>
                </tr>
                <tr v-for="user in users" :key="user.id" >
                  <td>{{user.id}}</td>
                  <td>{{user.name|UpText}}</td>
                  <td>{{user.email}}</td>

                  <td><span class="label label-success">Approved</span></td>
                  <td><span class="label label-success">Approved</span></td>
                  <td>
                      <a href="#" @click="editModal(user)">
                       <i class="fa fa-edit" style="color:#ff9933"> </i>
                      </a>
                      /
                        <a href="#" @click="DeleteUser(user.id)">
                       <i class="fa fa-trash" style="color:Red"> </i>
                      </a>
                  </td>
                </tr>

              </tbody></table>
            </div>
            <!-- /.box-body -->
          </div>
          <!-- /.box -->
        </div>

        <div class="modal fade" id="Addnew" tabindex="-1" role="dialog" aria-labelledby="AddnewLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" v-show="!editmode" id="AddnewLabel">Create user</h5>
         <h5 class="modal-title" v-show="editmode" id="AddnewLabel">Update user</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <form @submit.prevent="editmode ? UpdateUser():CreateUser()">
      <div class="modal-body">
                  <div class="form-group">
                        <label>Username</label>
                          <input v-model="form.name" type="text" name="name" placeholder="enter name"
                             class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                         <has-error :form="form" field="name"></has-error>
                  </div>

                    <div class="form-group" v-show="!editmode">
                        <label>Email</label>
                          <input v-model="form.email" type="text" name="email" placeholder="enter email"
                             class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                         <has-error :form="form" field="email" ></has-error>
                  </div>



                       <div class="form-group">
                        <label>password</label>
                          <input v-model="form.password" type="password" name="password" placeholder="enter password"
                             class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                         <has-error :form="form" field="password"></has-error>
                  </div>
      </div>

      <div class="modal-footer">
        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
        <button v-show="!editmode" type="submit" class="btn btn-primary">Create</button>
        <button v-show="editmode" type="submit" class="btn btn-primary">update</button>
      </div>
       </form>
    </div>
  </div>
</div>
    </div>
</template>


<script>
    export default {

               data(){

                   return{
                       editmode: true,
                       users:{},
                    form: new Form({
                        id:'',
                        name:'',
                        email:'',
                        password:'',
                        photo:''
                    })

                   }
               },

          methods:{
                  UpdateUser(){
                      this.$Progress.start();

                      this.form.put('api/user/'+this.form.id)

                      .then(()=>{

                         $('#Addnew').modal('hide')
                           toast.fire({
                            icon: 'success',
                          title: 'user updated successfully'
                       })
                          this.$Progress.finish();
                            Fire.$emit('AfterCreate');

                      })
                      .catch(()=>{

                          this.$Progress.fail();
                      Swal.fire("Failed!","something wrong","warning");
                      })

                  },

                     editModal(user){
                         this.editmode=true;
                       this.form.reset();
                      $('#Addnew').modal('show');
                      this.form.fill(user);

                       },
                   newModal(){
                        this.editmode=false;
                       this.form.reset();
                      $('#Addnew').modal('show');

                       },
                  DeleteUser(id){
                     Swal.fire({
                     title: 'Are you sure?',
                     text: "You won't be able to revert this!",
                     icon: 'warning',
                     showCancelButton: true,
                     confirmButtonColor: '#3085d6',
                     cancelButtonColor: '#d33',
                     confirmButtonText: 'Yes, delete it!'
                      }).then((result) => {
                               if (result.value) {
                          this.form.delete('api/user/'+id).then(()=>{

                      Swal.fire(
                         'Deleted!',
                         'Your user has been deleted.',
                         'success'
                             )

                            Fire.$emit('AfterCreate');
                          }).catch(()=>{

                            Swal.fire("Failed!","something wrong","warning");

                               });
                               }
                             });

                  },


              loadUsers(){
              axios.get("api/user").then(({data})=>(this.users=data.data));


              },
             CreateUser(){


             this.$Progress.start();
             this.form.post('api/user')
               .then(()=>{

                    Fire.$emit('AfterCreate');
                   $('#Addnew').modal('hide')
                      toast.fire({
                      icon: 'success',
                     title: 'user created successfully'
                       })
                     this.$Progress.finish();

                    })
                    .catch(()=>{



                    })


             }

          },
             created() {
            this.loadUsers();
            Fire.$on('AfterCreate',()=>{this.loadUsers();})

            }
    }
</script>
