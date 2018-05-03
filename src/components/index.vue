<template>
  
</template>
<script>
export default {
  beforeRouteEnter(to, from, next){
    next({replace: true, name: 'appmain', params: { api: 'catlist?type=portrait_media', page: 1 }})
  }
}
</script>
