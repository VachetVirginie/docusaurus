---
sidebar_position: 2
---

# 🧱 ARIA (Accessible Rich Internet Applications)

## 🎯 Objectif
Permettre aux développeurs d’ajouter des **informations d’accessibilité** aux composants **custom** ou **dynamiques** (menus déroulants, carrousels, modales, etc.), quand le HTML natif ne suffit pas.

---

## 🔷 1. Rôles ARIA (`role`)

Les rôles définissent **la fonction** d’un élément pour les technologies d’assistance.

### 📚 Catégories principales :

| Catégorie        | Exemples de rôles                                 | Description                                      |
|------------------|---------------------------------------------------|--------------------------------------------------|
| **Structure**     | `banner`, `main`, `navigation`, `region`          | Structuration logique de la page                 |
| **Widget**        | `button`, `checkbox`, `dialog`, `tab`, `tooltip` | Composants interactifs                          |
| **Document**      | `article`, `note`, `definition`                   | Types de contenu                                |
| **Live region**   | `alert`, `log`, `status`, `marquee`               | Zones mises à jour dynamiquement               |
| **Landmark**      | `form`, `search`, `navigation`, `main`            | Aide à la navigation régionale                 |
| **Window**        | `alertdialog`, `dialog`                           | Fenêtres modales                                |

#### Exemple :
```html
<div role="button" tabindex="0">Valider</div>
