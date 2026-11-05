# FlashVue
#  FlashVue — L'Art de Réviser en Jouant

> **« Apprendre, réviser, réussir »**  
> *D'un palier à l'autre, transformez vos révisions en succès.*

---

##  Idée Globale & Problématique

### Le Constat
Dans le cadre des études, la mémorisation d'un volume important de cours représente un défi majeur. Beaucoup d'étudiants révisent de manière **passive** (relecture simple de fiches ou de cours), une méthode souvent ennuyeuse, peu engageante et inefficace pour ancrer durablement les connaissances en mémoire à long terme.

### La Solution FlashVue
**FlashVue** est une application web interactive conçue sous **Vue.js** qui transforme la révision académique en une expérience **ludique, structurée et motivante**. Elle s'appuie sur la méthode du **rappel actif** (*active recall*) via des cartes de mémoire (*flashcards*) interactives.

---

##  Fonctionnalités Pédagogiques & Mode Jeu

* **Cartes Recto/Verso 3D (Flip Card) :** Sollicitation active de la mémoire avant d'afficher la réponse d'un simple clic.
* **Système de Progression par Paliers (Gamification) :**
  * **Palier 1 (Initiation) :** 3 cartes requises.
  * **Palier 2 (Consolidation) :** 5 cartes requises.
  * **Palier 3 (Maîtrise) :** 10 cartes requises.
* ** Mécanique de Maîtrise :**
  * **« Maîtrisé » :** Valide la carte et fait progresser l'étudiant.
  * **« À revoir » :** Recommence le palier en cours depuis le début pour garantir l'assimilation parfaite du bloc.
* **Création de Cartes Personnalisées :** L'étudiant démarre avec une application vierge et construit son propre savoir en ajoutant ses propres flashcards.
* **Gestion des Favoris :** Marque les notions complexes pour les réviser séparément dans un espace dédié.

---

## Technologies & Outils (Tools)

### Frameworks & Langages
* [**Vue.js 3**](https://vuejs.org/) (Composition API `<script setup>`) — Réactivité et architecture par composants.
* **HTML5 / CSS3** — Animations 3D (`transform: rotateY`), design responsive et variables CSS.
* **JavaScript (ES6+)** — Logique d'état réactif (`ref`, `computed`).

### Tools & Environnement de Développement
* [**Vite.js**](https://vitejs.dev/) — Build tool ultra-rapide et serveur de développement local.
* [**Node.js**](https://nodejs.org/) (v18+) — Environnement d'exécution JavaScript.
* [**npm**](https://www.npmjs.com/) — Gestionnaire de paquets.
* [**VS Code**](https://code.visualstudio.com/) + Extension **Volar** — IDE recommandé.

---

## Architecture du Projet

```text
flashvue/
├── public/
│   └── favicon.ico
├── src/
│   ├── components/
│   │   ├── AppHeader.vue      # En-tête (Logo, Slogans, Boutons Favoris / Nouvelle carte)
│   │   ├── FlashCard.vue      # Composant Carte 3D, Flip & Boutons "Maîtrisé / À revoir"
│   │   └── AppFooter.vue      # Pied de page institutionnel
│   ├── App.vue                # Composant Racine (Gestion des paliers, score, cartes)
│   ├── main.js                # Point d'entrée de l'application Vue
│   └── style.css              # Styles globaux (Charte graphique & Responsive)
├── package.json
├── vite.config.js
└── README.md
