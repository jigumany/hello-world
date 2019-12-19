<template>
  <div class="container ">
    <div
      class="row mt-5"
      v-if="$gate.isAdminOrAuthor()"
    >
      <div class="col-md-12">
        <div class="card">
          <div class="card-header">
            <h3 class="card-title">Users Table</h3>

            <div class="card-tools">
              <button
                class="btn btn-success"
                @click="newModal"
              >Add New <i class="fas fa-user-plus fa-fw"></i></button>
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
                  <th>Registred At</th>
                  <th>Modify</th>
                </tr>

                <tr
                  v-for="user in users.data"
                  :key="user.id"
                >
                  <td>{{user.id}}</td>
                  <td>{{user.name}}</td>
                  <td>{{user.email}}</td>
                  <td>{{user.type | upText}}</td>
                  <td>{{user.created_at | myDate}}</td>
                  <td>
                    <a
                      href="#"
                      @click="editModal(user)"
                    >
                      <i class="fa fa-edit green"></i>
                    </a>
                    /
                    <a
                      href="#"
                      @click="deleteUser(user.id)"
                    >
                      <i class="fa fa-trash red"></i>
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <!-- /.card-body -->
          <div>
            <pagination
              :data="users"
              @pagination-change-page="getResults"
            ></pagination>
          </div>
        </div>
        <!-- /.card -->
      </div>
    </div>

    <div v-if="!$gate.isAdminOrAuthor()">
      <not-found></not-found>
    </div>
    <!--Modal -->
    <div
      class="modal fade"
      id="addNew"
      tabindex="-1"
      role="dialog"
      aria-labelledby="addNewLabel"
      aria-hidden="true"
    >
      <div
        class="modal-dialog modal-dialog-centered"
        role="document"
      >
        <div class="modal-content">
          <div class="modal-header">
            <h5
              class="modal-title "
              v-show="!editmode"
              id="addNewLabel"
            >Add New User</h5>
            <h5
              class="modal-title "
              v-show="editmode"
              id="addNewLabel"
            >Update User's Info</h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <form @submit.prevent=" editmode ? updateUser() : createUser() ">
            <div class="modal-body">
              <div class="form-group row">
                <label
                  for="inputName"
                  class="col-sm-2 col-form-label"
                >Name</label>
                <div class="col-sm-10">
                  <input
                    type="text"
                    class="form-control"
                    id="inputName"
                    placeholder="Name"
                    v-model="form.name"
                    required
                  >
                  <span
                    class="invalid-feedback d-block"
                    role="alert"
                    v-if="form.errors.has('name')"
                    v-text="form.errors.get('name')"
                  ></span>
                </div>
              </div>
              <div class="form-group row">
                <label
                  for="inputEmail"
                  class="col-sm-2 col-form-label"
                >Email</label>
                <div class="col-sm-10">
                  <input
                    type="text"
                    class="form-control"
                    id="inputEmail"
                    placeholder="Email Address"
                    v-model="form.email"
                    required
                  >
                  <span
                    class="invalid-feedback d-block"
                    role="alert"
                    v-if="form.errors.has('email')"
                    v-text="form.errors.get('email')"
                  ></span>
                </div>
              </div>

              <div class="form-group row">
                <label
                  for="inputBio"
                  class="col-sm-2 col-form-label"
                >Bio</label>
                <div class="col-sm-10">
                  <textarea
                    class="form-control"
                    id="bio"
                    name="bio"
                    placeholder="Short Bio For User (optional)"
                    v-model="form.bio"
                  ></textarea>
                  <span
                    class="invalid-feedback d-block"
                    role="alert"
                    v-if="form.errors.has('bio')"
                    v-text="form.errors.get('bio')"
                  ></span>
                </div>
              </div>

              <div class="form-group row">
                <label
                  for="inputType"
                  class="col-sm-2 col-form-label"
                >Type</label>
                <div class="col-sm-10">
                  <select
                    class="form-control"
                    id="type"
                    name="type"
                    v-model="form.type"
                    required
                  >
                    <option value="">Select User Role</option>
                    <option value="admin">Admin</option>
                    <option value="user">Standard User</option>
                    <option value="author">Author</option>
                  </select>
                  <span
                    class="invalid-feedback d-block"
                    role="alert"
                    v-if="form.errors.has('type')"
                    v-text="form.errors.get('type')"
                  ></span>
                </div>
              </div>

              <div class="form-group row">
                <label
                  for="password"
                  class="col-sm-2 col-form-label"
                >Password</label>
                <div class="col-sm-10">
                  <input
                    type="password"
                    class="form-control"
                    id="password"
                    v-model="form.password"
                    required
                  >
                  <span
                    class="invalid-feedback d-block"
                    role="alert"
                    v-if="form.errors.has('password')"
                    v-text="form.errors.get('password')"
                  ></span>
                </div>
              </div>
            </div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-danger"
                data-dismiss="modal"
              >Close</button>
              <button
                v-show="editmode"
                type="submit"
                class="btn btn-success"
              >Update</button>
              <button
                v-show="!editmode"
                type="submit"
                class="btn btn-primary"
              >Create</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      editmode: false,
      users: {},
      form: new Form({
        id: "",
        name: "",
        email: "",
        password: "",
        type: "",
        bio: "",
        photo: ""
      })
    };
  },
  methods: {
    getResults(page = 1) {
      axios.get("api/user?page=" + page).then(response => {
        this.users = response.data;
      });
    },
    updateUser() {
      //   console.log("editing data");
      this.$Progress.start();
      this.form
        .put("api/user/" + this.form.id)
        .then(() => {
          $("#addNew").modal("hide");
          swal.fire("Updated!", "User infomation has been updated!", "success");
          this.$Progress.finish();
          Fire.$emit("AfterCreate");
        })
        .catch(() => {
          this.$Progress.fail();
        });
    },
    editModal(user) {
      this.editmode = true;
      this.form.reset();
      $("#addNew").modal("show");
      this.form.fill(user);
    },
    newModal() {
      this.editmode = false;
      this.form.reset();
      $("#addNew").modal("show");
    },
    deleteUser(id) {
      swal
        .fire({
          title: "Are you sure?",
          text: "You wont be able to revert this!",
          icon: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Yes, delete it!"
        })
        .then(response => {
          //Send request to the server
          if (response.value) {
            this.form
              .delete("api/user/" + id)
              .then(() => {
                swal.fire("Deleted!", "Your file has been deleted!", "success");
                Fire.$emit("AfterCreate");
              })
              .catch(() => {
                swal.fire("Failed", "There was something wrong.", "warning");
              });
          }
        });
    },
    loadUsers() {
      if (this.$gate.isAdminOrAuthor()) {
        axios.get("api/user").then(({ data }) => (this.users = data));
      }
    },
    createUser() {
      this.$Progress.start();
      this.form
        .post("api/user")
        .then(() => {
          Fire.$emit("AfterCreate");

          $("#addNew").modal("hide");

          toast.fire({
            icon: "success",
            title: "User Created Succefully"
          });

          this.$Progress.finish();
        })
        .catch(() => {});
    }
  },
  created() {
    this.loadUsers();
    Fire.$on("AfterCreate", () => {
      this.loadUsers();
    });
    Fire.$on("searching", () => {
      let query = this.$parent.search;
      axios
        .get("api/findUser?q=" + query)
        .then(data => {
          this.users = data.data;
        })
        .catch(() => {});
    });
  }
};
</script>
