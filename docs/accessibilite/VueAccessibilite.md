---
sidebar_position: 4
---

# 🦄 Comment créer des applications Vue.js accessibles

## Source de l'article

_Cet article est une traduction et adaptation en français de l'article ["How to Build Accessible Vue.js Applications"](https://www.vuemastery.com/blog/how-to-build-accessible-vuejs-applications) publié sur Vue Mastery par Kelvin Omereshone. J'ai traduit cet article pour mes notes personnelles et pour référence future._

## Introduction à l'accessibilité dans Vue.js

L'accessibilité décrit la facilité avec laquelle un site web peut être utilisé par des personnes de tous horizons. Lorsqu'une application est qualifiée d'accessible (abrégé en "a11y"), cela signifie que n'importe qui, quelle que soit sa situation, peut utiliser l'application sans difficulté et vivre une expérience agréable.

Pour atteindre cet objectif, il faut non seulement améliorer les performances de l'application, mais aussi la concevoir de manière à ce qu'elle soit :
- **Perceptible**
- **Utilisable**
- **Compréhensible**
- **Robuste**

Ce sont les quatre principes fondamentaux des Web Content Accessibility Guidelines (WCAG).

L'optimisation d'une application pour l'accessibilité vise à minimiser le nombre de personnes incapables de l'utiliser. Ainsi, l'importance de l'accessibilité ne peut être surestimée, car tout le monde n'utilise pas le web de la même manière.

## Liste de contrôle d'accessibilité pour une application Vue.js

Vue.js présente ses propres défis d'accessibilité spécifiques aux applications monopage.

Par exemple, dans une application web traditionnelle, lorsqu'un utilisateur navigue vers une page ou effectue une requête asynchrone, la page entière est actualisée. Cette actualisation redirige également le focus du clavier ou du lecteur d'écran vers le haut de la page.

Cependant, dans les applications monopage (SPA), les données sont mises à jour dynamiquement sans actualisation de la page. Par défaut, cela signifie que le focus reste sur le bouton ou le lien qui a été activé. Par conséquent, les utilisateurs qui ne peuvent pas voir la page ne sont pas informés du changement.

Heureusement, ces problèmes peuvent être correctement traités et résolus.

Examinons une liste de contrôle idéale qui peut servir de point de départ pour transformer votre application en une application inclusive. Cela vous permettra de créer plus facilement des applications Vue.js accessibles.

Voici les 13 points essentiels tirés de l'article :

### 1. Améliorer l'accessibilité au clavier avec l'attribut tabindex

La navigation au clavier est un aspect essentiel de l'accessibilité, car elle permet aux personnes utilisant des lecteurs d'écran ou d'autres technologies d'assistance d'interagir intuitivement avec une page web, via l'ordre de tabulation. Ainsi, l'ordre de tabulation doit suivre une séquence intuitive et prévisible alignée sur le flux de contenu d'une page web. C'est là qu'intervient l'attribut `tabindex`.

L'attribut `tabindex` est utilisé pour rendre les éléments HTML focalisables ou séquentiellement non focalisables lors de l'utilisation de la touche TAB du clavier pour la navigation.

Pour inclure dans l'ordre de tabulation un élément qui n'est pas focalisable par défaut (par exemple `<div>`, `<span>`), nous pouvons utiliser `tabindex="0"`. Utilisez `tabindex="-1"` pour retirer un élément de l'ordre de tabulation, comme les éléments cachés ou les images décoratives.

```vue
<template>
  <!--     Les boutons sont focalisables par défaut mais avec tabindex défini à -1, le bouton n'est plus focalisable -->
  <button tabindex="-1">Bouton non focalisable</button>
</template>
```

### 2. Faciliter la gestion du focus

La gestion du focus consiste à déterminer comment afficher un élément actuellement actif ou mis en évidence pour les utilisateurs lorsque la souris n'est pas utilisée. Elle garantit également que les changements de focus sont prévisibles.

Voici quelques conseils pour faciliter la gestion du focus :

- Pour les formulaires, assurez-vous de définir le premier champ sur `autofocus`, puis définissez programmatiquement le focus sur le champ suivant en appelant la méthode `.focus()`. Ainsi, les utilisateurs n'ont pas besoin d'utiliser leur souris pour remplir un formulaire.

