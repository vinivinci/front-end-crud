<template>
  <div>
    <mdb-navbar dark color="primary">
      <mdb-navbar-brand>Users aplication</mdb-navbar-brand>
      <mdb-navbar-toggler style="justify-content:flex-end;color:white">
     <mdb-dropdown tag="li" class="nav-item">
            <mdb-dropdown-toggle
              tag="a"
              navLink
              color="primary"
              slot="toggle"
              waves-fixed
              ><mdb-icon far icon="user-circle" /> {{user.name}}</mdb-dropdown-toggle
            >
            <mdb-dropdown-menu style="width:90%">
              <mdb-dropdown-item @click.native="logout">Logout</mdb-dropdown-item>
            </mdb-dropdown-menu>
          </mdb-dropdown>
      </mdb-navbar-toggler>
    </mdb-navbar>
  </div>
</template>
<script>
import {
  mdbNavbar,
  mdbNavbarToggler,
  mdbDropdown,
  mdbDropdownItem,
  mdbDropdownMenu,
  mdbDropdownToggle,
  mdbNavbarBrand,
  mdbIcon,
} from 'mdbvue';

export default {
  name: 'NavigationPage',
  components: {
    mdbNavbar,
    mdbNavbarToggler,
    mdbDropdown,
    mdbDropdownItem,
    mdbDropdownMenu,
    mdbDropdownToggle,
    mdbNavbarBrand,
    mdbIcon,
  },
  data() {
    return {
      user: {},
    };
  },
  methods: {
    logout() {
      localStorage.clear();
      sessionStorage.clear();
      window.location.href = '/';
    },
  },
  beforeMount() {
    this.user = sessionStorage.getItem('user') != null
      ? JSON.parse(sessionStorage.getItem('user'))
      : JSON.parse(localStorage.getItem('user'));
  },
};
</script>
<style scoped>
  .view {
    background: url("https://mdbootstrap.com/img/Photos/Others/img (42).jpg")
      no-repeat center center;
    background-size: cover;
    height: 100%;
  }

  .navbar .dropdown-menu a:hover {
    color: inherit !important;
  }
</style>
