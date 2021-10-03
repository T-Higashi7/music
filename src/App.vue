
<template>

<p>検索したい曲名を入力してください。</p>

<div class="text-gray-100">
 <div>
  <input type="text" v-model="term" class="bg-gray-900 text-gray-100">
  <button @click="search">検索</button>
 </div>
 <div class="mt-3" v-if="show">
   <button type="button" class="ms-1 btn btn-primary">トラックID</button>
   <button type="button" class="ms-1 btn btn-primary">トラック名</button>
   <button type="button" class="ms-1 btn btn-primary">トラックの料金</button>
   <button type="button" class="ms-1 btn btn-primary">アルバムID</button>
   <button type="button" class="ms-1 btn btn-primary">アルバム名</button>
   <button type="button" class="ms-1 btn btn-primary">アルバムの料金</button>
   <button type="button" class="ms-1 btn btn-primary">アーティスト名</button>
   <button @click="filter" type="button" class="ms-1 btn btn-primary">ジャンル</button>
   <button type="button" class="ms-1 btn btn-primary">リリース日</button>

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
      //検索ボックスの初期値を指定する。v-modelで検索ボックスのテキストと連動。
      term:"Thriller",
      //検索結果
      song: null,
      //tureの場合は、検索結果(テーブル)を表示し、falseの場合は検索結果(テーブル)を表示しない。
      show: false,
　　　//クリックしたカラム名がsort_keyに代入され昇順か降順かを判断します。
      sort_key: "",
      //trueの場合は昇順になり、falseの場合は降順になる。
      sort_asc: true,
      //ジャンルボタンをクリックした時に重複を削除した検索結果を代入する。
      primaryGenreName_filter: ""
    }
  },


  methods:{
    
    //serchは検索ボタンをクリックした後に実行される関数になります。
    search(){
      //検索ボックスの入力値をコンソールログに出力。
      console.log(this.term)
      //URLの前半部分をurlに代入。
      const url = "/search"
      //URLの後半部分をqueryParameterに代入。
      const queryParameter = `?term=${this.term}&media=music&entity=musicTrack`
      //URLをgetで取得する。
      axios.get(url+queryParameter)
          .then((response) => {
            //検索ボタンを押したタイミングでshowプロパティをtrueに切り替え、テーブルを表示するようにする。
            this.show = true
            //検索結果をコンソールに表示する。
            console.log(response),
            console.log(response.data.results),
            //songプロパティに検索結果を代入。
            this.song =  response.data.results

            //検索ボタンを押したタイミングでリリース日の昇順でソートを行う。
            return this.song.sort((a,b) => {
            //「a」より「b」のほうが大きければ、-1を返し「a」は「b」の後ろに。bよりaのほうが大きい場合は1を返し、「a」は「b」の前に。
            // 大きさが同じ場合は0を返し、「a」は「b]の前になる。
            return (a.releaseDate < b.releaseDate) ? -1 : (a.releaseDate > b.releaseDate) ? 1 : 0;
      　})
      }) 
    },
    
    filter(item){
     console.log(item.primaryGenreName)
     this.primaryGenreName_filter =  c.primaryGenreName
    },
    

　　 //dateはリリース日(releaseDate)を"****-**-**"の表記に変更する関数になります。
　　 //引数に指定しているsongReleaseDateがreleaseDateとなります。
    date(songReleaseDate){
      //songReleaseDateを"T"の文字で区切って配列に変換してwordsに代入する。
      const words = songReleaseDate.split("T");
      console.log(words[0]);
　　　//配列(words)の0番目のデータを返す。
      return words[0] ;
    },

    //初めてカラムをクリックした時は、sort_keyにカラム名を代入しsort_ascにtrueを代入してクリックしたカラムを昇順に切り替える。
    //2回目に1回目と同じカラムをクリックした時は、sort_keyにカラム名を代入しsort_ascをfalseに切り替え、クリックしたカラムを降順に切り替える。
    sortBy(key) {
      this.sort_key === key? (this.sort_asc = !this.sort_asc): (this.sort_asc = true);
      //sort_keyにクリックしたカラム名を代入する。
      this.sort_key = key;
    },
    //昇順、降順の矢印を切り替える。
    addClass(key) {
      return {
        //sort_keyがクリックしたカラム名と同じかつ、this.sort_ascがtrueの場合は、昇順の矢印を表示する。
       asc: this.sort_key === key && this.sort_asc,
       //sort_keyがクリックしたカラム名と同じかつ、this.sort_ascがfalseの場合は、降順の矢印を表示する。
       desc: this.sort_key === key && !this.sort_asc,
      }
    }
 },


  computed: {
　　//各列のソート切り替えを行う。クリックした列(カラム)を取得した結果を昇順にソートする。
    SortSong() {
      //もしsort_keyが空白ではなかった場合(列名をクリックして値が入っている場合)、
      if (this.sort_key != "") {
        //setに1を代入する。
        let set = 1;
        //sort_ascがtrue(昇順)の場合はsetに1を代入し、false(降順)の場合は、setに-1を代入する。
        this.sort_asc ? (set = 1) : (set = -1);
        //もし、aよりbのほうが大きい場合、setが1(sort_ascがtrueで昇順)だと返り値は「-1*1=-1」になるのでaはbの後ろにソートされる。
        //もし、bよりaのほうが大きい場合、setが1(sort_ascがtrueで昇順)だと返り値は「 1*1=-1」になるのでaはbの前にソートされる。
        this.song.sort((a, b) => {
          if (a[this.sort_key] < b[this.sort_key]) return -1 * set;
          if (a[this.sort_key] > b[this.sort_key]) return 1 * set;
        //ソートをした後に返り値を初期値0に戻す。
          return 0;
        });
        //ソートをした検索結果を返す。
        return this.song;
        //もしsort_keyが空白だった場合、
      } else {
        //ソートをしていない検索結果を返す。
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