```vue
<template>
  <form class="form">
    <div class="form-control">
      <label for="email">Email</label>
      <input
        autofocus
        type="email"
        name="email"
        id="email"
        @keypress.enter="setFocusOnSecondInputField"
      />
    </div>
    <div class="form-control">
      <label for="password">Mot de passe</label>
      <input ref="inputTwo" type="password" name="password" id="password" />
    </div>
  </form>
</template>

<script lang="ts" setup>
// Référence au deuxième champ de saisie dans le template
const inputTwo = ref<HTMLElement | null>(null);

// Fonction pour définir le focus sur le deuxième champ lorsque la touche Entrée est pressée
const setFocusOnSecondInputField = () => inputTwo.value?.focus();
</script>
```

- Certains éléments sont naturellement focalisables par défaut (en utilisant la touche TAB du clavier), sinon, avec `tabindex="0"`, n'importe quel élément peut être placé dans l'ordre de tabulation.

```vue
<template>
  <div tabindex="0" @click="doSomething" class="card">
    Ceci est un composant de carte cliquable, avec tabindex défini à 0, afin qu'il puisse être focalisable
  </div>
</template>
```

- Avec l'attribut `ref`, vous pouvez faire référence à un élément dans le template d'un composant. Par exemple, supposons que vous souhaitiez déplacer le focus sur un bouton de capture lorsqu'une page est montée :

```vue
<template>
	<div class="button-container">
		<button ref="captureButton">Capturer</button>	
	</div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";

const captureButton = ref<HTMLElement | null>(null);
const focusOnCaptureButton = () => {
	captureButton.value?.focus();
};

onMounted(() => focusOnCaptureButton());
</script>

<style>
.button-container {	
	display: flex;	
	align-items: center;	
	justify-content: center;	
	height: 300px;	
	background-color: white;
}
</style>
```

Dans l'extrait de code ci-dessus, lorsque la page est montée, la méthode `.focus()` est appelée sur le bouton de capture, qui reçoit alors le focus. Cela montre comment le focus peut être programmatiquement défini sur un élément.

### 3. Configurer un routage accessible et un titre de page pour chaque page

Les applications Vue.js utilisent généralement Vue Router pour gérer la navigation. Cependant, Vue Router présente quelques problèmes d'accessibilité qui doivent être pris en compte.

Par exemple, lorsque vous naviguez d'une page à l'autre dans une SPA, le contenu est actualisé sans rechargement complet de la page. Cela signifie que les utilisateurs de lecteurs d'écran ne sont pas informés de ce changement.

Ce problème peut être résolu en ajoutant un titre de page pour chaque route et en mettant en place un système d'annonce pour informer les utilisateurs des changements de page :

```javascript
// Dans votre fichier de configuration du routeur
import { createRouter, createWebHistory } from 'vue-router';
import Home from '../views/Home.vue';
import About from '../views/About.vue';

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,
    meta: {
      title: 'Accueil'
    }
  },
  {
    path: '/about',
    name: 'About',
    component: About,
    meta: {
      title: 'À propos'
    }
  }
];

const router = createRouter({
  history: createWebHistory(),
  routes
});

// Hook afterEach pour mettre à jour le titre de la page et annoncer la navigation
router.afterEach((to) => {
  // Mettre à jour le titre du document
  document.title = `MonApplication - ${to.meta.title}`;
  
  // Annoncer la navigation aux utilisateurs de lecteurs d'écran
  announceRouteChange(to.meta.title);
});

// Fonction pour annoncer les changements aux technologies d'assistance
function announceRouteChange(pageTitle) {
  const announcer = document.getElementById('route-announcer');
  if (announcer) {
    announcer.textContent = `Navigué vers ${pageTitle}`;
  }
}

export default router;
```

Dans votre composant `App.vue`, ajoutez un élément invisible pour les annonces :

```vue
<template>
  <div>
    <!-- Annonceur pour les lecteurs d'écran -->
    <div 
      id="route-announcer" 
      class="sr-only" 
      aria-live="assertive" 
      aria-atomic="true"
    ></div>
    
    <router-view />
  </div>
</template>

<style>
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
</style>
```

Ce code permet d'actualiser le titre de la page et d'annoncer les changements de navigation aux utilisateurs de technologies d'assistance.

### 4. Écrire du HTML sémantique dès le début

L'utilisation du HTML sémantique est essentielle pour la construction d'applications accessibles. Le HTML sémantique comprend l'utilisation d'éléments HTML qui transportent une signification ou un sens - au-delà de leur apparence visuelle.

Cela aide les utilisateurs de technologies d'assistance à parcourir et comprendre le contenu comme vous l'avez prévu.

