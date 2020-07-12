<template>
  <div class="container">
    <div class="row">
      <div class="col-md-6 col-md-offset-3">
        <h3>Animation ve Transition</h3>
        <hr>

        <transition :name="effectName" appear>
          <!-- Appear eklenince sayfa onLoad olduğu zaman gönderilen animasyonu gerçekleştirir. -->
          <button class="btn btn-primary" @click="show = !show"> Kutuyu göster / Gizle</button>
        </transition>

        <!-- <select class="form-control" v-model="effectName">
          <option value="fade">Fade</option>
          <option value="slide">Slide</option>
        </select>
        <br>

        <br><br>
        <transition :name="effectName">
          <div class="alert alert-success" v-show="!show"> Bu bir alert kutusudur</div>
        </transition>
        <br>
        <transition :name="effectName" type="animation" appear>
          <div class="alert alert-warning" v-show="!show"> Bu bir alert kutusudur</div>
        </transition>
        <hr>
        <transition 
          enter-class=""
          enter-active-class="animate__animated animate__shakeX"
          leave-class=""
          leave-active-class="animate__animated animate__shakeY"
          appear>
          <div class="alert alert-warning" v-show="!show"> Bu bir alert kutusudur</div>
        </transition> -->
        <hr>
        <transition name="fade" mode="in-out">
          <!-- mode, transition sırası belirtir. smooth bir görüntü sağlar. -->
          <div class="alert alert-success" v-if="show" key="success"> Bu bir alert kutusudur</div>
          <div class="alert alert-danger" v-else key="danger"> Bu bir alert kutusudur</div>
          <!-- key'ler divlerin birbirinden farklı etiketler olduğunu belirtmek için var. -->
        </transition>

        <hr>
        <button class='btn btn-primary' @click="showJs = !showJs"> Js Göster</button>
        <br><br>
        <transition
          :css="false"
          @before-enter='beforeEnter'
          @enter='enter'
          @after-enter='afterEnter'
          @after-enter-cancelled='afterEnterCancelled'

          @before-leave='beforeLeave'
          @leave='leave'
          @after-leave='afterLeave'
          @after-leave-cancelled='afterLeaveCancelled'>
          <div style="width:100px;height:100px;background-color:#fbbd08;border:1px solid #666;" v-if="showJs"> JS alert kutusu. </div>
        </transition>

        <hr>
        <h3>Dinamik Componenetler Arası Geçiş</h3>
        <button class="btn btn-danger" @click="activeComp='post'">Post</button>
        <button class="btn btn-primary" @click="activeComp='home'">Home</button>
        <br><br>
        <transition name="slide" mode="out-in">
          <component :is="activeComp"></component>
        </transition>
        <hr>
        <button class="btn btn-danger" @click="addNewItem()"> Yeni Eleman Ekle</button>
        <br><br>
          <transition-group name="slide" tag="ol" class="list-group">
            <li :key='number' class="list-group-item" v-for="(number,index) in numberList" @click="removeItem(index)">Number: {{number}}</li>
          </transition-group>
      </div>
    </div>
  </div>
</template>

<script>
import Post from './components/Post';
import Home from './components/Home';
export default {
  components:{
    Post,
    Home
  },
  data(){
    return{
      show : false,
      effectName : 'fade',
      showJs: false,
      elemntWidth : 100,
      activeComp : 'post',
      numberList : ['1','2','3','4']
    }
  },
  methods:{
    beforeEnter(el){
      console.log('beforeEnter');
      this.elemntWidth = 100;
      el.style.width = this.elemntWidth +'px';
    },
    enter(el,done){
      console.log('Enter');
      let round = 1;
      const interval = setInterval(() => {
        el.style.width = (this.elemntWidth + round *10)+'px';
        round++;
        if(round > 20){
          clearInterval(interval);
          done();
        }
      }, 50);
    },
    afterEnter(el){
      console.log('afterEnter');
    },
    afterEnterCancelled(el){
      console.log('afterEnterCancelled')
    },
    beforeLeave(el){
      console.log('beforeLeave');
      this.elemntWidth = 300;
      el.style.width = this.elemntWidth +'px';
    },
    leave(el,done){
      console.log('Leave')
      let round = 1;
      const interval = setInterval(() => {
        el.style.width = (this.elemntWidth - round *10)+'px';
        round++;
        if(round > 20){
          clearInterval(interval);
          done();
        }
      }, 50);
    },
    afterLeave(el){
      console.log('afterLeave');
    },
    afterLeaveCancelled(el){
      console.log('afterLeaveCancelled')
    },
    addNewItem(){
      const position = Math.floor(Math.random() * this.numberList.length);
      this.numberList.splice(position,0,this.numberList.length+1)
    },
    removeItem(index){
      this.numberList.splice(index,1)
    }
  }

}
</script>

<style>
  /* işlemler
    DOM'a eklenirken
    *-enter = Elementin başlangıçtaki durumu
    *-enter-active = Değişim hali ve son durumu
    DOM'dan çıkartılırken
  *-leave = Çıkarılmaya başlamadan önceki durumu
  *-leave-active = Çıkarılırkenki hali ve son durumu
  
  */
  .fade-enter{
    opacity: 0;
  }
  .fade-enter-active{
    transition: opacity 1s;
  }
  .fade-leave{
    /* opacity: 1; */
  }
  .fade-leave-active{
    transition: opacity 1s;
    opacity: 0;
  }

  .slide-enter{
    opacity: 0;
  }
  .slide-enter-active{
    animation: slide-in 1s ease-out forwards;
    transition: opacity 0.5s;
  }
  .slide-leave{
  }
  .slide-leave-active{
    animation: slide-out 1s ease-out forwards;
    transition: opacity 1s;
    opacity: 0;
    position:absolute; 
    /* position absolute transition-group'ta silinenin altındaki elemanın yukarı aynı anda çıkması için kullanılır. */
  }
  /* move transition-group için kullanılır. */
  .slide-move{
    transition: transform 1s;
  }

  @keyframes slide-in {
    from{
      transform: translateY(20px);
    }
    to{
      transform: translateY(0px);
    }
  }

  @keyframes slide-out {
    from{
      transform: translateY(0px);
    }
    to{
      transform: translateY(20px);
    }
  }
</style>
