<script>
	import axios from 'axios'
	import Pagination from './Pagination.vue'

  let config = {
    headers: {'Authorization': 'CWB-2637AACE-A630-418A-87BE-D04AB1617362'}
  }
  export default {
		name: 'hello',
		components: {
			Pagination
		},
    data () {
      return {
        opened: [],
        twCity: [],
        twData: {},
        search: "",
        countOfPage: 10,
        currPage: 1,
        sort: "",
        asc: true,
        sortKey: ''
      }
    },
    methods: {
      getData () {
        axios.get('/data/api/v1/rest/datastore/F-D0047-091', config)
        .then(res => {
          res = res.data;
          let data = res.records.locations[0].location;
          this.arrangeData(data);
        }).catch(err => {
          console.log(err)
        })
      },
      arrangeData (data) {
        let dataId = 0;
        data.forEach((row, cityId) => {
          let cityData = {
            cityId: cityId,
            city: row.locationName
          };
          let cityObj = [];
          let temperatureData = row.weatherElement[1].time;
          let RHData = row.weatherElement[2].time;
          for (let i = 0; i < temperatureData.length; i++) {
            let tmp = {};
            tmp.dataId = dataId;
            tmp.cityId = cityId;
            tmp.city = row.locationName;
            tmp.temperature = temperatureData[i].elementValue[0].value;
            tmp.RH = RHData[i].elementValue[0].value;
            tmp.startTime = temperatureData[i].startTime;
            tmp.endTime = temperatureData[i].endTime;
            cityObj.push(tmp);
            dataId++;
          }
          this.twData[cityId] = cityObj;
          this.twCity.push(cityData);
        });
      },
      toggleDetail (cityId) {
        const index = this.opened.indexOf(cityId);
        if (index === -1) {
          this.opened.push(cityId);
        } else {
          this.opened.splice(index, 1);
        }
      },
      dustStatusCondition (row) {
        return row.RH >= 60 && row.temperature >= 20 && row.temperature <= 30 === true;
      },
      sortTable (key, direction) {
        this.sort = `${key} > ${direction}`;
        this.asc = this.asc === false;
        this.sortKey = key;
        Object.keys(this.twData).map((objectKey, index) => {
          this.twData[objectKey].sort((a, b) => {
            if (direction === 'asc') {
              return a[key] > b[key] ? 1 : -1;
            } else {
              return a[key] < b[key] ? 1 : -1;
            }
          })
        });
			},
			updateCurrPage (page) {
				this.currPage = page;
        console.log("TCL: updateCurrPage -> currPage", page);
			}
    },
    computed: {
      filteredRows: function () {
        return (this.search.trim() !== '')
        ? this.twCity.filter(row => row.city.includes(this.search))
        : this.twCity;
      },
      sortIcon: function () {
        return this.asc === true ? 'sort-amount-up' : 'sort-amount-down-alt';
      }
    },
    mounted: function () {
      this.getData()
    }
  }

</script>
<template>
  <div class="hello container">
    ＊塵蟎適合的生存溫度為20 ~ 30度C、相對濕度60% ~ 80%
    <div class="input-group mb-3">
      <input type="text" class="form-control"
        v-model="search"
        placeholder="Input City Name">
    </div>
    {{sort}}
    <table class="table">
      <thead class="thead-dark">
        <tr>
          <th scope="col">Country</th>
          <th scope="col">City</th>
          <th scope="col" class="cursor" @click.prevent="sortTable('endTime', asc ? 'asc' : 'desc')">
            Time
            <font-awesome-icon v-show="sortKey.length === 0" :icon="['fas', 'sort'] " />
            <font-awesome-icon v-show="sortKey.length !== 0 && sortKey === 'endTime' " :icon="['fas', sortIcon] " />
          </th>
          <th scope="col" class="cursor" @click.prevent="sortTable('temperature', asc ? 'asc' : 'desc')">
            Temperature(°C)
            <font-awesome-icon v-show="sortKey.length === 0" :icon="['fas', 'sort'] " />
            <font-awesome-icon v-show="sortKey.length !== 0 && sortKey === 'temperature' " :icon="['fas', sortIcon] " />
          </th>
          <th scope="col" class="cursor" @click.prevent="sortTable('RH', asc ? 'asc' : 'desc')">
            Humidity(%)
            <font-awesome-icon v-show="sortKey.length === 0" :icon="['fas', 'sort'] " />
            <font-awesome-icon v-show="sortKey.length !== 0 && sortKey === 'RH' " :icon="['fas', sortIcon] " />
          </th>
          <th scope="col">Dust mite situation</th>
        </tr>
      </thead>
      <template v-for="cityRow in filteredRows.slice(currPage, currPage + countOfPage)">
        <tr @click.prevent="toggleDetail(cityRow.cityId)"
          :class="{ opened: opened.includes(cityRow.cityId)}"
          class="title"
          :key="'C'+cityRow.cityId">
          <th scope="row">Taiwan</th>
          <th>{{cityRow.city}}
            <font-awesome-icon :icon="['fas', opened.includes(cityRow.cityId) ?'sort-down' : 'sort-up' ] " />
          </th>
          <td>-</td>
          <td>-</td>
          <td>-</td>
          <td>-</td>
        </tr>
        <template v-for="row in twData[cityRow.cityId]">
          <tr v-show="opened.includes(cityRow.cityId)" :key="'D'+row.dataId">
            <td>-</td>
            <td>{{row.city}}</td>
            <td>{{row.startTime}} - {{row.endTime}}</td>
            <td>{{row.temperature}}</td>
            <td>{{row.RH}}</td>
            <td :class="[dustStatusCondition (row) ? 'alert-danger' : 'alert-success']">
              {{dustStatusCondition (row) ? "Danger": "Safe"}}
            </td>
          </tr>
        </template>
      </template>
    </table>

    <pagination
			:OnePageRow = "countOfPage"
			:filteredRowsLength = "filteredRows.length"
			@update-currPage = "updateCurrPage"
		/>
    <div v-show="filteredRows.length === 0">
      Not found
    </div>

  </div>
</template>
<style>
.title {
  cursor: pointer;
}
.opened {
  background-color:#DDDDDD;
}
.page-item.disabled .page-link {
  color: #868e96;
  pointer-events: none;
  cursor: auto;
  background-color:#DDDDDD;
  border-color: #dee2e6;
}
.cursor {
  cursor: pointer;
}
</style>
