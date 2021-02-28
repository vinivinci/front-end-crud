<template>
    <div>
        <modal
            v-on:close="closeModal"
            v-on:create="create"
            v-on:edit="update"
            v-if="openModal"
            :openModal="openModal"
            :user="currentUser"
            :title="titleModal"
        />
        <mdb-modal :show="openModalDelete" @close="openModalDelete = false">
            <mdb-modal-header>
                <mdb-modal-title>Confirm Deletion</mdb-modal-title>
            </mdb-modal-header>
            <mdb-modal-footer>
                <mdb-btn
                    color="blue-grey"
                    @click.native="openModalDelete = false"
                    >Cancel</mdb-btn
                >
                <mdb-btn @click.native="deleteUserApi" color="danger"
                    >Confirm</mdb-btn
                >
            </mdb-modal-footer>
        </mdb-modal>
        <top-bar />
        <mdb-row style="width:100%">
            <mdb-col
                style="justify-content:end;
                align-items:baseline;
                display:flex;
                margin-top:10px;
                padding:0px;
                padding-left:30px"
                col="3"
            >
                <mdbIcon icon="search" />
                <input
                    @input="getUsersForName()"
                    v-model="nameSearched"
                    type="text"
                    class="form-control ml-2"
                    style="width:100%"
                    placeholder="Search"
                    aria-label="Search"
                />
            </mdb-col>
            <mdb-col style="padding:0px;display:flex;justify-content:flex-end">
                <mdb-btn
                    v-if="user.permission === 'admin'"
                    @click.native="beforeCreateUser"
                    color="primary"
                >
                    <mdb-icon icon="plus" />
                    NEW USER
                </mdb-btn>
            </mdb-col>
        </mdb-row>
        <mdb-alert style="text-transform:uppercase" v-show="alertFlag" color="danger">
          it is not possible to exclude yourself
        </mdb-alert>
        <mdb-tbl btn responsive striped>
            <mdb-tbl-head>
                <tr>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Permission</th>
                    <th>Phone</th>
                    <th v-if="user.permission === 'admin'">Actions</th>
                </tr>
            </mdb-tbl-head>

            <mdb-tbl-body v-for="userIterator in data" :key="userIterator.id">
                <tr>
                    <td>{{ userIterator.name }}</td>
                    <td>{{ userIterator.email }}</td>
                    <td>{{ userIterator.permission }}</td>
                    <td>{{ userIterator.phone }}</td>
                    <td v-if="user.permission === 'admin'">
                        <mdb-icon
                            style="cursor:pointer"
                            @click.native="setUser(userIterator)"
                            icon="edit"
                        />
                        <mdb-icon
                            style="color:red;cursor:pointer"
                            @click.native="deleteUser(userIterator)"
                            class="ml-2"
                            icon="trash-alt"
                        />
                    </td>
                </tr>
            </mdb-tbl-body>
        </mdb-tbl>
    </div>
</template>
<script>
import {
  mdbIcon,
  mdbTbl,
  mdbTblHead,
  mdbTblBody,
  mdbRow,
  mdbCol,
  mdbBtn,
  mdbModal,
  mdbModalHeader,
  mdbModalTitle,
  mdbModalFooter,
  mdbAlert,
} from 'mdbvue';
import axios from 'axios';
import TopBar from '../components/topBar.vue';
import Modal from '../components/modal.vue';

export default {
  name: 'DatatablePage',
  components: {
    TopBar,
    mdbIcon,
    mdbTbl,
    mdbTblHead,
    mdbModal,
    mdbTblBody,
    mdbRow,
    mdbCol,
    mdbBtn,
    Modal,
    mdbModalHeader,
    mdbModalTitle,
    mdbModalFooter,
    mdbAlert,
  },
  data() {
    return {
      data: {},
      nameSearched: '',
      openModal: false,
      openModalDelete: false,
      user: {},
      alertFlag: false,
      currentUser: {
        name: '',
        email: '',
        phone: '',
        permission: 'standard',
        titleModal: '',
      },
    };
  },
  beforeMount() {
    const token = sessionStorage.getItem('token') != null
      ? sessionStorage.getItem('token')
      : localStorage.getItem('token');
    this.user = sessionStorage.getItem('user') != null
      ? JSON.parse(sessionStorage.getItem('user'))
      : JSON.parse(localStorage.getItem('user'));
    axios.defaults.headers.common.Authorization = `Bearer ${token}`;
    this.getUsers();
  },
  methods: {
    showAlert() {
      this.alertFlag = true;
      setTimeout(() => {
        this.alertFlag = false;
      }, 5000);
    },
    deleteUser(user) {
      this.openModalDelete = true;
      this.currentUser = user;
    },
    setUser(user) {
      this.titleModal = 'Edit User';
      this.currentUser = user;
      this.openModal = true;
    },
    beforeCreateUser() {
      this.titleModal = 'Create User';
      this.openModal = true;
    },
    closeModal() {
      this.currentUser = {
        name: '',
        email: '',
        phone: '',
        permission: 'standard',
      };
      this.openModal = false;
    },
    deleteUserApi() {
      const id = this.currentUser._id;
      if (id === this.user._id) {
        this.showAlert();
        this.openModalDelete = false;
      } else {
        axios
          .delete(`${process.env.VUE_APP_URL}/app/delete/${id}`)
          .then((response) => {
            if (response) {
              this.openModalDelete = false;
              this.currentUser = {
                name: '',
                email: '',
                phone: '',
                permission: 'standard',
                titleModal: '',
              };
              this.getUsers();
            }
          })
          .catch((err) => {
            alert(err);
          });
      }
    },
    create(e) {
      axios
        .post(`${process.env.VUE_APP_URL}/app/register`, {
          name: e.name,
          email: e.email,
          password: e.password,
          phone: e.phone,
          permission: e.permission,
        })
        .then((response) => {
          if (response) {
            this.openModal = false;
            this.getUsers();
          }
        })
        .catch((err) => {
          alert(err);
        });
    },
    update(e) {
      const id = this.currentUser._id;
      axios
        .put(`${process.env.VUE_APP_URL}/app/update/${id}`, {
          name: e.name,
          email: e.email,
          password: e.password,
          phone: e.phone,
          permission: e.permission,
        })
        .then((response) => {
          if (response) {
            this.openModal = false;
            this.getUsers();
          }
        })
        .catch((err) => {
          alert(err);
        });
    },
    getUsers() {
      axios
        .get(`${process.env.VUE_APP_URL}/app/get`)
        .then((response) => {
          if (response.data) {
            this.data = response.data;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getUsersForName() {
      axios
        .get(`${process.env.VUE_APP_URL}/app/getForName`, {
          params: {
            name: this.nameSearched,
          },
        })
        .then((response) => {
          if (response.data) {
            this.data = response.data;
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>
