<template>
  <div>
    <h1>
      <span v-for="(letter, index) in title" :key="index" class="letter">{{ letter }}</span>
    </h1>
    <h2>{{ message }}</h2>
    <h3> App by Fabio. Take a look at my
      <a href="https://github.com/fabio-mancin/" title="GitHub Link"> GitHub </a> or my
      <a href="https://fabiomancin.dev" title="Personal Website"> website!</a>
    </h3>
  </div>

</template>

<script>
import '@/assets/styles/ShowMessage.scss'
export default {
  name: 'ShowMessage',
  props: ['message', 'title']
}
</script>
