<script setup>
defineEmits(['toggle-form', 'toggle-favoris'])
defineProps({
  modeFavoris: Boolean
})
</script>

<template>
  <header class="site-header">
    <div class="container header-flex">
              <div class="brand-section">

        <img src="../components/FlashVue.jpg" alt="Logo FlashVue" class="logo-img" />
        <div class="brand-text">
              <h1>FlashVue</h1>
            <p class="slogant">Apprendre, réviser, réussir </p>
              </div></div>
      <div class="header-buttons">
        <button class="btn-fav-toggle" @click="$emit('toggle-favoris')">
          {{ modeFavoris ? ' Revenir aux Paliers' : ' Voir mes favoris' }}
        </button>
        <button class="btn-add" @click="$emit('toggle-form')" title="Ajouter une carte">
          + Nouvelle Carte
        </button>
      </div>
    </div>
  </header>
</template>