Les technologies d'assistance telles que les lecteurs d'écran utilisent la sémantique HTML pour fournir plus d'informations contextuelles aux utilisateurs. Par exemple, un élément `<nav>` est reconnu comme un point de navigation principal, tandis qu'un élément `<article>` est reconnu comme un contenu autonome.

Exemples d'éléments de structure sémantiques importants :
- `<header>` - En-tête de page ou de section
- `<nav>` - Section de navigation
- `<main>` - Contenu principal de la page
- `<section>` - Section thématique du contenu
- `<article>` - Contenu autonome
- `<aside>` - Contenu lié au contenu principal
- `<footer>` - Pied de page ou de section

Voici un exemple d'application Vue.js utilisant le HTML sémantique :

```vue
<template>
  <div id="app">
    <header>
      <h1>Mon Application Vue</h1>
      
      <nav>
        <ul>
          <li><router-link to="/">Accueil</router-link></li>
          <li><router-link to="/about">À propos</router-link></li>
          <li><router-link to="/contact">Contact</router-link></li>
        </ul>
      </nav>
    </header>
    
    <main>
      <router-view></router-view>
    </main>
    
    <footer>
      <p>&copy; 2025 - Mon Application Vue</p>
    </footer>
  </div>
</template>
```

Contrairement à cet exemple non sémantique qui ne fournit aucune information sur la structure aux technologies d'assistance :

```vue
<template>
  <div id="app">
    <div class="header">
      <div class="title">Mon Application Vue</div>
      
      <div class="nav">
        <div class="nav-items">
          <div class="nav-item" @click="navigateTo('/')">Accueil</div>
          <div class="nav-item" @click="navigateTo('/about')">À propos</div>
          <div class="nav-item" @click="navigateTo('/contact')">Contact</div>
        </div>
      </div>
    </div>
    
    <div class="content">
      <router-view></router-view>
    </div>
    
    <div class="footer">
      <div class="copyright">&copy; 2025 - Mon Application Vue</div>
    </div>
  </div>
</template>
```

L'utilisation du HTML sémantique établit également une base solide pour l'ajout d'attributs ARIA (lorsque nécessaire) afin d'améliorer davantage l'accessibilité. Cependant, rappelez-vous la première règle d'ARIA : "N'utilisez pas ARIA si vous pouvez utiliser du HTML natif à la place."

### 5. Utiliser ARIA quand le HTML natif ne suffit pas

Lorsque le HTML sémantique seul ne peut pas communiquer toutes les informations nécessaires aux utilisateurs de technologies d'assistance, les attributs ARIA (Accessible Rich Internet Applications) peuvent être utilisés pour améliorer l'accessibilité.

ARIA est un ensemble d'attributs qui définissent comment rendre le contenu et les applications web plus accessibles aux personnes ayant des handicaps. Ces attributs complètent le HTML en fournissant des informations supplémentaires aux technologies d'assistance lorsque les éléments HTML standards ne disposent pas des fonctionnalités nécessaires.

Principales catégories d'attributs ARIA :

1. **Rôles (roles)** - Définissent ce qu'un élément est ou fait (ex. : `role="tablist"`, `role="tab"`, `role="tabpanel"`).

2. **Propriétés** - Définissent les propriétés des éléments (ex. : `aria-labelledby`, `aria-label`, `aria-describedby`).

3. **États** - Définissent les états actuels des éléments (ex. : `aria-expanded`, `aria-checked`, `aria-selected`).

Voici un exemple d'implémentation d'un panneau d'onglets accessible dans Vue.js :

