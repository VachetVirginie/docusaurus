---
sidebar_position: 2
---

# 🧠 Formulaires accessibles & erreurs (RGAA)

## 🎯 Objectifs du jour

1. Comprendre comment structurer un formulaire accessible.
2. Apprendre à lier les erreurs à un champ via `aria-describedby`.
3. Gérer dynamiquement les erreurs avec une zone de live-region (`aria-live`).
4. Implémenter un exemple dynamique avec Vue 3, Vuetify, RGAA-friendly.

---

## 🏗️ 1. Structure de base d’un champ accessible

### ✅ Ce qu’exige le RGAA :

| Élément            | Règle                                                                      |
| ------------------ | -------------------------------------------------------------------------- |
| `label`            | Chaque champ de formulaire doit avoir un libellé visible ou accessible.    |
| `aria-describedby` | Lier un champ à une aide ou erreur textuelle.                              |
| `aria-live`        | Permet d’annoncer dynamiquement les erreurs aux technologies d’assistance. |

---

## 📦 2. Exemple Vue 3 + Vuetify

### 🛠️ Composant : `AccessibleForm.vue`

```vue
<template>
  <v-form ref="formRef" @submit.prevent="validateForm">
    <v-text-field
      v-model="email"
      label="Adresse e-mail"
      :error="!!emailError"
      :aria-describedby="'email-error'"
      :aria-invalid="!!emailError"
      required
    />

    <div
      id="email-error"
      class="text-red-600 mt-1"
      v-if="emailError"
      aria-live="assertive"
      role="alert"
    >
      {{ emailError }}
    </div>

    <v-btn type="submit" color="primary">Envoyer</v-btn>
  </v-form>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const email = ref('')
const emailError = ref('')
const formRef = ref()

const validateForm = () => {
  emailError.value = ''

  if (!email.value) {
    emailError.value = 'L’adresse e-mail est requise.'
  } else if (!/^[^@\s]+@[^@\s]+\.[^@\s]+$/.test(email.value)) {
    emailError.value = 'Format de l’e-mail invalide.'
  }

  if (!emailError.value) {
    alert('Formulaire valide ✅')
  }
}
</script>

<style scoped>
#email-error {
  font-size: 0.875rem;
}
</style>
```

---

## 🔍 3. Explication des éléments clés

| Élément                 | Rôle                                                     |
| ----------------------- | -------------------------------------------------------- |
| `label`                 | Rendu automatiquement par Vuetify (`label="..."`)        |
| `aria-describedby`      | Lie l’erreur au champ. Utile pour lecteurs d’écran       |
| `aria-invalid`          | Signale que le champ contient une erreur                 |
| `aria-live="assertive"` | Annonce l’erreur dès qu’elle apparaît, même sans focus   |
| `role="alert"`          | Rôle complémentaire pour renforcer l’annonce de l’erreur |

---

## ✅ Bonnes pratiques RGAA

- Utiliser `required` **en plus** d’une validation JS.
- Afficher les erreurs sous le champ concerné.
- Toujours lier chaque message d’erreur via `aria-describedby`.
- Ne pas masquer les erreurs visuellement.
- `aria-live` doit être utilisé uniquement pour les erreurs **dynamiques**.

---

## 🎓 Pour aller plus loin

- Ajouter un `fieldset` + `legend` pour les groupes de champs.
- Ajouter la navigation clavier avec `tabindex`.
- Tester avec NVDA ou VoiceOver.
- Lire [Critères 11.1 à 11.13 du RGAA](https://accessibilite.numerique.gouv.fr/methode/criteres/#formulaires).

