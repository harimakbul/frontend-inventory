<template>
  <CCard>
    <CCardHeader>
      <slot name="header">
        <CIcon name="cil-grid"/> {{caption}}
      </slot>
    </CCardHeader>
    <CContainer class="mt-2">
      <CRow>
        <CCol lg="4" md="4">
          <CCard>
            <CCardBody>
              <CInput
                label="Transaction Code"
                placeholder="Enter transaction code"
                v-model="salesData.name"
              />
              <CInput
                label="Transaction Date"
                placeholder="Enter transaction Date"
                type="date"
                v-model="salesData.date"
              />
              <CSelect
                label="Customer"
                :options="['FIX', 'PERCENT']"
                @change="handleDiscountType"
              />
            </CCardBody>
          </CCard>
        </CCol>
        <CCol lg="8" md="8">
          <CCard>
          <table class="table table-striped">
            <thead>
              <tr>
                <th>Product Name</th>
                <th>Unit</th>
                <th>Stock</th>
                <th>Price</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="post in product" :key="post.id">
                <td>{{ post.product_name }}</td>
                <td>{{ post.unit }}</td>
                <td>{{ post.stock }}</td>
                <td>{{ post.price }}</td>
                <td>
                  <CBadge :color="'success'" @click="handleAddCart(post)" class="mr-2" style="cursor: pointer;">ADD PRODUCT</CBadge>
                </td>
              </tr>
            </tbody>
          </table>
            <!-- <CDataTable
              :hover="hover"
              :striped="striped"
              :border="border"
              :small="small"
              :fixed="fixed"
              :items="items2"
              :fields="product"
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
            </CDataTable> -->
          </CCard>
        </CCol>
      </CRow>
    </CContainer>
    <CCardBody>
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
              v-model="salesData.name"
            />
          </CCol>
          <CCol sm="6">
            <CInput
              label="Contact"
              placeholder="Enter your customer Contact"
              v-model="salesData.contact"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Email"
              placeholder="Enter your customer email"
              v-model="salesData.email"
            />
          </CCol>
          <CCol sm="6">
            <CTextarea
              label="Address"
              placeholder="Customer Address"
              v-model="salesData.address"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Discount"
              placeholder="Enter discount"
              v-model="salesData.discount"
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
              v-model="salesData.name"
            />
          </CCol>
          <CCol sm="6">
            <CInput
              label="Contact"
              placeholder="Enter your customer Contact"
              v-model="salesData.contact"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Email"
              placeholder="Enter your customer email"
              v-model="salesData.email"
            />
          </CCol>
          <CCol sm="6">
            <CTextarea
              label="Address"
              placeholder="Customer Address"
              vertical
              rows="9"
              v-model="salesData.address"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="6">
            <CInput
              label="Discount"
              placeholder="Enter discount"
              v-model="salesData.discount"
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
        <template #action="{item}">
          <td>
            <CBadge :color="'success'" @click="handleEdit(item.id)" class="mr-2" style="cursor: pointer;">Update</CBadge>
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
    items2: Array,
    fields: {
      type: Array,
      default () {
        return ['product', 'qty', 'total_price', 'action']
      }
    },
    fields2: {
      type: Array,
      default () {
        return ['product_image', 'product_name', 'unit', 'stock', 'price', 'action']
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
      console.log(this.salesData)
      const files = event.target.files
      const formData = new FormData()
      formData.append('name', this.salesData.name)
      formData.append('contact', this.salesData.contact)
      formData.append('email', this.salesData.email)
      formData.append('address', this.salesData.address)
      formData.append('discount', this.salesData.discount)
      formData.append('discountType', this.salesData.discountType)
      formData.append('ktp', this.salesData.ktp)

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
      this.salesData.ktp = file[0]
    },
    handleDiscountType(event) {
      console.log(event)
      this.salesData.discountType = event.target.value
    },
    handleProductName(event) {
      console.log(event, 'This Event')
    },
    handleDelete(id) {
      fetch(`http://127.0.0.1:3333/api/v1/deleteItem/${id}`, {
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
      this.salesData = {
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
    handleAddCart(item) {
      const formData = new FormData()
      formData.append('transactionCodeSales', '001')
      formData.append('product', item.product_name)
      formData.append('qty', 1)
      formData.append('totalPrice', item.price)

      fetch('http://127.0.0.1:3333/api/v1/storeItem', {
        method: 'POST',
        body: formData
      })
      .then(response => response.json())
      .then(data => {
        console.log(data)
      })
      .catch(error => {
        console.error(error)
      })
    },
    handleEdit(id) {
      fetch(`http://127.0.0.1:3333/api/v1/getCustomer/${id}`, {
        method: 'GET',
      })
      .then(response => response.json())
      .then(res => {
        console.log(res, 'this res')
        this.salesData.name = res.data.name
        this.salesData.id = res.data.id
        this.salesData.contact = res.data.contact
        this.salesData.email = res.data.email
        this.salesData.address = res.data.address
        this.salesData.discount = res.data.discount
        this.salesData.discountType = res.data.discount_type
        this.salesData.ktp = res.data.ktp
        this.editModal = true
      })
      .catch(error => {
        console.error(error)
      })
    },
    updateCustomer() {
      const id = this.salesData.id
      const files = event.target.files
      const formData = new FormData()
      formData.append('name', this.salesData.name)
      formData.append('contact', this.salesData.contact)
      formData.append('email', this.salesData.email)
      formData.append('address', this.salesData.address)
      formData.append('discount', this.salesData.discount)
      formData.append('discountType', this.salesData.discountType)
      formData.append('ktp', this.salesData.ktp)

      fetch(`http://127.0.0.1:3333/api/v1/updateCustomer/${id}`, {
        method: 'PUT',
        body: formData
      })
      .then(response => response.json())
      .then(data => {
        console.log(data)
        this.editModal = false
        this.salesData = {
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
    },
    product() {
      fetch("http://127.0.0.1:3333/api/v1/getAllProduct")
        .then(response => response.json())
        .then(data => {
          console.log(data, 'Hello')
          this.product = data.data
        })
    }
  },
  data () {
    this.product()
    return {
      myModal: false,
      editModal: false,
      salesData: {
        id: '',
        name: '',
        contact: '',
        email: '',
        address: '',
        discount: 0,
        discountType: 'FIX',
        ktp: ''
      },
      salesData: [],
      product: [],
    }
  }
}
</script>
