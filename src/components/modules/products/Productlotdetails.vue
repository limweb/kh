<template>
  <div class="content-wrapper">

    <!-- Content Header (Page header) -->
    <section class="content-header">
      <h1>วันที่: {{lot.date}} / รายละเอียด Lot ที่: <small>{{$route.params.lotid}}</small> </h1>
      <ol class="breadcrumb">
        <li><router-link  :to="'/home'" class="button" ><i class="fa fa-dashboard"></i> Home</router-link></li>
        <li><router-link  :to="'/rcproduct'" class="button" > Product lots</router-link></li>
        <li class="active">lotdetails</li>
      </ol>
    </section>

    <!-- Main content -->
    <section style="min-height:450px;" class="content print">
       <div v-if="chklot">
          <div class="box box-widget ">
            <div class="box-header with-border noprint">
              <div class="user-block noprint" style="margin-right: 5px;">
                <h3 class="box-title noprint">รายละเอียด การรับสินค้า  lotid : {{lot.id}} / {{ lot.lot_name }}</h3>
                <button type="button" style="width:90px;float:right;" class="btn btn-primary noprint" @click="addbill">Add Bill</button>
                <button type="button" style="width:90px;float:right;margin-right:10px;" class="btn btn-primary noprint" @click="print">Print</button>
              </div>
            </div>


            <!-- /.box-header -->
            <div class="box-body print">
              <table class="table table-bordered table-striped ">
                <thead>
                <tr>
                  <th colspan="4" style="text-align:center;" >
                    <span>รายละเอียด การรับสินค้า {{lot.id}} / {{ lot.lot_name }} Total: {{Number(lot.total).toLocaleString('th-TH', {minimumFractionDigits: 2})}} <b>฿</b> จำนวนบิล{{billength}} ใบ </span>
                  </th>
                </tr>
                <tr style="background-color: #ea5b0b;" >
                  <th style="width:160px">รหัส</th>
                  <th>แยกตามชนิดผลไม้</th>
                  <th style="text-align:right;width:160px"> จำนวน (kg) </th>
                  <th style="text-align:right;width:160px"> เฉลี่ยน/Kg </th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(sumaryrow,index) in lot.summary">
                  <td>{{sumaryrow.product_code}}</td>
                  <td>{{sumaryrow.name}}</td>
                  <td align="right">{{Number(sumaryrow.qty).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                  <td align="right">{{Number(sumaryrow.avg).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                </tr>    
                </tbody>
               </table>
               <br />
            <!-- /.box-body -->
              <table class="table table-bordered table-striped">
                <thead>
                <tr style="background-color: aquamarine;">
                  <th>แยกตาม ประเภทของผลไม้</th>
                  <th style="text-align:right;width:160px"> จำนวน (kg) </th>
                  <th style="text-align:right;width:160px"> เฉลี่ยน/Kg </th>
                </tr>
                </thead>
                <tbody>
                 <tr v-for="sumcat in lot.sumbycat" >
                    <td >{{sumcat.name}}</td>
                    <td style="text-align:right;width:160px">{{Number(sumcat.qty).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                    <td style="text-align:right;width:160px">{{Number(sumcat.avg).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                  </tr>
                </tbody>
              </table>
              <br/>
              <table class="table table-bordered table-striped">
                <thead>
                <tr style="background-color: #ea5b0b;">
                  <th style="width: 20px;">No.</th>
                  <th style="width: 80px;">Bill No.</th>
                  <th>ผู้ขาย</th>
                  <th style="text-align:right;width: 160px;"> จำนวน (kg) </th>
                  <th style="text-align:right;width: 160px;"> จำนวนเงิน (บาท) </th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(detail,index) in lot.bills">
                  <td style="width: 20px;x">{{index+1}}</td>
                  <td style="width: 60px;">{{detail.id}}</td>
                  <td>{{detail.name}}</td>
                  <td style="text-align:right;"align="right">{{Number(detail.qty).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                  <td style="text-align:right;"align="right">{{Number(detail.total).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                </tr>                
                </tbody>
                <br />
                <tfoot>
                <tr style="background-color: #707bea;color: aliceblue;">
                  <td colspan="3"><b>เฉลี่ย รวม </b></td>
                  <th  style="text-align:right">{{Number(lot.sumx[0].qty).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</th>
                  <th  style="text-align:right">{{Number(lot.sumx[0].avg).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</th>
                </tr>
                </tfoot>
              </table>
            <!-- /.box-body -->
            </div>
          </div>    
          <div class="box box-widget noprint" v-for="(detail,index) in lot.bills">
            <div class="box-header with-border">
              <div class="user-block">
                <span class="username">No:{{index+1}} บิลเลขที่ :{{detail.id}} </span>
                <span class="description">Name :{{detail.name}}</span>
              </div>
              <div class="box-tools">

                 <button @click="editbill(index,detail)" class="btn btn-warning btn-xs" title="edit bill" >
                   <span class="glyphicon glyphicon-edit"></span>
                 </button>                 
                 <button @click="removebill(index,detail.id)" class="btn btn-danger btn-xs" title="Remove Bill">
                   <span class="glyphicon glyphicon-trash"></span>
                 </button>

                <button type="button" class="btn btn-box-tool" data-widget="collapse">
                  <i class="fa fa-minus"></i>
                </button>
              </div>
            </div>
            <div class="box-body">
              <table class="table table-bordered table-striped">
                <thead>
                <tr>
                  <th>Id</th>
                  <th>ผลไม้</th>
                  <th>จำนวน (kg)</th>
                  <th>ราคา รับซื้อ</th>
                  <th>จำนวนเงิน</th>
                </tr>
                </thead>
                <tbody>
                
                <tr v-for="(row,index) in detail.billdetails">
                  <td>{{index+1}}</td>
                  <td>{{row.name}}</td>
                  <td style="text-align:right;">{{Number(row.qty).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                  <td style="text-align:right;">{{Number(row.price).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                  <td style="text-align:right;">{{(row.qty*row.price).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</td>
                </tr>                
                </tbody>
                <tfoot>
                <tr style="background-color: #237530;color: aliceblue;">
                  <th>Status </th>
                  <th>&nbsp;</th>
                  <th>{{Number(detail.qty).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</th>
                  <th>&nbsp;</th>
                  <th>{{Number(detail.total).toLocaleString('th-TH', {minimumFractionDigits: 2})}}</th>
                </tr>
                </tfoot>
              </table>
            </div>
            <div class="box-footer">
              &nbsp;
            </div>
          </div>          
       </div>
       <div v-else="chklot"><h2>ยังไม่รายการสำหรับ Lot นี้ </h2></div>
    </section>
    <!-- /.content -->
  </div>
</template>

<script>
import { mapState, mapActions  } from 'vuex'
import bootbox from 'bootbox'


  export default {
    name: 'productdetails',
    mounted () {
      this.lotbyname(this.$route.params.lotid)
      this.$store.state.currentlot = this.$route.params.lotid;
      console.log('mount',this.$store.state.currentlot);
    },
    methods:{
      ...mapActions(['lotbyname','delbillbyid','createnewbill']),
      print(){
        window.print();
      },
      addbill(){
        let nb = JSON.parse(JSON.stringify(this.$store.state.app.newbill))
        nb.lotid= this.lot.id;
        nb.lot_name = this.lot.lot_name;
        nb.cate = this.lot.cate;
        nb.action = 'NEW';
        nb.save = false;
        nb.isNew = true;
        console.log('addbile in lot',nb,this.lot)
        this.createnewbill(nb)
        this.$router.push("/addrcproduct")
      },
      removebill(index,billid){
        console.log('index=',index,'billid=',billid)
          bootbox.confirm({ message: "ต้องการที่จะลบ บิลรายการนี้หรือไม่ ?!"  ,size: 'small',callback: (result)=>{ 
            if(result){
               console.log('remove bill',billid,result)
               // this.lot.bills.splice(index,1)
               this.delbillbyid(billid);
            }
          }
        });
      },
      editbill(index,bill){
        console.log('editbill--',bill,this.lot)
        let nb = JSON.parse(JSON.stringify(bill))
        nb.action = 'edit';
        nb.save = false;
        nb.isNew = true;
        nb.lot_id= this.lot.id;
        nb.cate = this.lot.cate;
        nb.lot_name = this.lot.lot_name;
        nb.date = bill.created_date;
        this.createnewbill(nb)
        this.$router.push("/addrcproduct")
      },
    },
    computed: {
      ...mapState(['lot']),
      billength() {
        if( this.lot.bills.length != undefined){ 
          return  this.lot.bills.length  
        } else { 
          return 1
        }
      },
      chklot(){
        if(this.lot != undefined ) {
          return 1
        } else {
          return 0
        }
      },
      scrheight(){
        if( window.screen.height ) {
          return window.screen.height
        } else {
          return 450;
        }
      }
    }
  }
</script> 

<style scope>
  * {
    font-size:15px !important;
   }

  .content {
    min-height:450px;
    height: 100%!important;
  }

@page{
  margin: 30 auto;
}

@media print {

  * {
     font-size:14px!important; 
      height: auto;
    }

    .noprint {
       display: none;
    }
    
 .print {
    margin: 30 auto;
    display: block; 
    overflow: hidden;
    height: auto;
  }

  .content-wrapper {
    height: 100%!important; 
    overflow: hidden;
  }

  .wrapper {
    overflow: hidden;
  }

 .content {
   height: 100%!important; 
   overflow: hidden;
   height: auto;
 }
  
}
</style>