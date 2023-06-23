<template>
  <div class="container-lg sticky-top">
    <div   aria-live="polite" aria-atomic="true" class="d-flex justify-content-center align-items-center w-100">
      <div class="toast" role="alert" aria-live="assertive" aria-atomic="true">
        <div class="toast-header">
          <img src="" class="rounded me-2" alt="" />
          <strong class="me-auto">Wordle</strong>
          <small class ="attempt"></small>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="toast"
            aria-label="Close"
          ></button>
        </div>
        <div class="toast-body"></div>
      </div>
    </div>
    <div class="timer mb-40">
      <h3>Timer</h3>
    </div>
    <div class="row">
      <div class="col-3">
        <input
          :type="typ ? 'text' : 'password'"
          @keyup.enter="handletargetword"
          v-model="targetword"
          autofocus
          autocomplete="off"
          maxlength="4"
          name="targetword"
          class="float-start word"
        />
      </div>
      <div class="col-2">
        <button
          id="showword"
          class="btn btn-outline-primary"
          @click="showtargetword"
        >
          Show word
        </button>
      </div>
      <div class="col-6">
        <input
          type="type"
          @keyup.enter="handlewordguess"
          v-model="wordguess"
          disabled
          autocomplete="off"
          id="guess"
          maxlength="4"
          name="wordguess"
          class="word"
        />
        <div class="row correctletter w-0 p-1"   tabindex="0" @keypress="pressedkey">
          <div class="text-uppercase col border border-4 fs-1">
            {{ correctletter[0] }}
          </div>
          <div class="text-uppercase col border border-4 fs-1">
            {{ correctletter[1] }}
          </div>
          <div class="text-uppercase col border border-4 fs-1">
            {{ correctletter[2] }}
          </div>
          <div class="text-uppercase col border border-4 fs-1">
            {{ correctletter[3] }}
          </div>
        </div>
      </div>
    </div>
    <div :class="wrongposition.length == 0 ? 'd-none' : 'd-block'">
      <p>words in wrong position</p>
      <p>
        <span v-for="wp in wrongposition" :key="wp.key">{{ wp }},</span>
      </p>
    </div>
  </div>
</template>

<script>
import { ref, watch } from "vue";
export default {

  props:['attemptno'],
  setup(props, context) {
    let targetword = ref();
    let wordguess = ref();
    let correctletter = ref(["", "", "", ""]);
    let wrongposition = ref([]);
    let wordguess2 = ref();
    let targetword2 = ref();
    let typ = ref(false);
    let showguess = ref(false);
    let timer = 180;
    let endtime =0;
    let endmin = 0;
    let endsec = 0;
    let min = 0;
    let sec = 0;
    let start = '';


    function showtargetword() {
      typ.value = !typ.value;
    }

    const handletargetword = (e) => {
      if (e.key == "Enter" && targetword.value.length >= 4) {
          if(typ.value == true ){
            typ.value= !typ.value
          }
          document.getElementById("showword").classList.add("disabled");
          document.getElementsByName("targetword")[0].setAttribute("disabled", "true");

          let elementguess = document.getElementsByName("wordguess")[0];
          console.log(elementguess)
          elementguess.removeAttribute('disabled')
          elementguess.focus()

          start = setInterval(() => {
            if (timer===0) {
              clearInterval(start);
              alert("timeout")
            } else {
              timer--;
              sec = Math.trunc(timer % 60);
              min = Math.trunc((timer / 60) % 60);
              endtime = 180 - timer
              console.log(endtime);

              document.getElementsByClassName("timer")[0].innerHTML = min + "Minutes :" + sec + "Seconds";
              console.log(timer)
            }
          }, 1000);
      } else {
        alert("Too short");
      }
    };

    const handlewordguess = (e) => {
      if (e.key == "Enter" && wordguess.value.length >= 4) {
        context.emit("wordguessed", wordguess.value);
        wordguess2 = ref(wordguess.value.split(""));
        targetword2 = ref(targetword.value.split(""));

        if (wordguess.value == targetword.value) {
          clearInterval(start)
          document.getElementsByClassName("toast-body")[0].innerHTML = "You finished in "+ min +" minute "+  sec +" seconds" ;
          document.getElementsByClassName("toast")[0].classList.add("show");
          document.getElementsByClassName("attempt")[0].innerHTML = "finished in "+ props.attemptno +" attempts"
          showtargetword();
        }
        wordguess.value = ''

        for (let i = 0; i < 4; i++) {
          //console.log(wordguess2.value[i])
          if (wordguess2.value[i] == targetword2.value[i]) {
            //console.log(i,targetword.value.split('')[i])

            
            correctletter.value.splice(i, 1, targetword.value.split("")[i]);
          } else if (targetword2.value.includes(wordguess2.value[i])) {
            if (
              wrongposition.value.includes(wordguess2.value[i]) === false &&
              wrongposition.value.includes(correctletter.value[i]) === false
            )
              wrongposition.value.push(wordguess2.value[i]);
          }
        }
      } else {
        alert("Too short");
      }
    };

    return {
      handletargetword,
      handlewordguess,
      wordguess,
      targetword,
      typ,
      showtargetword,
      correctletter,
      wordguess2,
      targetword2,
      wrongposition,
      showguess,
      timer,
      start,
      min,
      sec,
      endtime,
      endmin,
      endsec
    };
  },
};
</script>

<style>
.word {
  background-image: url("http://3.bp.blogspot.com/-4oAWWCcNNz4/Tjr3nKNyVUI/AAAAAAAAPLU/Pouua-pNsEY/s1600/sq.gif");
  width: 240px;
  height: 60px;
  background-size: 60px;
  border: none;
  font-family: monospace;
  font-size: 60px;
  padding-left: 10px;
  letter-spacing: 20px;
  cursor: pointer;
  margin-right: 20px;
}

.correctletter{
  width: 256px;
  
}
</style>