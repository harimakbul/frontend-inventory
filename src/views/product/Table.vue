<template>
  <CCard>
    <CCardHeader>
      <slot name="header">
        <CIcon name="cil-grid"/> {{caption}}
      </slot>
    </CCardHeader>
    <CCardBody>
      <CButton color="primary" @click="handleAdd" class="mb-2">
        Add Product
      </CButton>
      <CModal
        title="Add Product"
        :show.sync="myModal"
        hide-footer
      >
        <CRow>
          <CCol sm="12">
            <CInput
              label="Product Name"
              placeholder="Enter your product name"
              v-model="productData.productName"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="4">
            <CSelect
              label="Unit"
              :options="['KG', 'PCS']"
              @change="handleUnit"
            />
          </CCol>
          <CCol sm="4">
            <CInput
              label="Stock"
              placeholder="Enter your product Stock"
              v-model="productData.stock"
            />
          </CCol>
          <CCol sm="4">
            <CInput
              label="Price"
              placeholder="Enter your Price"
              v-model="productData.price"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol>
            <label>Product Image</label>
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
        title="Edit Product"
        :show.sync="editModal"
        hide-footer
      >
        <CRow>
          <CCol sm="12">
            <CInput
              label="Product Name"
              placeholder="Enter your product name"
              v-model="productData.productName"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol sm="4">
            <CSelect
              label="Unit"
              :options="['KG', 'PCS']"
              @change="handleUnit"
            />
          </CCol>
          <CCol sm="4">
            <CInput
              label="Stock"
              placeholder="Enter your product Stock"
              v-model="productData.stock"
            />
          </CCol>
          <CCol sm="4">
            <CInput
              label="Price"
              placeholder="Enter your Price"
              v-model="productData.price"
            />
          </CCol>
        </CRow>
        <CRow>
          <CCol>
            <label>Product Image</label>
            <CInputFile
              horizontal
              custom
              @change="inputFile"
            />
          </CCol>
        </CRow>
        <template #footer>
          <CButton @click="editModal = false" color="secondary">Close</CButton>
          <CButton @click="updateProduct()" color="success">Submit Data</CButton>
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
        return ['product_image', 'product_name', 'unit', 'stock', 'price', 'status']
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
      console.log(this.productData.productName)
      const files = event.target.files
      const formData = new FormData()
      formData.append('productName', this.productData.productName)
      formData.append('unit', this.productData.unit)
      formData.append('stock', this.productData.stock)
      formData.append('price', this.productData.price)
      formData.append('productImage', this.productData.productImage)

      fetch('http://127.0.0.1:3333/api/v1/storeProduct', {
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
      this.productData.productImage = file[0]
    },
    handleUnit(event) {
      console.log(event)
      this.productData.unit = event.target.value
    },
    handleProductName(event) {
      console.log(event, 'This Event')
    },
    handleDelete(id) {
      fetch(`http://127.0.0.1:3333/api/v1/deleteProduct/${id}`, {
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
      this.productData = {
        id: '',
        productName: '',
        unit: 'KG',
        price: 0,
        stock: 0,
        productImage: ''
      }
    },
    handleEdit(id) {
      fetch(`http://127.0.0.1:3333/api/v1/getProduct/${id}`, {
        method: 'GET',
      })
      .then(response => response.json())
      .then(res => {
        this.productData.productName = res.data.product_name
        this.productData.id = res.data.id
        this.productData.unit = res.data.unit
        this.productData.price = res.data.price
        this.productData.stock = res.data.stock
        this.productData.productImage = res.data.productImage
        this.editModal = true
      })
      .catch(error => {
        console.error(error)
      })
    },
    updateProduct() {
      console.log(this.productData.productName)
      const id = this.productData.id
      const files = event.target.files
      const formData = new FormData()
      formData.append('productName', this.productData.productName)
      formData.append('unit', this.productData.unit)
      formData.append('stock', this.productData.stock)
      formData.append('price', this.productData.price)
      formData.append('productImage', this.productData.productImage)

      fetch(`http://127.0.0.1:3333/api/v1/updateProduct/${id}`, {
        method: 'PUT',
        body: formData
      })
      .then(response => response.json())
      .then(data => {
        console.log(data)
        this.editModal = false
        this.productData = {
          id: '',
          productName: '',
          unit: 'KG',
          price: 0,
          stock: 0,
          productImage: ''
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
      productData: {
        id: '',
        productName: '',
        unit: 'KG',
        price: 0,
        stock: 0,
        productImage: ''
      }
    }
  }
}
</script>
