<script setup>
import { ref, computed } from 'vue'
import AppHeader from './components/AppHeader.vue'
import FlashCard from './components/FlashCard.vue'
import AppFooter from './components/AppFooter.vue'

const quotasPaliers = {
  1: 3,
  2: 5,
  3: 10
}

const cards = ref([
  { id: 1, question: 'Quelle est la complexité du tri fusion ?', reponse: 'O(n log n) dans tous les cas', palier: 1, favori: false },
  { id: 2, question: 'Quelle commande SQL modifie une table ?', reponse: 'ALTER TABLE', palier: 1, favori: false },
  { id: 3, question: 'Rôle de ref() dans Vue.js ?', reponse: 'Créer une donnée réactive', palier: 1, favori: false },
  { id: 4, question: 'Comment afficher une donnée dans un template Vue ?', reponse: 'Avec les doubles accolades {{ }}', palier: 1, favori: false },
  { id: 5, question: 'Quel hook s’exécute au chargement d’un composant ?', reponse: 'onMounted()', palier: 1, favori: false },
  { id: 6, question: 'Quelle est la différence entre ref() et reactive() ?', reponse: 'ref accepte les primitives et les objets ; reactive est destiné aux objets', palier: 2, favori: false }
])

const palierActuel = ref(1)
const maxPalierAtteint = ref(1)
const indexEtape = ref(0)
const afficherFormulaire = ref(false)
const modeFavoris = ref(false)
const palierValide = ref(false)
const menuPaliersOuvert = ref(false)

const nouvelleQuestion = ref('')
const nouvelleReponse = ref('')

const listePaliersDisponibles = computed(() =>
  Object.keys(quotasPaliers).map(Number)
)

const cartesDuPalier = computed(() =>
  cards.value.filter(c => c.palier === palierActuel.value)
)

const carteCourante = computed(() =>
  cartesDuPalier.value[indexEtape.value] ?? null
)

const listeFavoris = computed(() =>
  cards.value.filter(c => c.favori)
)

const totalFavoris = computed(() => listeFavoris.value.length)

const quotaCourant = computed(() =>
  quotasPaliers[palierActuel.value] ?? 0
)

const nbCartesActuelles = computed(() =>
  cartesDuPalier.value.length
)

const cartesManquantes = computed(() =>
  Math.max(0, quotaCourant.value - nbCartesActuelles.value)
)

const quotaAtteint = computed(() =>
  nbCartesActuelles.value >= quotaCourant.value
)

const peutPasserPalier = computed(() =>
  palierValide.value &&
  palierActuel.value < listePaliersDisponibles.value.length
)

function traiterReponse(reussite) {
  if (palierValide.value || !carteCourante.value) return

  if (!reussite) {
    alert(`Mauvaise réponse ! Vous recommencez le Palier ${palierActuel.value} depuis le début.`)
    indexEtape.value = 0
    return
  }

  indexEtape.value++

  if (indexEtape.value >= cartesDuPalier.value.length) {
    if (!quotaAtteint.value) {
      alert(`Vous avez révisé toutes les cartes, mais il faut au moins ${quotaCourant.value} cartes au Palier ${palierActuel.value}. Ajoutez encore ${cartesManquantes.value} carte(s).`)
      indexEtape.value = 0
      return
    }

    palierValide.value = true

    maxPalierAtteint.value = Math.min(
      Math.max(maxPalierAtteint.value, palierActuel.value + 1),
      listePaliersDisponibles.value.length
    )
  }
}

function changerPalier(palier) {
  if (
    !listePaliersDisponibles.value.includes(palier) ||
    palier > maxPalierAtteint.value
  ) return

  palierActuel.value = palier
  indexEtape.value = 0
  palierValide.value = false
  modeFavoris.value = false
  menuPaliersOuvert.value = false
}

function passerPalierSuivant() {
  if (!peutPasserPalier.value) return

  palierActuel.value++
  indexEtape.value = 0
  palierValide.value = false
  menuPaliersOuvert.value = false
}

function recommencerPalier() {
  indexEtape.value = 0
  palierValide.value = false
}

function ajouterCarte() {
  const question = nouvelleQuestion.value.trim()
  const reponse = nouvelleReponse.value.trim()

  if (!question || !reponse) return

  cards.value.push({
    id: Date.now(),
    question,
    reponse,
    palier: palierActuel.value,
    favori: false
  })

  nouvelleQuestion.value = ''
  nouvelleReponse.value = ''
  afficherFormulaire.value = false
}

function supprimerCarte(id) {
  if (!confirm('Voulez-vous vraiment supprimer cette flashcard ?')) return

  const indexSupprime = cartesDuPalier.value.findIndex(c => c.id === id)

  cards.value = cards.value.filter(c => c.id !== id)

  if (indexSupprime !== -1) {
    if (indexSupprime < indexEtape.value) {
      indexEtape.value--
    }

    indexEtape.value = Math.max(
      0,
      Math.min(indexEtape.value, cartesDuPalier.value.length - 1)
    )

    palierValide.value = false
  }
}

function basculerFavori(id) {
  const carte = cards.value.find(c => c.id === id)

  if (carte) {
    carte.favori = !carte.favori
  }
}
</script>

