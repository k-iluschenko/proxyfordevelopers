<template>
  <section class="container">
    <div class="row">
      <div class="sidebar col-sm-4 col-md-3 form">
        <div class="form__country">
          <select class="form-control" v-model="selectedCountry">
            <option disabled value="">Страны</option>
            <option
              v-for="country in countries"
              :key="country"
              :value="country"
            >
              {{ country }}
            </option>
          </select>
        </div>
        <div class="form__type">
          <div class="type__item" v-for="type in proxy_type" :key="type.id">
            <input
              class="form-check-input"
              type="checkbox"
              :value="type.id"
              :id="type.title"
              v-model="checkedType"
            />
            <label class="form-check-label" :for="type.title">
              {{ type.title }}
            </label>
          </div>
        </div>
        <div class="form__alive">
          <div>
            <input
              v-model="checkedAlive"
              class="form-check-input"
              type="checkbox"
              value=""
              id="aliveCheck"
            />
            <label class="form-check-label" for="aliveCheck">
              Alive
            </label>
          </div>
        </div>
        <div class="button" @click="clearFilter">
          Сбросить фильтр
        </div>
      </div>
      <div class="content col-sm-7 col-md-9">
        <table class="table table-striped table-bordered">
          <thead class="thead-dark">
            <tr>
              <th scope="col">ID</th>
              <th scope="col">Host:Port</th>
              <th class="disable_md_screen" scope="col">Country</th>
              <th class="disable_lg_screen" scope="col">Type</th>
              <th class="disable_lg_screen" scope="col">Last check time</th>
              <th scope="col">Response time</th>
              <th class="disable_sm_screen" scope="col">Alive</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(proxy, index) in filterProxies" :key="proxy.id">
              <th scope="row">{{ ++index }}</th>
              <td>{{ proxy.host }}:{{ proxy.port }}</td>
              <td class="disable_md_screen">{{ proxy.country }}</td>
              <td class="disable_lg_screen">
                {{ proxyType(proxy.proxy_type) }}
              </td>
              <td class="disable_lg_screen">
                {{ new Date(proxy.last_check_time).toLocaleString() }}
              </td>
              <td>
                {{ proxy.port_response_time }}
              </td>
              <td class="disable_sm_screen">{{ proxy.alive }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </section>
</template>
<script>
import axios from "axios";
const url = "https://proxyfordevelopers.com/api/proxies/?format=json";

//const crosProxy = 'https://cors-anywhere.herokuapp.com/'
//Обойти ошибку
//Access to XMLHttpRequest at 'https://proxyfordevelopers.com/api/proxies/?format=json' from origin 'http://localhost:8080' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.
const crosProxy = "";
export default {
  name: "proxiesPage",
  data() {
    return {
      proxies: [],
      proxy_type: [
        { id: 0, title: "Transparent" },
        { id: 1, title: "Anonymous" },
        { id: 2, title: "Elite" }
      ],
      // proxies: [ { "id": 5, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T14:41:09.364247+03:00", "host": "84.52.74.194", "port": 8080, "country": "EN", "alive": true, "last_check_time": "2019-02-23T18:53:57.949177+03:00", "busy_until": "2018-10-16T14:47:55.524905+03:00", "proxy_type": 1, "port_response_time": 1.166689, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 6, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T14:41:12.374605+03:00", "host": "91.203.36.188", "port": 8080, "country": "RU", "alive": true, "last_check_time": "2019-02-23T17:52:15.601483+03:00", "busy_until": "2018-10-16T14:46:32.401657+03:00", "proxy_type": 2, "port_response_time": 1.782754, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 16, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T13:55:15.644310+03:00", "host": "81.163.57.121", "port": 41258, "country": "GB", "alive": true, "last_check_time": "2019-02-23T19:54:05.609305+03:00", "busy_until": "2018-10-16T20:09:21.144691+03:00", "proxy_type": 2, "port_response_time": 1.035598, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 37, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-18T15:54:53.557085+03:00", "host": "46.146.203.124", "port": 42031, "country": "GB", "alive": true, "last_check_time": "2019-02-23T20:51:19.154716+03:00", "busy_until": "2018-10-16T14:46:35.757674+03:00", "proxy_type": 2, "port_response_time": 2.212496, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 88, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-18T14:55:26.250019+03:00", "host": "95.80.98.41", "port": 8080, "country": "RU", "alive": true, "last_check_time": "2019-02-23T17:01:56.592787+03:00", "busy_until": "2018-11-09T21:17:41.295535+03:00", "proxy_type": 2, "port_response_time": 3.156112, "chargeable": false, "use_counter": 1, "protocol": 1, "source_of_proxy": "-" }, { "id": 97, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T06:16:42.436427+03:00", "host": "81.30.216.147", "port": 41258, "country": "RU", "alive": true, "last_check_time": "2019-02-23T13:52:28.364561+03:00", "busy_until": "2018-10-16T14:47:52.487973+03:00", "proxy_type": 2, "port_response_time": 1.998894, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 131, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T04:57:14.742133+03:00", "host": "178.213.13.136", "port": 53281, "country": "RU", "alive": true, "last_check_time": "2019-02-23T13:52:34.635138+03:00", "busy_until": "2018-10-18T19:34:24.919220+03:00", "proxy_type": 2, "port_response_time": 1.418125, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 146, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T05:54:30.637862+03:00", "host": "46.73.33.253", "port": 8080, "country": "EN", "alive": false, "last_check_time": "2019-02-23T19:17:52.720800+03:00", "busy_until": "2018-10-16T20:09:24.267828+03:00", "proxy_type": 1, "port_response_time": 1.562049, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 174, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T09:54:36.353545+03:00", "host": "85.15.179.5", "port": 8080, "country": "RU", "alive": true, "last_check_time": "2019-02-23T13:52:38.554122+03:00", "busy_until": "2018-10-16T20:09:27.405413+03:00", "proxy_type": 2, "port_response_time": 1.762946, "chargeable": false, "use_counter": 3, "protocol": 1, "source_of_proxy": "-" }, { "id": 190, "created": "2018-09-05T12:57:00.852060+03:00", "modified": "2018-12-19T00:54:42.956378+03:00", "host": "195.208.172.70", "port": 8080, "country": "RU", "alive": true, "last_check_time": "2019-02-23T13:51:10.488620+03:00", "busy_until": "2018-10-17T22:45:44.944348+03:00", "proxy_type": 2, "port_response_time": 0.495875, "chargeable": false, "use_counter": 2, "protocol": 1, "source_of_proxy": "-" } ],
      checkedAlive: true,
      selectedCountry: "",
      checkedType: []
    };
  },
  created() {
    this.loadData();
  },
  methods: {
    loadData() {
      axios
        .get(crosProxy + url, {
          headers: {
            "X-Requested-With": "XMLHttpRequest",
            "Content-Type": "application/json",
            "Access-Control-Allow-Origin": "*",
            Accept: "application/json"
          }
        })
        .then(res => (this.proxies = res.data))
        .catch(e => console.log(e));
    },
    clearFilter() {
      this.checkedAlive = true;
      this.selectedCountry = "";
      this.checkedType = [];
    },
    proxyType(typeId) {
      let typeString = "";
      switch (typeId) {
        case 0:
          typeString = "Transparent";
          break;
        case 1:
          typeString = "Anonymous";
          break;
        case 2:
          typeString = "Elite";
          break;
      }
      return typeString;
    }
  },
  computed: {
    countries() {
      let results = [];
      const countries = this.proxies.map(item => item.country);
      countries.forEach(value => {
        if (results.indexOf(value) === -1) {
          results.push(value);
        }
      });
      return results;
    },
    filterProxies() {
      const country = this.selectedCountry;
      const alive = this.checkedAlive;
      const proxyType = this.checkedType;
      let filterCountry = [],
        filterAlive = [],
        filterType = [];

      // фильтр по типу
      if (proxyType.length) {
        let arrType = proxyType.map(val => {
          return this.proxies.filter(item => {
            return item.proxy_type === val;
          });
        });
        arrType.map(item => {
          item.map(val => filterType.push(val));
        });
      } else {
        filterType = this.proxies;
      }
      // фильтр по стране
      filterCountry =
        filterType.length && country !== ""
          ? filterType.filter(item => item.country === this.selectedCountry)
          : filterType;
      filterAlive = filterCountry.filter(item => item.alive === alive);
      return filterAlive;
    }
  }
};
// https://proxyfordevelopers.com/api/proxies/?format=json
</script>
<style>
.button {
  background: #fff;
  padding: 10px 10px;
  font-weight: 700;
  margin-top: 30px;
  font-size: 12px;
  text-align: center;
  width: 100%;
  border-radius: 5px;
  border: 1px solid #343a40;
  cursor: pointer;
  color: #343a40;
}
.button:hover {
  color: #fff;
  background: #343a40;
}

.form__type,
.form__alive {
  margin-left: 20px;
}

.form__country,
.form__type,
.form__alive {
  margin-bottom: 10px;
}

@media (max-width: 992px) {
  .disable_lg_screen {
    display: none;
  }
}

@media (max-width: 768px) {
  .disable_md_screen {
    display: none;
  }
}

@media (max-width: 576px) {
  .button {
    margin-bottom: 30px;
  }
  .disable_sm_screen {
    display: none;
  }
}

@media (max-width: 320px) {
  .disable_exsm_screen {
    display: none;
  }
}
</style>
