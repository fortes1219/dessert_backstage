<template>
  <div class="guide_line">
    <p>{{ msg }}</p>
  </div>
</template>
<script>

export default {
  name:'GuideLine',
  props:{
    msg: String,
  },
  setup(props){
    const msg = props.msg
    return{
      msg
    }
  }
}
</script>