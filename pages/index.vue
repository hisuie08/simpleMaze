<template>
<div>
  <h1>迷路</h1>
  <div id="main-wrapper">
    <Maze/>
    <SidePanel/>
    </div>
</div>
</template>

<script>
import Maze from '@/components/Maze.vue'
import SidePanel from '@/components/SidePanel.vue'
export default {
    components:{Maze,SidePanel},
}
</script>
<style lang="scss">
body{
  padding: 40px;
  #main-wrapper{
  display: flex;
  #maze{margin-right: auto;}
  @media screen and (max-width:calc(400px + 250px + 80px)) {
    display: block;
    #maze{margin-right: 0;}
  }
}
}
</style>