<template>
  <div class="app">
    <AppHeader
      :modeFavoris="modeFavoris"
      @toggle-form="afficherFormulaire = !afficherFormulaire"
      @toggle-favoris="modeFavoris = !modeFavoris"
    />

    <main class="container main-content">
      <div v-if="!modeFavoris" class="trophy-nav-container">
        <button
          type="button"
          class="btn-trophy"
          @click="menuPaliersOuvert = !menuPaliersOuvert"
        >
          Palier actuel : {{ palierActuel }} ▾
        </button>

        <div v-if="menuPaliersOuvert" class="trophy-dropdown">
          <p class="dropdown-title">Paliers débloqués :</p>

          <button
            v-for="p in listePaliersDisponibles"
            :key="p"
            type="button"
            class="dropdown-item"
            :class="{
              active: palierActuel === p,
              locked: p > maxPalierAtteint
            }"
            :disabled="p > maxPalierAtteint"
            @click="changerPalier(p)"
          >
            <span>Palier {{ p }} ({{ quotasPaliers[p] }} cartes)</span>
            <span>
              {{ p > maxPalierAtteint ? '🔒' : p === palierActuel ? 'En cours' : '⭐ Débloqué' }}
            </span>
          </button>
        </div>
      </div>

      <section v-if="afficherFormulaire" class="form-section">
        <h3>Ajouter une carte au Palier {{ palierActuel }}</h3>

        <form @submit.prevent="ajouterCarte">
          <div class="form-group">
            <label for="question">Question :</label>
            <input
              id="question"
              v-model="nouvelleQuestion"
              type="text"
              placeholder="Entrez la question"
              required
            />
          </div>

          <div class="form-group">
            <label for="reponse">Réponse :</label>
            <textarea
              id="reponse"
              v-model="nouvelleReponse"
              placeholder="Entrez la réponse"
              rows="3"
              required
            ></textarea>
          </div>

          <div class="form-actions">
            <button type="submit" class="btn-primary">Ajouter</button>
            <button
              type="button"
              class="btn-secondary"
              @click="afficherFormulaire = false"
            >
              Annuler
            </button>
          </div>
        </form>
      </section>

      <section v-if="modeFavoris">
        <div class="palier-header">
          <h2>⭐ Mes flashcards favorites</h2>
          <span class="badge-palier">{{ totalFavoris }} carte(s)</span>
        </div>

        <div v-if="listeFavoris.length" class="favoris-grid">
          <FlashCard
            v-for="carte in listeFavoris"
            :key="carte.id"
            :card="carte"
            @supprimer="supprimerCarte"
            @favori="basculerFavori"
          />
        </div>

        <div v-else class="empty-state">
          <p>Aucune carte dans vos favoris pour l'instant.</p>
        </div>
      </section>

      <section v-else>
        <div v-if="cartesDuPalier.length">
          <div class="palier-header">
            <h2>🏆 Palier {{ palierActuel }}</h2>
            <span
              class="badge-palier"
              :class="{
                'quota-ok': quotaAtteint,
                'quota-warning': !quotaAtteint
              }"
            >
              {{ nbCartesActuelles }} / {{ quotaCourant }} cartes requises
            </span>
          </div>

          <div class="stepper-wrapper">
            <div
              v-for="(carte, index) in cartesDuPalier"
              :key="carte.id"
              class="step-item"
            >
              <div
                class="step-circle"
                :class="{
                  completed: index < indexEtape,
                  active: index === indexEtape && !palierValide
                }"
              >
                {{ index < indexEtape ? '✓' : index + 1 }}
              </div>

              <div
                v-if="index < cartesDuPalier.length - 1"
                class="step-line"
                :class="{ completed: index < indexEtape }"
              ></div>
            </div>
          </div>

          <div v-if="cartesManquantes > 0" class="quota-alert">
            <p>
              💡 <strong>Objectif du palier :</strong>
              Il manque encore <strong>{{ cartesManquantes }} carte(s)</strong>
              pour compléter ce palier. Cliquez sur
              <em>« Nouvelle carte »</em> pour enrichir votre session.
            </p>
          </div>

          <section v-if="!palierValide && carteCourante">
            <FlashCard
              :card="carteCourante"
              @repondre="traiterReponse"
              @supprimer="supprimerCarte"
              @favori="basculerFavori"
            />
          </section>

          <section v-else-if="palierValide" class="results-card">
            <h2>Félicitations ! 🎉</h2>
            <p>
              Vous avez validé toutes les cartes du
              <strong>Palier {{ palierActuel }}</strong> !
            </p>

            <div class="results-buttons">
              <button
                v-if="peutPasserPalier"
                type="button"
                class="btn-primary"
                @click="passerPalierSuivant"
              >
                Passer au palier suivant
              </button>

              <button
                type="button"
                class="btn-secondary"
                @click="recommencerPalier"
              >
                Revoir ce palier
              </button>
            </div>

            <p v-if="!peutPasserPalier" class="success-message">
              🏆 Bravo ! Vous avez terminé tous les paliers.
            </p>
          </section>
        </div>

        <div v-else class="empty-state">
          <h2>Palier {{ palierActuel }} vide !</h2>
          <p>Créez une nouvelle flashcard pour commencer à réviser.</p>
          <button
            type="button"
            class="btn-primary"
            @click="afficherFormulaire = true"
          >
            + Nouvelle carte
          </button>
        </div>
      </section>
    </main>

    <AppFooter />
  </div>
</template>

