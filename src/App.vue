<template>
  <div id="app" class="container-md">
    <header>
      <h1>SPA</h1>
    </header>
    <main class="container">
      <form @submit="(e) => {e.preventDefault(); searchPackages(searchString);}">
        <b-form-input v-model="searchString" placeholder="Введите запрос"></b-form-input>
      </form>

      <table class="table table-hover" v-if="packs">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Name</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in displayedPacks"
            :key="index + 10*(+page - 1)"
            v-on:click="() => current = item.package">

            <th scope="row">{{index + 10*(+page - 1)}}</th>
            <td>{{item.package.name}}</td>
          </tr>
        </tbody>
      </table>
      <div v-else class="mt-3">
        Введите название в поисковой строке и нажмите "Ввод" для отображения таблицы
      </div>

      <nav aria-label="Page navigation example" v-if="pages">
        <ul class="pagination">
          <li class="page-item">
            <button type="button" class="page-link" v-if="page != 1" @click="page--"> Previous </button>
          </li>
          <li class="page-item">
            <button type="button" class="page-link" :key="index" v-for="(pageNumber, index) in pages" v-on:click="page = pageNumber"> {{pageNumber}} </button>
          </li>
          <li class="page-item">
            <button type="button" @click="page++" v-if="page < pages.length" class="page-link"> Next </button>
          </li>
        </ul>
      </nav>

      <div class="modal-window" v-if="current"
        v-on:click="() => current = null">

        <b>name</b>: {{current.name}}<br />
        <b>version</b>: {{current.version}}<br />
        <b>description</b>: {{current.description}}
      </div>
    </main>

    <footer class="d-flex flex-wrap justify-content-between align-items-center py-3 my-4 border-top">
      <div class="col-md-4 d-flex align-items-center">
        <span class="text-muted">Хаятова Айсылу</span>
      </div>
      <a href="https://github.com/belladiluna1">
        <svg xmlns="http://www.w3.org/2000/svg" width="25" height="25" fill="currentColor" class="bi bi-github" viewBox="0 0 16 16">
          <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.012 8.012 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
        </svg>
      </a>
    </footer>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        packs: null,
        searchString: null,
        current: null,
        endpoint: 'https://registry.npmjs.org/-/v1/search',
        page: 1,
        perPage: 10,
        pages: [],
      }
    },

    created() {
      this.getAllPacks();
    },

    methods: {
      searchPackages(string) {
        fetch(`${this.endpoint}?text="${string}"`)
          .then(response => response.json())
          .then(result => {
            this.pages = [];
            this.packs = result.objects;
          })
          .catch(error => {
            console.log('-----error-------');
            console.log(error);
          })
      },

      getAllPacks() {
        fetch(this.endpoint)
          .then(response => response.json())
          .then(result => {
            this.packs = result;
          })
          .catch(error => {
            console.log('-----error-------');
            console.log(error);
          })
      },

      setPages () {
        let numberOfPages = Math.ceil(this.packs.length / this.perPage);
        console.log(numberOfPages);
        for (let index = 1; index <= numberOfPages; index++) {
          this.pages.push(index);
        }
      },

      paginate (packs) {
        let page = this.page;
        let perPage = this.perPage;
        let from = (page * perPage) - perPage;
        let to = (page * perPage);
        return  packs.slice(from, to);
      }
    },
    computed: {
      displayedPacks () {
        return this.paginate(this.packs);
      }
    },
    watch: {
      packs () {
        this.setPages();
      }
    }
  }
</script>

<style lang="scss">

  body {
    margin: 0;
    padding: 0;
  }
  #app {
    color: #2c3e50;
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  footer.my-4{
    margin-top: auto !important;
  }

  .modal-window {
    position: fixed;
    top: 50%;
    left: 50%;
    background-color: #fff;
    border: 1px solid #eee;
    padding: 40px;
    transform: translate(-50%, -50%);
  }

  button.page-link {
    display: inline-block;
  }
  .offset{
    width: 500px !important;
    margin: 20px auto;  
  }
  
</style>
