<template>
  <CCard>
    <CCardHeader>
      <slot name="header">
        <CIcon name="cil-grid"/> {{caption}}
      </slot>
    </CCardHeader>
    <CCardBody>
      <CButton color="primary" @click="handleAdd" class="mb-2">
        Add Customer
      </CButton>
      <CModal
        title="Add Customer"
        :show.sync="myModal"
        hide-footer
      >
        <CRow>
          <CCol sm="6">
            <CInput
              label="Name"
              placeholder="Enter your customer name"
              v-model="customerData.name"
            />
          </CCol>
          <CCol sm="6">
            <CInput
              label="Contact"
              placeholder="Enter your customer Contact"
              v-model="customerData.contact"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Email"
              placeholder="Enter your customer email"
              v-model="customerData.email"
            />
          </CCol>
          <CCol sm="6">
            <CTextarea
              label="Address"
              placeholder="Customer Address"
              v-model="customerData.address"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Discount"
              placeholder="Enter discount"
              v-model="customerData.discount"
            />
          </CCol>
          <CCol sm="6">
            <CSelect
              label="Tipe Discount"
              :options="['FIX', 'PERCENT']"
              @change="handleDiscountType"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol>
            <label>KTP</label>
            <CInputFile
              horizontal
              custom
              @change="inputFile"
            />
          </CCol>
        </CRow>
        <template #footer>
          <CButton @click="myModal = false" color="secondary">Close</CButton>
          <CButton @click="submitData()" color="success">Submit Data</CButton>
        </template>
      </CModal>
      <CModal
        title="Edit Customer"
        :show.sync="editModal"
        hide-footer
      >
        <CRow>
          <CCol sm="6">
            <CInput
              label="Name"
              placeholder="Enter your customer name"
              v-model="customerData.name"
            />
          </CCol>
          <CCol sm="6">
            <CInput
              label="Contact"
              placeholder="Enter your customer Contact"
              v-model="customerData.contact"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Email"
              placeholder="Enter your customer email"
              v-model="customerData.email"
            />
          </CCol>
          <CCol sm="6">
            <CTextarea
              label="Address"
              placeholder="Customer Address"
              vertical
              rows="9"
              v-model="customerData.address"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Discount"
              placeholder="Enter discount"
              v-model="customerData.discount"
            />
          </CCol>
          <CCol sm="6">
            <CSelect
              label="Tipe Discount"
              :options="['FIX', 'PERCENT']"
              @change="handleDiscountType"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol>
            <label>KTP</label>
            <CInputFile
              horizontal
              custom
              @change="inputFile"
            />
          </CCol>
        </CRow>
        <template #footer>
          <CButton @click="myModal = false" color="secondary">Close</CButton>
          <CButton @click="updateCustomer()" color="success">Submit Data</CButton>
        </template>
      </CModal>

      <CDataTable
        :hover="hover"
        :striped="striped"
        :border="border"
        :small="small"
        :fixed="fixed"
        :items="items"
        :fields="fields"
        :items-per-page="small ? 10 : 5"
        :dark="dark"
        pagination
      >
        <template #status="{item}">
          <td>
            <CBadge :color="'success'" @click="handleEdit(item.id)" class="mr-2" style="cursor: pointer;">Edit</CBadge>
            <CBadge :color="'danger'" @click="handleDelete(item.id)" style="cursor: pointer;">Delete</CBadge>
          </td>
        </template>
      </CDataTable>
    </CCardBody>
  </CCard>
</template>

<script>
export default {
  name: 'Table',
  props: {
    items: Array,
    fields: {
      type: Array,
      default () {
        return ['ktp', 'name', 'contact', 'email', 'address', 'discount', 'discount_type', 'status']
      }
    },
    caption: {
      type: String,
      default: 'Table'
    },
    hover: Boolean,
    striped: Boolean,
    border: Boolean,
    small: Boolean,
    fixed: Boolean,
    dark: Boolean
  },
  methods: {
    getBadge (status) {
      return status === 'Active' ? 'success'
        : status === 'Inactive' ? 'secondary'
          : status === 'Pending' ? 'warning'
            : status === 'Banned' ? 'danger' : 'primary'
    },
    submitData () {
      console.log(this.customerData)
      const files = event.target.files
      const formData = new FormData()
      formData.append('name', this.customerData.name)
      formData.append('contact', this.customerData.contact)
      formData.append('email', this.customerData.email)
      formData.append('address', this.customerData.address)
      formData.append('discount', this.customerData.discount)
      formData.append('discountType', this.customerData.discountType)
      formData.append('ktp', this.customerData.ktp)

      fetch('http://127.0.0.1:3333/api/v1/storeCustomer', {
        method: 'POST',
        body: formData
      })
      .then(response => response.json())
      .then(data => {
        this.myModal = false
        console.log(data)
      })
      .catch(error => {
        console.error(error)
      })
    },
    inputFile(file) {
      this.customerData.ktp = file[0]
    },
    handleDiscountType(event) {
      console.log(event)
      this.customerData.discountType = event.target.value
    },
    handleProductName(event) {
      console.log(event, 'This Event')
    },
    handleDelete(id) {
      fetch(`http://127.0.0.1:3333/api/v1/deleteCustomer/${id}`, {
        method: 'DELETE',
      })
      .then(response => response.json())
      .then(data => {
        console.log(data)
      })
      .catch(error => {
        console.error(error)
      })
    },
    handleAdd() {
      this.myModal = true
      this.customerData = {
        id: '',
        name: '',
        contact: '',
        email: '',
        address: '',
        discount: 0,
        discountType: 'FIX',
        ktp: ''
      }
    },
    handleEdit(id) {
      fetch(`http://127.0.0.1:3333/api/v1/getCustomer/${id}`, {
        method: 'GET',
      })
      .then(response => response.json())
      .then(res => {
        console.log(res, 'this res')
        this.customerData.name = res.data.name
        this.customerData.id = res.data.id
        this.customerData.contact = res.data.contact
        this.customerData.email = res.data.email
        this.customerData.address = res.data.address
        this.customerData.discount = res.data.discount
        this.customerData.discountType = res.data.discount_type
        this.customerData.ktp = res.data.ktp
        this.editModal = true
      })
      .catch(error => {
        console.error(error)
      })
    },
    updateCustomer() {
      const id = this.customerData.id
      const files = event.target.files
      const formData = new FormData()
      formData.append('name', this.customerData.name)
      formData.append('contact', this.customerData.contact)
      formData.append('email', this.customerData.email)
      formData.append('address', this.customerData.address)
      formData.append('discount', this.customerData.discount)
      formData.append('discountType', this.customerData.discountType)
      formData.append('ktp', this.customerData.ktp)

      fetch(`http://127.0.0.1:3333/api/v1/updateCustomer/${id}`, {
        method: 'PUT',
        body: formData
      })
      .then(response => response.json())
      .then(data => {
        console.log(data)
        this.editModal = false
        this.customerData = {
          id: '',
          name: '',
          contact: '',
          email: '',
          address: '',
          discount: 0,
          discountType: 'FIX',
          ktp: ''
        }
      })
      .catch(error => {
        console.error(error)
      })
    }
  },
  data () {
    return {
      myModal: false,
      editModal: false,
      customerData: {
        id: '',
        name: '',
        contact: '',
        email: '',
        address: '',
        discount: 0,
        discountType: 'FIX',
        ktp: ''
      }
    }
  }
}
</script>
