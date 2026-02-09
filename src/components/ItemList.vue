<script setup lang="ts">
import { ref } from 'vue'

interface Enemy {
    id: number;
    name: string;
    hp: number;
}
// 1. リアクティブな「空のList<int>」を作る
const enemyList = ref<Enemy[]>([])

const name = ref("");
const hp = ref(100);

// 2. ボタンを押した時の関数
const addRandomNumber = () => {
  const randomNum = Math.floor(Math.random() * 100)
  
  // C#の list.Add(randomNum) と同じ！
  // 配列にプッシュするだけで、画面に勝手に追加されます
  enemyList.value.push({ id:enemyList.value.length + 1, name:name.value || `敵${randomNum}`, hp:hp.value } )
}
</script>

<template>
  <input type="text" placeholder="敵の名前を入力してください" v-model="name" /> 
  <input type="number" placeholder="敵のHPを入力してください" v-model.number="hp" />
  <input type="number">
  <button @click="addRandomNumber">追加！</button>
  <button @click="enemyList = []">リセット</button>
  <ul>
    <li v-for="(enemy, index) in enemyList" :key="index">
      {{ index + 1 }}行目: {{ enemy.name }} (HP: {{ enemy.hp }})
    </li>
  </ul>
</template>

