<template>
  <div id="app">
    <div class="container">
      <v-wait message='Something loading! Lovely...'>
        <template slot='waiting'>
          <span class="animated">❤️</span>
        </template>
        This will be shown after load.
      </v-wait>
    </div>
    <button @click='writeCode()' :disable='$l.is("writing code")'>
      <v-wait for='writing code' message='Writing Code...'>
        <template slot='waiting'>
          <span class="animated">️️♻️</span>
        </template>
        Write Code
      </v-wait>
    </button>
    <button @click='end()' :disabled='!$l.is("writing code")'>
      <span v-if='$l.is("writing code")'>Stop Coding</span>
      <span v-else>Coding Stopped</span>
    </button>
    <br>
    <button @click='$l.start("c")' :disabled='$l.is("c")'>
      Start <b>"c"</b> Loader
    </button>
    <button @click='$l.end("c")' :disabled='!$l.is("c")'>
      Stop <b>"c"</b> Loader
    </button>
    <p>
      Toggle on any letter to load/unload together.
    </p>
    <ul class="list">
      <li v-for='loader in loaders' :key='loader + Math.random()' @click='toggleLoader(loader)'>
        <v-wait :for='loader'>
          <template slot='waiting' v-if='loader == "c"'>
            <span class="animated">🎉</span>
          </template>
          <template slot='waiting' v-else>
            <span class="animated">👋🏻</span>
          </template>
          {{ loader }}
        </v-wait>
      </li>
    </ul>
    <hr>
    <button @click='incrementAsync'>
      <v-wait for="incrementing" message='incrementing...'>
        <template slot='waiting'>
          <span class="animated">+</span>
        </template>
        <code>mapWaitingActions</code> {{ count }}
      </v-wait>
    </button>
    <span v-if='isIncrementing'>is incrementing...</span>
    <hr>
    Percentage of <code>writing code</code>: <span>{{ $l.percent('writing code') }}</span>
    <p>
      <label>Native Progress</label><br>
      <progress min="0" max="100" :value='$l.percent("writing code")'></progress>
    </p>
    <p>
      <label>Custom Progress</label><br>
      <span class="progress">
        <span class="percent" :style="`width: ${$l.percent('writing code')}%`"></span>
      </span>
    </p>
    <button @click='writeCode()'>
      Start <code>writing code</code>
    </button>
    <button v-wait:click.progress="['writing code', 10]">Set progress to 10</button>
    <button v-wait:click.progress="['writing code', 50]">Set progress to 50</button>
    <button v-wait:click.progress="['writing code', 100, 100]">Set progress to 100 of 100</button>
    <button v-wait:click.progress="['writing code', 101]">Set progress to 101 (complete)</button>
    <button v-wait:click.progress="['writing code', 50, 200]">Set progress to 50 of 200 (25%)</button>
  </div>
</template>

<script>
  import { mapGetters } from 'vuex';
  import { mapWaitingActions, mapWaitingGetters } from '../../src/vue-wait';

  export default {
    name: 'vuex-app',
    data() {
      return {
        loaders: ['a', 'c', 'b', 'a', 'b', 'a', 'c', 'a', 'c', 'a', 'b']
      };
    },
    watch: {
      isWritingCode() {
        if (!this.isWritingCode) clearInterval(this.timer);
      }
    },
    computed: {
      ...mapGetters(['count']),
      ...mapWaitingGetters({
        isIncrementing: 'incrementing count',
        isWritingCode: 'writing code',
      }),
    },
    methods: {
      ...mapWaitingActions({
        incrementAsync: 'incrementing count',
      }),
      writeCode() {
        this.$l.start('writing code');
        let i = this.$l.percent('writing code');
        this.timer = setInterval(() => {
          this.$l.progress('writing code', i++);
        }, 100);
      },
      end() {
        this.$l.end('writing code');
        clearInterval(this.timer);
      },
      toggleLoader(loader) {
        if (this.$l.is(loader)) {
          this.$l.end(loader);
        } else {
          this.$l.start(loader);
        }
      }
    }
  }
</script>

<style>
  body {
    font-family: Arial, Helvetica, sans-serif;
    font-size: 20px;
  }

  #app {
    margin: 50px auto;
    width: 500px;
    text-align: center;
  }

  .list {
    list-style: none;
    padding: 0;
  }

  .list li {
    display: inline-block;
    margin: 10px;
    width: 30px;
    height: 30px;
    border-radius: 3px;
    border: 2px solid #ccc;
    line-height: 30px;
  }

  .container {
    padding: 50px;
  }

  .progress {
    width: 400px;
    height: 40px;
    background-color: #ccc;
    border: 1px solid #999;
    margin: 0 auto;
    display: inline-block;
    position: relative;
  }

  .percent {
    position: absolute;
    top: 0;
    left: 0;
    height: 40px;
    background-color: blue;
    transition-duration: 600ms;
  }

  button {
    border: 0;
    background-color: #fff;
    border: 2px solid #9f0fa0;
    border-radius: 4px;
    margin: 5px;
    color: #9f0fa0;
    font-size: 16px;
    padding: 8px;
  }

  button[disabled] {
    opacity: 0.4;
  }

  .animated {
    text-align: center;
    opacity: 0.5;
    animation: pulse 1s infinite;
    animation-delay: 1s;
    display: inline-block;
  }

  @-webkit-keyframes pulse {
    0%,
    100% {
      opacity: 1;
      transform: scale(1.3);
    }
    50% {
      opacity: 0.1;
      transform: scale(1);
    }
  }

  @keyframes pulse {
    0%,
    100% {
      opacity: 1;
      transform: scale(1.3);
    }
    50% {
      opacity: 0.1;
      transform: scale(1);
    }
  }
</style>
