<template>
  <div>
    <CRow>
      <CCol lg="12">
        <CTableWrapper
          :items="getAllData()"
          fixed
          border
          caption="Sales Table"
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
      sales: []
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
      fetch("http://127.0.0.1:3333/api/v1/getAllItem")
        .then(response => response.json())
        .then(data => {
          this.sales = data.data
        })
      // console.log(this.product, "This Product")
      return this.sales
      
      // return [['Sabun', 'PCS', 3, 12000, 'BLA' ]]
    },
  }
}
</script>
