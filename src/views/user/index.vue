<template>
  <div class="card my-3">
    <div class="card-header d-flex justify-content-between">
      <h4>User List (with Custom Pagination, Filter)</h4>
      <button class="btn btn-danger" @click="openModal()">Add New User</button>
    </div>
    <div class="card-body table-responsive">
      <table class="table">
        <thead>
        <tr>
          <th scope="col">
            Name
            <div class="d-flex">
              <input type="text" placeholder="Search By Name" class="form-control filter-input" v-model="filterData.name"/>
              <button class="btn text-danger fw-bolder" v-if="filterData.name" @click="this.filterData.name = ''">x</button>
            </div>
          </th>
          <th scope="col">
            Username
            <div class="d-flex">
              <input type="text" placeholder="Search By Username" class="form-control filter-input" v-model="filterData.username"/>
              <button class="btn text-danger fw-bolder" v-if="filterData.username" @click="this.filterData.username = ''">x</button>
            </div>
          </th>
          <th scope="col">
            Email
            <div class="d-flex">
              <input type="text" placeholder="Search By Email" class="form-control filter-input" v-model="filterData.email"/>
              <button class="btn text-danger fw-bolder" v-if="filterData.email" @click="this.filterData.email = ''">x</button>
            </div>
          </th>
          <th scope="col">
            Actions
          </th>
        </tr>
        </thead>
        <tbody>
        <tr v-if="currentUsers.length===0">
          <td colspan="4">
            No Data Found
          </td>
        </tr>
        <tr v-for="user in currentUsers" :key="user.id">
          <td>{{ user.name }}</td>
          <td>{{ user.username }}</td>
          <td>{{ user.email }}</td>
          <td>
            <router-link :to="`/users/${user.id}`" class="btn btn-sm btn-info mr-5">
              <i class="pi pi-eye"/>
            </router-link>
            <button class="btn btn-sm btn-primary mr-5" @click="editUserHandler(user)">
              <i class="pi pi-user-edit"/>
            </button>
            <button class="btn btn-sm btn-danger" @click="onDelete(user.id)">
              <i class="pi pi-trash"/>
            </button>
          </td>
        </tr>
        </tbody>
      </table>
      <Pagination
          :totalRecords="this.getAllUsers"
          @onPageChange="this.onPageChange"
      />
    </div>
  </div>
  <UserAddModal
      :editable="editable"
      :selectedUser="selectedUser"
      v-show="visible"
      @close="closeModal"
  />
</template>

<script>
import {mapActions, mapState, mapGetters} from 'vuex'
import UserAddModal from "@/components/user/UserAddModal";
import Pagination from "@/components/pagination/Pagination";

export default {
  name: "UserList",
  components: {Pagination, UserAddModal},
  data() {
    return {
      visible: false,
      editable: false,
      selectedUser: {},
      currentUsers: [],
      filterData: {
        name: '',
        username: '',
        email: ''
      },
      filterActive: false
    }
  },

  methods: {
    ...mapActions('user', ['fetchUsersList', 'deleteUser', 'filterUser']),
    openModal(editable = false) {
      if (!editable) {
        this.selectedUser = {}
      }
      this.visible = true;
      this.editable = editable;
    },
    closeModal() {
      this.visible = false;
    },
    onDelete(id) {
      if (!confirm('Are you sure to delete?')) return false;
      this.deleteUser(id);
      this.$toast.add({severity: 'success', summary: 'Success', detail: 'User Deleted Successfully', life: 3000});
    },
    editUserHandler(user) {
      this.selectedUser = user
      this.openModal(true)
    },
    onPageChange(pageData) {
      let offset = (pageData.activePage - 1) * pageData.pageLimit;
      this.currentUsers = this.filteredUsers.slice(offset, offset + pageData.pageLimit)
    },
  },
  created() {
    this.fetchUsersList();
  },
  computed: {
    ...mapState({
      loading: state => state.user.loading,
      filteredUsers: state => state.user.filteredUsers
    }),
    ...mapGetters('user', ['getAllUsers'])
  },
  watch: {
    filterData: {
      handler(value) {
        this.filterUser(value)
      },
      deep: true
    }
  }
}
</script>

<style>
.filter-input {
  width: 50%
}
</style>