```vue
<template>
  <div class="tabs-component">
    <!-- Liste des onglets -->
    <div role="tablist" class="tabs">
      <button 
        v-for="(tab, index) in tabs" 
        :key="index"
        :id="`tab-${index}`"
        role="tab"
        :aria-selected="activeTab === index ? 'true' : 'false'"
        :aria-controls="`panel-${index}`"
        :tabindex="activeTab === index ? 0 : -1"
        @click="activateTab(index)"
        @keydown="handleTabKeydown"
        class="tab"
        :class="{ 'active': activeTab === index }"
      >
        {{ tab.title }}
      </button>
    </div>

    <!-- Panneaux de contenu des onglets -->
    <div 
      v-for="(tab, index) in tabs" 
      :key="index"
      :id="`panel-${index}`"
      role="tabpanel"
      :aria-labelledby="`tab-${index}`"
      :hidden="activeTab !== index"
      class="tab-panel"
    >
      {{ tab.content }}
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';

const tabs = [
  { title: 'Onglet 1', content: 'Contenu du premier onglet' },
  { title: 'Onglet 2', content: 'Contenu du deuxième onglet' },
  { title: 'Onglet 3', content: 'Contenu du troisième onglet' },
];

const activeTab = ref(0);

const activateTab = (index: number) => {
  activeTab.value = index;
};

const handleTabKeydown = (event: KeyboardEvent) => {
  // Gestion des touches flèches pour la navigation entre les onglets
  const tabsCount = tabs.length;
  
  switch (event.key) {
    case 'ArrowRight':
      event.preventDefault();
      activeTab.value = (activeTab.value + 1) % tabsCount;
      document.getElementById(`tab-${activeTab.value}`)?.focus();
      break;
    case 'ArrowLeft':
      event.preventDefault();
      activeTab.value = (activeTab.value - 1 + tabsCount) % tabsCount;
      document.getElementById(`tab-${activeTab.value}`)?.focus();
      break;
  }
};
</script>

<style scoped>
.tabs-component {
  margin: 20px 0;
}

.tabs {
  display: flex;
  border-bottom: 1px solid #ccc;
}

.tab {
  padding: 10px 15px;
  background: none;
  border: none;
  cursor: pointer;
  border-bottom: 3px solid transparent;
  transition: border-color 0.3s;
}

.tab.active {
  border-bottom-color: #42b983;
}

.tab-panel {
  padding: 15px;
  background: #f9f9f9;
}
</style>
```

Cet exemple implémente un composant d'onglets accessible en utilisant les attributs ARIA appropriés. Les points clés d'accessibilité sont :

- Utilisation des rôles ARIA appropriés (`tablist`, `tab`, `tabpanel`)
- Association des onglets et de leurs panneaux avec `aria-controls` et `aria-labelledby`
- Indication de l'onglet sélectionné avec `aria-selected`
- Gestion du focus et de la navigation clavier avec les flèches directionnelles

Suivez toujours les cinq règles fondamentales d'ARIA :

1. N'utilisez pas ARIA si vous pouvez utiliser des éléments HTML natifs avec la sémantique intégrée.
2. Ne modifiez pas la sémantique native sauf si c'est absolument nécessaire.
3. Tous les contrôles interactifs ARIA doivent être utilisables au clavier.
4. N'utilisez pas `role="presentation"` ou `aria-hidden="true"` sur un élément focalisable.
5. Tous les éléments interactifs doivent avoir un nom accessible.

### 6. Indiquer les changements d'état dynamiques

L'un des défis d'accessibilité dans les applications Vue.js concerne les mises à jour dynamiques du contenu. Lorsque le contenu change sans rechargement de page, comme c'est souvent le cas dans les SPA, les utilisateurs de lecteurs d'écran peuvent ne pas être avertis de ces changements.

La solution consiste à utiliser des régions live ARIA pour annoncer ces changements aux technologies d'assistance.

```vue
<template>
  <div>
    <form @submit.prevent="submitForm">
      <button type="submit">Envoyer</button>
    </form>
    
    <!-- Région live pour annoncer le résultat de l'envoi du formulaire -->
    <div class="status-message" aria-live="polite" aria-atomic="true">
      {{ statusMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const statusMessage = ref('');

const submitForm = async () => {
  statusMessage.value = 'Envoi en cours...'; // Sera annoncé par le lecteur d'écran
  
  try {
    await new Promise(resolve => setTimeout(resolve, 1500));
    statusMessage.value = 'Formulaire envoyé avec succès!'; // Sera annoncé
  } catch (error) {
    statusMessage.value = 'Erreur lors de l\'envoi du formulaire. Veuillez réessayer.'; // Sera annoncé
  }
};
</script>
```

Il existe différentes valeurs pour l'attribut `aria-live` :

- `aria-live="polite"` : Le lecteur d'écran annoncera les changements lorsque l'utilisateur sera inactif, sans interrompre les annonces en cours. C'est le meilleur choix pour la plupart des cas.

- `aria-live="assertive"` : Le lecteur d'écran interrompra tout ce qu'il est en train de dire pour annoncer immédiatement le changement. À utiliser uniquement pour les messages urgents (comme des erreurs critiques).

- `aria-live="off"` (valeur par défaut) : Les changements ne seront pas annoncés automatiquement.

Ajoutez l'attribut `aria-atomic="true"` pour que tout le contenu de la région soit annoncé à chaque fois qu'il change, même si une seule partie a été modifiée. Sans cet attribut, seul le texte modifié serait annoncé.

