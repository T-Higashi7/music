
<template>

<p>検索したい曲名を入力してください。</p>

<div class="text-gray-100">
 <div>
  <input type="text" v-model="term" class="bg-gray-900 text-gray-100">
  <button @click="search">検索</button>
 </div>
   <div class="container mt-3">
    <table v-if="show">
     <tr><th @click="sortBy('trackId')" v-bind:class="addClass('trackId')">トラックID</th><th @click="sortBy('trackName')" v-bind:class="addClass('trackName')">トラック名</th>
     <th @click="sortBy('trackPrice')" v-bind:class="addClass('trackPrice')">トラックの料金</th><th @click="sortBy('collectionId')" v-bind:class="addClass('collectionId')" >アルバムID</th>
     <th @click="sortBy('collectionName')" v-bind:class="addClass('collectionName')">アルバム名</th><th @click="sortBy('collectionPrice')" v-bind:class="addClass('collectionPrice')">アルバムの料金</th>
     <th @click="sortBy('artistName')" v-bind:class="addClass('artistName')">アーティスト名</th><th @click="sortBy('primaryGenreName')" v-bind:class="addClass('primaryGenreName')">ジャンル</th>
     <th @click="sortBy('releaseDate')" v-bind:class="addClass('releaseDate')">リリース日</th></tr>
     <tr v-for = "item in SortSong" v-bind:key="item.trackId">
      <td>{{ item.trackId }}</td><td>{{ item.trackName }}</td><td>{{ "¥"+item.trackPrice }}</td><td>{{ item.collectionId }}</td><td>{{ item.collectionName }}</td>
      <td>{{ "¥"+item.collectionPrice }}</td><td>{{ item.artistName }}</td><td>{{ item.primaryGenreName }}</td><td>{{ date(item.releaseDate) }}</td>
     </tr>
    </table>
   </div>
</div>

</template>

<script>

export default {

  data () {
    return {
      term:"Thriller",
      song: null,
      show: false,
      sort_key: "",
      sort_asc: true
    }
  },


  methods:{
    
    search(){
      console.log(this.term)
      const term = "We Will Rock You"
      const url = "/search"
      const queryParameter = `?term=${this.term}&media=music&entity=musicTrack`
      
      axios.get(url+queryParameter)
          .then((response) => {
            //検索ボタンを押したタイミングでshowプロパティをtrueに切り替え、テーブルを表示するようにする。
            this.show = true
            //検索結果をコンソールに表示する。
            console.log(response.data.results),
            //songプロパティに検索結果を代入。
            this.song =  response.data.results

            //検索ボタンを押したタイミングでリリース日の昇順でソートを行う。
            return this.song.sort((a,b) => {
            return (a.releaseDate < b.releaseDate) ? -1 : (a.releaseDate > b.releaseDate) ? 1 : 0;
      　})
      }) 
    },

　　 //リリース日を"****-**-**"の表記に変更する。
    date(songReleaseDate){
      const words = songReleaseDate.split("T");
      console.log(words[0]);
      return words[0] ;
    },

    //各列のソート切り替えを行う。クリックした列(カラム)を取得し、sort_keyプロパティに代入する。
    sortBy(key) {
      this.sort_key === key? (this.sort_asc = !this.sort_asc): (this.sort_asc = true);
      this.sort_key = key;
    },
    //昇順、降順の矢印を切り替える。
    addClass(key) {
      return {
       asc: this.sort_key === key && this.sort_asc,
       desc: this.sort_key === key && !this.sort_asc,
      }
    }
 },


  computed: {
　　//各列のソート切り替えを行う。クリックした列(カラム)を取得した結果を昇順にソートする。
    SortSong() {
      if (this.sort_key != "") {
        let set = 1;
        this.sort_asc ? (set = 1) : (set = -1);
        this.song.sort((a, b) => {
          if (a[this.sort_key] < b[this.sort_key]) return -1 * set;
          if (a[this.sort_key] > b[this.sort_key]) return 1 * set;
          return 0;
        });
        return this.song;
      } else {
        return this.song;
      }
    }
  }
  

}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

table, td, th{
    border: 1px solid black;
}

th{
  background-color: #EEFFFF;
}

.asc::after {
  content: "↓";
}
.desc::after {
  content: "↑";
}

</style>
