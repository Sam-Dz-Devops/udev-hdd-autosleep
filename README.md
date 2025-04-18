# 🎚️ Mise en veille automatique d'un disque dur externe (HDD) avec udev sous Linux

Cette documentation explique comment configurer une règle `udev` pour mettre en veille automatiquement un **disque dur externe HDD**, ici un **LaCie Rugged Mini 5 To**, après 3 minutes d'inactivité.

---

## 🧾 Objectif

Mettre le disque en veille après **180 secondes (3 minutes)** grâce à `hdparm` via une règle `udev`.

---

## 🔍 Prérequis

- Le disque dur doit être **de type rotatif** (`ROTA=1`)
- Paquet `hdparm` installé :
  ```bash
  sudo apt install hdparm
