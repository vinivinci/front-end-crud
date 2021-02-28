<template>
    <div>
        <modal
          v-on:close="openModal=false"
          v-on:create="create"
          v-if="openModal" :openModal="openModal"
          :user = currentUser
        />
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
                @click.native="openModal = true"
                 color="primary"
                >
                  <mdb-icon icon="plus" />
                  NEW USER
                </mdb-btn>
            </mdb-col>
        </mdb-row>
        <mdb-tbl btn responsive striped>
            <mdb-tbl-head>
                <tr>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Permission</th>
                    <th>Phone</th>
                    <th>Actions</th>
                </tr>
            </mdb-tbl-head>

            <mdb-tbl-body v-for="user in data" :key="user.id">
                <tr>
                    <td>{{ user.name }}</td>
                    <td>{{ user.email }}</td>
                    <td>{{ user.permission }}</td>
                    <td>{{ user.phone }}</td>
                    <td>
                        <mdb-icon style="cursor:pointer"
                        @click.native="setUser(user)"
                        icon="edit" />
                        <mdb-icon
                            style="cursor:pointer"
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
    mdbTblBody,
    mdbRow,
    mdbCol,
    mdbBtn,
    Modal,
  },
  data() {
    return {
      data: {},
      nameSearched: '',
      openModal: false,
      currentUser: {
        name: '',
        email: '',
        phone: '',
        permission: '',
      },
    };
  },
  beforeMount() {
    const token = sessionStorage.getItem('token') != null
      ? sessionStorage.getItem('token')
      : localStorage.getItem('token');
    axios.defaults.headers.common.Authorization = `Bearer ${token}`;
    this.getUsers();
  },
  methods: {
    setUser(user) {
      this.currentUser = user;
      this.openModal = true;
    },
    create(e) {
      console.log(e);
      axios.post('http://localhost:8080/app/register', {
        name: e.name,
        email: e.email,
        password: e.password,
        phone: e.phone,
        permission: e.permission,
      }).then((response) => {
        if (response) {
          this.openModal = false;
          this.getUsers();
        }
      }).catch((err) => {
        alert(err);
      });
    },
    getUsers() {
      axios
        .get('http://localhost:8080/app/get')
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
        .get('http://localhost:8080/app/getForName', {
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
