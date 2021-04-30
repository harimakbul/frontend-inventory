<template>
  <div>
    <CRow>
      <CCol lg="12">
        <CTableWrapper
          :items="getAllData()"
          fixed
          border
          caption="Product Table"
        />
      </CCol>
    </CRow>
  </div>
</template>

<script>
import CTableWrapper from './Table.vue'
import usersData from '../users/UsersData'

export default {
  name: 'Tables',
  components: { CTableWrapper },
  data() {
    return {
      product: []
    }
  },
  methods: {
    shuffleArray (array) {
      for (let i = array.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1))
        let temp = array[i]
        array[i] = array[j]
        array[j] = temp
      }
      console.log(array, "This Arry")
      return array
    },
    getAllData () {
      let allData = []
      fetch("http://127.0.0.1:3333/api/v1/getAllProduct")
        .then(response => response.json())
        .then(data => {
          this.product = data.data
        })
      // console.log(this.product, "This Product")
      return this.product
      
      // return [['Sabun', 'PCS', 3, 12000, 'BLA' ]]
    },
  }
}
</script>
