<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-12">
        <div class="card">
            <div class="card-header">
              <h3 class="card-title">Users</h3>
              <div class="card-tools">
                <button class="btn btn-success" data-toggle="modal" data-target="#myModal">Add new
                  <i class="fas fa-user-plus fa-fw"></i>
                </button>
                <!-- <div class="input-group input-group-sm" style="width: 150px;">
                  <input type="text" name="table_search" class="form-control float-right" placeholder="Search">

                  <div class="input-group-append">
                    <button type="submit" class="btn btn-default"><i class="fa fa-search"></i></button>
                  </div>
                </div> -->
              </div>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive p-0">
              <table class="table table-hover">
                <tbody>
                <tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Registered</th>
                  <th>Modify</th>
                </tr>
                <tr v-for="user in users" :key="user.id">
                  <td>{{user.id}}</td>
                  <td>{{user.name | capitalize}}</td>
                  <td>{{user.email}}</td>
                  <td>{{user.type | capitalize}}</td>
                  <td>{{user.created_at | CustomDate}}</td>
                  <td>
                    <a href="#"><i class="fa fa-edit blue"></i></a>
                    /
                    <a href="#" @click="deleteUser(user.id)"><i class="fa fa-trash red"></i></a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
              <!-- /.card-body -->
        </div>
      </div>
    </div>
    <!-- Modal -->
    <div id="myModal" class="modal fade" role="dialog" tabindex="-1" aria-hidden="true">
      <div class="modal-dialog modal-md modal-dialog-centered" role="document">

        <!-- Modal content-->
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title">Add new User</h4>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
          </div>
          <form class="" @submit.prevent="register" method="post">
            <div class="modal-body">
              <div class="form-group">
                <label>Name</label>
                <input v-model="form.name" type="text" name="username"
                  class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                <has-error :form="form" field="name"></has-error>
              </div>
              <div class="form-group">
                <label>Email</label>
                <input v-model="form.email" type="email" name="email"
                  class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                <has-error :form="form" field="email"></has-error>
              </div>
              <div class="form-group">
                <label>BIO</label>
                <textarea v-model="form.bio" name="bio"
                class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }">
              </textarea>
              <has-error :form="form" field="bio"></has-error>
              </div>
              <div class="form-group">
                <label>Type</label>
                <select v-model="form.type" name="type"
                  class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                  <option value="">Select Type</option>
                  <option value="admin">Admin</option>
                  <option value="user">Standard User</option>
                  <option value="author">Author</option>
                </select>
                <has-error :form="form" field="type"></has-error>
              </div>
              <div class="form-group">
                <label>Password</label>
                <input v-model="form.password" type="password" name="password"
                  class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                <has-error :form="form" field="password"></has-error>
              </div>
              <div class="form-group">
                <label>Confirm-Password</label>
                <input v-model="form.password_confirmation" type="password" name="password_confirmation"
                  class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                <has-error :form="form" field="password"></has-error>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
              <button type="submit" class="btn btn-primary">Create</button>
            </div>
        </form>
        </div>
      </div>
    </div>
    <!-- / Modal -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: {},
      form:  new Form({
        name: '',
        email: '',
        password: '',
        password_confirmation: '',
        type: '',
        bio: '',
        photo: '',
      })
    }
  },
  methods: {
    register(){
      this.$Progress.start();
      this.form.post('/api/user')
      .then((response)=>{
        console.log(response);
        this.getUsers();
        this.$Progress.finish();
        $("#myModal").modal('hide');
        toast({
          type: 'success',
          title: response.data.success
        })
      })
      .catch((message)=>{
        console.log(message);
        toast({
          type: 'error',
          title: 'Oops Error Occured!'
        })
      })

      // .then()

    },
    deleteUser(id){
      swal({
        title: 'Are you sure?',
        text: "You won't be able to revert this!",
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!'
      }).then((result) => {
        // Server Request
        if (result.value) {
          this.form.delete('api/user/'+id).then((response) =>{
            console.log(response);
              swal(
                'Deleted!',
                'Your file has been deleted.',
                'success'
              )
              this.getUsers();
          }).catch(()=> {
            swal("Failed!","There was something wrong","warning");
          })
        }
      })
    },
    getUsers(){
      let self=this;
      axios.get('api/user').then((response) => {
        self.users=response.data.data;
      })


    }

  },
  created() {
    this.getUsers();
  }
}
</script>
