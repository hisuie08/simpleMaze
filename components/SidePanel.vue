<template>
  <div id="sidePanel">
    <div class="controllBox">
      <input id="touchmode" type="checkbox" /><span>タッチモード</span>
    </div>
    <table id="mazeButtons" class="controllBox">
      <tr>
        <td></td>
        <td>
          <button id="bup" class="moveButton">
            <chevron-up />
          </button>
        </td>
        <td></td>
      </tr>
      <tr>
        <td>
          <button id="bleft" class="moveButton">
            <chevron-left />
          </button>
        </td>
        <td>
          <button id="bdown" class="moveButton">
            <chevron-down />
          </button>
        </td>
        <td>
          <button id="bright" class="moveButton">
            <chevron-right />
          </button>
        </td>
      </tr>
    </table>

    <div id="ctrl">
      <div class="sizeSlider controllBox">
        <span>サイズ調整</span><br />
        <input
          id="size"
          type="range"
          min="10"
          max="100"
          step="10"
          value="10"
        /><span id="current-value"></span>
      </div>
      <div class="controllBox">
        <button id="renew" class="ctrlUnit">新規ゲーム</button>
        <button id="reset" class="ctrlUnit">やりなおす</button>
      </div>
      <div class="controllBox">
        <input id="trace" type="checkbox" checked="true" /><span>
          移動した場所の色を変化</span
        >
      </div>
      <div id="indicator">
        <label id="stepCount">移動数: 000</label>
        <label id="stopWatch">00:00.000</label>
      </div>
    </div>
    <v-btn id="shareTwitter">Twitterでシェア</v-btn>
    <section id="howtoplay">
      <h5>遊び方</h5>
        <p><span>W</span> / 矢印上キー : 上に移動</p>
        <p><span>S</span> / 矢印下キー : 下に移動</p>
        <p><span>A</span> / 矢印左キー : 左に移動</p>
        <p><span>D</span> / 矢印右キー : 右に移動</p>
        <p>スライダー: 迷路サイズ調整</p>
        <p>迷路サイズは最小10,最大100</p>
    </section>
  </div>
</template>

<script>
import ChevronUp from "vue-material-design-icons/ChevronUp.vue";
import ChevronDown from "vue-material-design-icons/ChevronDown.vue";
import ChevronLeft from "vue-material-design-icons/ChevronLeft.vue";
import ChevronRight from "vue-material-design-icons/ChevronRight.vue";
function toggleTouch(e) {
  console.log({ e });
  const btn = document.getElementById("mazeButtons");
  if (e?.target.checked) {
    btn.classList.remove("hide");
  } else {
    btn.classList.add("hide");
  }
}

export default {
  components: { ChevronUp, ChevronDown, ChevronLeft, ChevronRight },
  mounted() {
    document.getElementById("mazeButtons").classList.add("hide");
    document
      .getElementById("touchmode")
      .addEventListener("change", toggleTouch);
  }
};
</script>
<style lang="scss" scoped>

#sidePanel {
  border: solid 8px;
  padding: 6px;
  border-color: #505050;
  text-align: center;
  display: inline-block;
  min-width: 250px;
  width: calc(100vw * 0.2);
  .hide {
  display: none !important;
}
.controllBox {
  display: block;
}
  #mazeButtons {
    padding: 5px;
    display: inline-block;
    text-align: center;
    .moveButton {
      width: calc(200px / 3);
      height: calc(200px / 5);
      background-color: rgb(212, 212, 212);
      * {
        pointer-events: none;
      }
    }
  }
}  .sideSlider {
      display: inline;
    }
    #sizeSlider {
      #current-value {
        color: white;
      }
    }
    button {
      margin-top: 6px;
      margin-right: 6px;
      padding: 4px;
      display: inline-block;
      background-color: rgb(58, 58, 58);
      border-radius: 10%;
    }
    #indicator {
      display: block;
      #stepCount {
        margin-top: 6px;
        display: inline-block;
        margin-bottom: 6px;
        font-size: 20px;
        padding: 6px;
        border: solid 3px;
        border-color: rgb(114, 114, 114);
        background-color: rgb(59, 59, 59);
      }
      #stopWatch {
        margin-top: 6px;
        display: inline-block;
        margin-bottom: 6px;
        font-size: 20px;
        padding: 6px;
        border: solid 3px;
        border-color: rgb(114, 114, 114);
        background-color: rgb(59, 59, 59);
      }
    }
  #shareTwitter {
    padding: 5px;
    color: white;
    font-size: 25px;
    text-transform: none;
    margin: 2px;
    background-color: #1da1f2;
  }

</style>
