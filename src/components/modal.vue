<template>
  <div>
    <mdb-modal :show="modal" @close="onClose">
      <mdb-modal-header>
        <mdb-modal-title>{{titleMenu}}</mdb-modal-title>
      </mdb-modal-header>
      <mdb-modal-body>
          <mdb-input
                    v-model="name"
                    label="Your name"
                    icon="user"
                    type="text"
           />
           <mdb-input
                    v-model="password"
                    label="Your password"
                    icon="lock"
                    type="password"
           />
           <mdb-input
                    v-model="email"
                    label="Your email"
                    icon="envelope"
                    type="email"
           />
           <mdb-input
                    v-model="phone"
                    label="Your phone"
                    icon="phone"
                    type="number"
           />
           <select v-model="permission" class="browser-default custom-select custom-select-lg mb-3">
                <option value="admin">ADMIN</option>
                <option value="standard">STANDARD</option>
            </select>
      </mdb-modal-body>
      <mdb-modal-footer>
        <mdb-btn color="blue-grey" @click.native="onClose">Cancel</mdb-btn>
        <mdb-btn
          color="success"
          :class="{ disabled:
            email == '' ||
            password == '' ||
            phone == '' ||
            name == ''}"
          @click.native="onSubmit"
        >
          Save changes
        </mdb-btn>
      </mdb-modal-footer>
    </mdb-modal>
  </div>
</template>
<script>
import {
  mdbModal, mdbModalHeader, mdbModalTitle, mdbModalBody, mdbModalFooter, mdbBtn, mdbInput,
} from 'mdbvue';

export default {
  props: {
    openModal: {
      type: Boolean,
      default: false,
    },
    title: {
      type: String,
      default: 'Create user',
    },
    user: {
      type: Object,
      default: () => ({
        name: '',
        email: '',
        phone: '',
        permission: 'admin',
      }),
    },
  },
  components: {
    mdbModal,
    mdbModalHeader,
    mdbModalTitle,
    mdbModalBody,
    mdbModalFooter,
    mdbBtn,
    mdbInput,
  },
  data() {
    return {
      modal: this.openModal,
      name: this.user.name,
      password: this.user.name ? '*********' : '',
      email: this.user.email,
      phone: this.user.phone,
      permission: this.user.permission,
      titleMenu: this.title,
    };
  },
  methods: {
    onClose() {
      this.$emit('close');
    },
    onSubmit() {
      const userInfo = {
        name: this.name,
        password: this.password,
        email: this.email,
        phone: this.phone,
        permission: this.permission,
      };
      if (this.titleMenu === 'Create User') {
        this.$emit('create', userInfo);
      } else {
        userInfo.password = userInfo.password === '*********' ? undefined : userInfo.password;
        this.$emit('edit', userInfo);
      }
    },
  },
};
</script>