Voici quelques conseils supplémentaires pour les régions live :

1. Gardez les messages concis et informatifs.
2. Évitez d'utiliser trop de régions `aria-live="assertive"` car elles peuvent interrompre l'expérience utilisateur.
3. Pré-initialisez les régions live vides dans le DOM avant de les mettre à jour pour garantir leur détection par les lecteurs d'écran.

### 7. Tester et valider l'accessibilité de votre application Vue.js

Il est crucial de tester régulièrement l'accessibilité de votre application Vue.js pendant le développement. Heureusement, il existe de nombreux outils et méthodes pour vous aider à identifier et corriger les problèmes d'accessibilité.

#### Outils de test automatiques

1. **Vue Axe** - Un plugin pour Vue.js basé sur axe-core qui vérifie l'accessibilité de vos composants Vue :

```bash
npm install --save-dev vue-axe
```

Puis dans votre fichier d'entrée principal :

```javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'

// Importer vue-axe uniquement en mode développement
if (process.env.NODE_ENV !== 'production') {
  const VueAxe = require('vue-axe').default
  const app = createApp(App)
  app.use(VueAxe)
  app.mount('#app')
} else {
  createApp(App).mount('#app')
}
```

2. **Vue A11y Checker** - Un ensemble d'outils pour valider l'accessibilité de vos applications Vue :

```bash
npm install @vue-a11y/eslint-plugin-a11y --save-dev
```

3. **ESLint avec eslint-plugin-vuejs-accessibility** - Ce plugin ESLint vérifie votre code Vue pour détecter les problèmes d'accessibilité :

```bash
npm install eslint-plugin-vuejs-accessibility --save-dev
```

Configuration dans `.eslintrc.js` :

```javascript
module.exports = {
  extends: [
    'plugin:vuejs-accessibility/recommended',
  ],
  plugins: [
    'vuejs-accessibility'
  ],
}
```

#### Tests manuels

Les outils automatiques ne peuvent pas capturer tous les problèmes d'accessibilité. Voici quelques méthodes de test manuel essentielles :

1. **Test de navigation au clavier** - Essayez de naviguer dans votre application en utilisant uniquement le clavier (touches Tab, Enter, flèches) pour vérifier que tous les éléments interactifs sont accessibles et que l'ordre de tabulation est logique.

2. **Test avec lecteurs d'écran** - Utilisez des lecteurs d'écran comme NVDA (Windows, gratuit), VoiceOver (macOS, intégré) ou JAWS pour tester votre application du point de vue d'un utilisateur non-voyant.

3. **Test de contraste** - Utilisez des outils comme le Color Contrast Analyzer pour vérifier que le texte a un contraste suffisant avec l'arrière-plan.

4. **Test avec des paramètres d'affichage différents** - Testez votre application avec un zoom élevé, différents paramètres de contraste, et en mode haute résolution.

