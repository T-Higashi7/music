
<template>
　<p>勤怠表入力</p>
      <div class="container mt-3">
        <p>　　　日付：{{this.input_date}} <input type="date" v-model="input_date"></p>
        <p>勤務開始時間：{{this.input_starttime}} <input type="time" v-model="input_starttime"></p>
        <p>勤務終了時間：{{this.input_endtime}} <input type="time" v-model="input_endtime"></p>
        <p>残業：{{this.input_overtime}} 
          <select v-model="input_overtime">
            <option value=0>0</option>
            <option value=0.5>0.5</option>
            <option value=1.0>1.0</option>
            <option value=1.5>1.5</option>
            <option value=2.0>2.0</option>
            <option value=2.5>2.5</option>
            <option value=3.0>3.0</option>
            <option value=3.5>3.5</option>
            <option value=4.0>4.0</option>
          </select>
        </p>
        <p>　　　残業理由：<input type="text" v-model="input_Reason"></p>
        <button v-on:click="join">追加</button>

         <table align="center">
　           <tr>
                <th>日付</th><th>勤務開始時間</th><th>勤務終了時間</th><th>残業</th><th>残業理由</th>
             </tr>
             <tr v-for="(element,index) of meisaiList" key="index" >
                <td>{{element.date}}</td><td>{{element.starttime}}</td><td>{{element.endtime}}</td>
                <td>{{element.overtime}}</td><td>{{element.Reason}}</td>
                  <div v-if="element.show">
                     <button v-on:click="edit(element)" >編集</button>
                     <button v-on:click="deleate(index)" >削除</button>
                  </div>
                  <div v-if="!element.show">
                    <input type="date" v-model="element.editdate">
                    <input type="time" v-model="element.editstarttime">
                    <input type="time" v-model="element.editendtime">
                    <select v-model="element.editovertime">
                      <option value=0>0</option>
                      <option value=0.5>0.5</option>
                      <option value=1.0>1.0</option>
                      <option value=1.5>1.5</option>
                      <option value=2.0>2.0</option>
                      <option value=2.5>2.5</option>
                      <option value=3.0>3.0</option>
                      <option value=3.5>3.5</option>
                      <option value=4.0>4.0</option>
                    </select>
                    <input v-model="element.editReason">
                     <button v-on:click="confirm(element)" >確定</button>
                     <button v-on:click="cancel(element)" >キャンセル</button>
                  </div>
　　         </tr>
         </table>
      </div>

</template>

<script>
export default {

  data () {
    return {

        input_date:null,
        input_starttime:"08:15",
        input_endtime:"16:45",
        input_overtime:0,
        input_Reason:null,

        meisaiList:[
            {date:"2021-11-01",starttime:"8:15",endtime:"17:45",overtime:"0.5",Reason:"電子カルテ対応",editdate:"",editstarttime:"",editendtime:"",editovertime:"",editReason:"",show:true}
        ]
        
    }
  },
  created:function() {
    //入力日付の初期値として今日の日付を設定する。
    this.input_date =this.formatDate(new Date());
  },

  methods:{
    //日付をYYYY-MM-DDにの形に整形する。
    formatDate:function(dt) {
      const y =dt.getFullYear();
      const m =("00" + (dt.getMonth()+1)) .slice(-2);
      const d = ("00" + dt.getDate()) .slice(-2);
      const result =y + "-" + m + "-" + d;
      return result;
    },

    //「追加」ボタンを押した時に勤務明細を追加する。
　　/*「追加」ボタンを押した時にinput_date,input_starttime,input_endtime,input_overtime,input_Reasonのいずれかが
       Nullの場合は「未入力の項目があります。」と警告を表示して勤務明細を追加しない。
    */
    //「追加」ボタンを押した時にstarttimeがendtimeより遅い時間の場合は「時刻が不正です。」と警告を表示する。
    //「追加」ボタンを押した時にinput_dateの日付が、すでにmeisaiListにあって重複する場合は警告を表示して勤務明細を追加しない。
    join: function(){this.meisaiList.push({
       date: this.input_date,
       starttime:this.input_starttime,
       endtime:this.input_endtime,
       overtime:this.input_overtime,
       Reason:this.input_Reason,
       show:true,
       })
       this.input_Reason = null
    
    },

    //「編集」ボタンを押した時に明細を編集する。
    edit: function (selectedelement) {
        selectedelement.editdate=selectedelement.date
        selectedelement.editstarttime=selectedelement.starttime
        selectedelement.editendtime=selectedelement.endtime
        selectedelement.editovertime=selectedelement.overtime
        selectedelement.editReason=selectedelement.Reason
        selectedelement.show=false
  
      },
    //「確定」ボタンを押した時に編集明細を確定する。
    //「確定」ボタンを押した時にstarttimeがendtimeより遅い時間の場合は「時刻が不正です。」と警告を表示する。
    confirm: function (selectedelement) {
        selectedelement.date=selectedelement.editdate
        selectedelement.starttime=selectedelement.editstarttime
        selectedelement.endtime=selectedelement.editendtime
        selectedelement.overtime=selectedelement.editovertime
        selectedelement.Reason=selectedelement.editReason
        selectedelement.show=true
      },
    //「キャンセル」ボタンを押した時に明細の編集をキャンセルする。
    cancel: function (selectedelement) {
        selectedelement.editdate=""
        selectedelement.editstarttime=""
        selectedelement.editendtime=""
        selectedelement.editovertime=""
        selectedelement.editReason=""
        selectedelement.show=true
      },

    //「削除」ボタンを押した時に明細を削除する。
    deleate: function (index) {
        this.meisaiList.splice(index,1)
      }
 },


  computed: {

  

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

td, th{
    border: 1px solid black;
}

th{
  background-color: #EEFFFF;
}


</style>
