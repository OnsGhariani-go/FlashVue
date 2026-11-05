<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  card: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['repondre', 'supprimer', 'favori'])

const estRetournee = ref(false)
const menuOuvert = ref(false)

// Réinitialiser au recto lors du changement de carte
watch(() => props.card.id, () => {
  estRetournee.value = false
  menuOuvert.value = false
})

function retourner() {
  estRetournee.value = !estRetournee.value
}

function valider(reussite) {
  estRetournee.value = false
  emit('repondre', reussite)
}

function toggleMenu(event) {
  event.stopPropagation() // Empêche de retourner la carte en cliquant sur les 3 points
  menuOuvert.value = !menuOuvert.value
}

function actionSupprimer(event) {
  event.stopPropagation()
  emit('supprimer', props.card.id)
  menuOuvert.value = false
}

function actionFavori(event) {
  event.stopPropagation()
  emit('favori', props.card.id)
  menuOuvert.value = false
}
</script>

<template>
  <div class="card-container">
    <!-- Carte avec animation 3D -->
    <div class="flip-card" :class="{ 'is-flipped': estRetournee }" @click="retourner">
      <div class="flip-card-inner">
        
        <!-- Front / Recto -->
        <div class="flip-card-front">
          <div class="card-top-bar">
            <span class="badge-palier">Palier {{ card.palier }} 🏆</span>
            <!-- Bouton 3 points -->
            <div class="menu-container">
              <button class="btn-dots" @click="toggleMenu">⋮</button>
              <div v-if="menuOuvert" class="dropdown-menu">
                <button @click="actionFavori">
                  {{ card.favori ? '⭐ Retirer des favoris' : '☆ Ajouter aux favoris' }}
                </button>
                <button class="btn-delete-opt" @click="actionSupprimer"> Supprimer</button>
              </div>
            </div>
          </div>

          <span class="card-label">QUESTION</span>
          <h3>{{ card.question }}</h3>
          <p class="hint-text">💡 Cliquez pour retourner</p>
        </div>

        <!-- Back / Verso -->
        <div class="flip-card-back">
          <span class="card-label">RÉPONSE</span>
          <p class="answer-text">{{ card.reponse }}</p>
          <p v-if="card.favori" class="fav-tag">⭐ Dans vos favoris</p>
        </div>
      </div>
    </div>

    <!-- Boutons d'action -->
    <div class="action-buttons" v-if="estRetournee">
      <button type="button" class="btn-danger" @click="valider(false)">
         À consolider
      </button>
      <button type="button" class="btn-success" @click="valider(true)">
         Acquis
      </button>
    </div>
  </div>
</template>