5. **Validation WCAG** - Utilisez des outils comme WAVE (https://wave.webaim.org/) ou Axe DevTools pour vérifier la conformité aux directives WCAG.

## Exemple pratique : une modale accessible en Vue.js

Voici un exemple de composant modal que j'ai créé en tenant compte de l'accessibilité :

```vue
<template>
  <Teleport to="body">
    <transition name="modal-fade">
      <div 
        v-if="isOpen" 
        class="modal-backdrop"
        @click="closeOnBackdrop ? close() : null"
      >
        <div 
          ref="modalRef"
          role="dialog"
          aria-modal="true"
          :aria-labelledby="titleId"
          aria-describedby="modal-description"
          class="modal"
          @click.stop
          @keydown.esc="close"
        >
          <header class="modal-header">
            <h2 :id="titleId">{{ title }}</h2>
            <button 
              type="button" 
              class="modal-close" 
              aria-label="Fermer" 
              @click="close"
            >
              &times;
            </button>
          </header>
          
          <div class="modal-body" id="modal-description">
            <slot></slot>
          </div>
          
          <footer class="modal-footer">
            <slot name="footer">
              <button @click="close">Fermer</button>
            </slot>
          </footer>
        </div>
      </div>
    </transition>
  </Teleport>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, watch } from 'vue';
import { useFocusTrap } from '@vueuse/core'; // Super utile pour confiner le focus

const props = defineProps({
  isOpen: Boolean,
  title: String,
  closeOnBackdrop: {
    type: Boolean,
    default: true
  },
  titleId: {
    type: String,
    default: 'modal-title'
  }
});

const emit = defineEmits(['close']);

const modalRef = ref(null);
const previouslyFocusedElement = ref(null);

// Piéger le focus dans la modale
const { activate, deactivate } = useFocusTrap(modalRef);

// Fonction pour fermer la modale
const close = () => {
  emit('close');
};

// Gestionnaire pour les touches d'échappement
const handleKeyDown = (e) => {
  if (e.key === 'Escape' && props.isOpen) {
    close();
  }
};

// Gérer l'ouverture et la fermeture
watch(() => props.isOpen, (isOpen) => {
  if (isOpen) {
    // Sauvegarder l'élément actif avant d'ouvrir la modale
    previouslyFocusedElement.value = document.activeElement;
    
    // Ajouter le gestionnaire d'événements et piéger le focus
    document.addEventListener('keydown', handleKeyDown);
    
    // Attendre le rendu pour activer le piège de focus
    nextTick(() => {
      activate();
      modalRef.value?.focus();
    });
    
    // Empêcher le défilement du corps
    document.body.style.overflow = 'hidden';
  } else {
    // Libérer le piège de focus
    deactivate();
    
    // Restaurer le focus précédent
    previouslyFocusedElement.value?.focus();
    
    // Supprimer le gestionnaire d'événements
    document.removeEventListener('keydown', handleKeyDown);
    
    // Restaurer le défilement
    document.body.style.overflow = '';
  }
});

// Nettoyage
onBeforeUnmount(() => {
  document.removeEventListener('keydown', handleKeyDown);
  document.body.style.overflow = '';
});
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  border-radius: 8px;
  max-width: 500px;
  width: 80%;
  max-height: 80vh;
  overflow-y: auto;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid #eee;
}

.modal-body {
  padding: 1rem;
}

.modal-footer {
  padding: 1rem;
  border-top: 1px solid #eee;
  display: flex;
  justify-content: flex-end;
}

.modal-close {
  border: none;
  background: transparent;
  font-size: 1.5rem;
  cursor: pointer;
}

/* Animation */
.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.3s;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}
</style>
```

## Conclusion : L'accessibilité comme partie intégrante du développement Vue.js

L'article original de Kelvin Omereshone sur Vue Mastery m'a vraiment ouvert les yeux sur l'importance cruciale de l'accessibilité dans le développement d'applications Vue.js. J'ai découvert que l'accessibilité n'est pas une fonctionnalité supplémentaire ou optionnelle, mais bien une approche fondamentale qui doit être intégrée dès les premières lignes de code.

Voici les apprentissages clés que je retiens de cet article :

- **Accessibilité native d'abord** : Privilégier le HTML sémantique et les fonctionnalités natives avant d'ajouter des attributs ARIA
- **Navigation au clavier** : Garantir que tous les éléments interactifs sont accessibles par clavier
- **Gestion du focus** : Prendre en charge explicitement le focus lors des changements dynamiques
- **Routage accessible** : Informer les utilisateurs de technologies d'assistance lors des changements de page
- **Composants réutilisables accessibles** : Créer des composants comme les modèles en pensant d'abord à l'accessibilité

Je comprends maintenant que rendre mes applications accessibles bénéficie à tous les utilisateurs, pas seulement à ceux qui ont des handicaps. Une application accessible est souvent plus intuitive, plus robuste, et offre une meilleure expérience utilisateur globale.

Ce que j'apprécie particulièrement dans l'approche de Vue.js, c'est qu'elle ne compromet pas l'expérience de développement ou les fonctionnalités avancées pour atteindre l'accessibilité. Nous pouvons créer des applications modernes, réactives et visuellement attrayantes tout en maintenant les standards d'accessibilité.

## Ressources pour aller plus loin

Pour approfondir mes connaissances sur l'accessibilité web et Vue.js, voici quelques ressources précieuses :

- [Documentation officielle de Vue.js sur l'accessibilité](https://fr.vuejs.org/guide/best-practices/accessibility.html)
- [Vue A11y Project](https://vue-a11y.com/) - Collection d'outils et de ressources pour l'accessibilité Vue
- [Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/fr)
- [Article original de Vue Mastery](https://www.vuemastery.com/blog/how-to-build-accessible-vuejs-applications)
- [A11Y Project](https://www.a11yproject.com/) - Ressource communautaire sur l'accessibilité web

---

> "L'accessibilité est comme l'air pur : on ne la remarque que lorsqu'elle manque, mais elle améliore la qualité de vie de tous."
