<template>
  <div v-if="dataIsReturn" class="HomePage">
    <!-- @ the name we gave in emit with the new array -->
    <SearchComponent @searchValue="this.searchVal" @returnList="this.searchRecipe" :recipesList="this.apiList"/>

    <!-- show all the results from the api if the check theres no slot in localResult -->
    
    <CardGalleryComponent :dishList="apiList" v-if="!this.checkSearch()"/> 
    <!-- if theres result show it -->
    <CardGalleryComponent :dishList="this.localResult" v-else/>

    <section class="pageBtn">
      <button @click="pageSelector('prev')">Prev</button>
      <button @click="pageSelector('next')">Next</button>
    </section>
  </div>
  <div v-else><h1>Loading...</h1></div>
</template>


<script>
import CardGalleryComponent from './CardGalleryComponent.vue';
import SearchComponent from './SearchComponent.vue';



export default {
  name: 'HomePage',
  components: {
    CardGalleryComponent,
    SearchComponent,
  },

  data(){
    return {
      apiList:[],
      dishesApi: "http://localhost/FinalProject/api/rest/api/V1/recipeApi.php",
      localResult: [], // we will put the array compared in this one.
      searchValue: "&q=",
      pageNumber: 0,
      dataIsReturn: true
    }
  },

  methods: {
    async getRecipeList() {
      let url = `https://tasty.p.rapidapi.com/recipes/list?from=${this.pageNumber}&size=10`;
      const options = {
        method: 'GET',
        headers: {
          'X-RapidAPI-Key': '5797d19803mshd474a7ef8d65541p1b0cacjsn91d219ec7fd1',
          'X-RapidAPI-Host': 'tasty.p.rapidapi.com'
        }
      };

      if(this.searchValue != "&q="){
        url += this.searchValue;
      }

      try {
        const response = await fetch(url, options);
        const result = await response.text();
        const recipeList = JSON.parse(result);
        this.apiList = recipeList.results;
        console.log(url);
        this.dataIsReturn = true;
      } catch (error) {
        console.error(error);
      }
    },

    // put the event in the localResult array
    searchRecipe(e) {
      this.localResult = e;
    },

    searchVal(e) {
      this.searchValue = e;
      this.pageNumber = 0;
      this.getRecipeList();
    },

    pageSelector(selector){
      if(selector == "prev" && this.pageNumber>0){
        this.pageNumber = this.pageNumber - 10;
        this.getRecipeList();
      }
      else if(selector == "next"){
        this.pageNumber = this.pageNumber + 10;
        this.getRecipeList();
      }
    },

    // check if theres any slots in the array
    checkSearch() {
      if (this.localResult.length > 0) {
        return true;
      } else {
        return false;
      }
    }

  },
    created() {
      this.getRecipeList();
    }
}
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Comfortaa:wght@300;400;500;600;700&display=swap');

body {
  font-family: 'Comfortaa', sans-serif;
}

* {
    margin: 0;
    padding: 0;
}


/* mobile version */
@media (min-width: 300px) {
  
  .HomePage{
    /* width: 90%;
    height: 100%; */
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .search-bar{
    display: flex;
    justify-content: center;
    align-items: center;
    padding-bottom: 5vh;
  }

  .search-bar > form{
    display: flex;
    align-items: center;
    border: 1px solid gainsboro;
    padding: 1vh;
    border-radius: 10px;
  }

  .search{
    width: 35vh;
    border-radius: 5px;
    border: unset;
    padding: 5px;
  }

  .search:focus{
    box-shadow: 0 0 0 0;
    outline: 0;
  }

  .fa-magnifying-glass{
    color: lightgray;
  }

  .search::placeholder{
    text-align: center;
  }


  .searchBtn{
    background-color: transparent;
    border: none;
    font-size: 20px;
    transition: .5s;
  }

  .pageBtn{
    display: flex;
    column-gap: 10vw;
  }

  .pageBtn > button{
    padding: 1vh;
    color: whitesmoke;
    background-color: #316318;
    border: 3px solid #316318;
    border-radius: 10px;
    font-size: 16px;
    transition: .5s;
  }

  .dishGallery ul {
    list-style: none;
    display: flex;
    flex-direction: column;
    row-gap: 5vh;
  }

  .dishGallery a {
    color: dimgrey;
    text-decoration: none;
  }

  .cardFigure{
    width: 35vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    border-radius: 10px;
    box-shadow: 3px -2px 10px 4px gainsboro;

  }

  .cardFigure img{
    position: cover;
    border-radius: 10px;
  }

  .cardFigcaption{
    border: 1px solid ghostwhite;
    padding: 2vh;
    border-radius: 10px;
  }

  .card-info {
    width: 30vh;
    display: flex;
    justify-content: center;
    column-gap: 2vh;
  }

  
}

@media (min-width: 600px){
  .dishGallery{
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .dishGallery ul {
    width: 90%;
    justify-content: center;
    list-style: none;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    column-gap: 20px;
  }

  .dishGallery li{
    height: 50%;
  }

  .cardFigure:hover {
    transform: scale(1.1);
  }

  .search-bar > form{
    column-gap: 1vw;
  }

  .searchBtn:hover{
    cursor: pointer;
    scale: 1.1;
  }

  .pageBtn > button:hover{
    background-color: transparent;
    color: #316318;
    cursor: pointer;
    scale: 1.1;
  }

  .cardFigcaption{
    height: 10vh;
  }
}

 .cardFigure img {
  width: 100%;
  height: 40vh;
}

.dishGallery {
  display: flex;
  flex-direction: column;
  align-items: center;
  row-gap: 4vh;
}

.pageBtn{
  margin-top: 5vh;
}
</style>
