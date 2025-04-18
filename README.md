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

---

## 🧪 Identifier le disque

**1 - Liste des disques :**

```bash
lsblk -d -o name,rota,tran
```

Exemple :

```bash
NAME ROTA TRAN
sda     0 sata
sdb     0 sata
sdc     1 usb
```

Ici, sdc est le disque rotatif externe LaCie.

**2 - Identifier son ID unique :**

```bash
udevadm info -q all -n /dev/sdc | grep ID_SERIAL
```

Résultat typique :

```bash
E: ID_SERIAL=LaCie_Rugged_Mini_STJJ5000400_XXXXXXXX
```

---

## ⚙️ Créer une règle udev

Créer le fichier suivant :

```bash
sudo nano /etc/udev/rules.d/99-hdparm-lacie.rules
```

---

## 💡 Version simple (si /dev/sdc est toujours le bon disque) :

```bash
ACTION=="add|change", KERNEL=="sdc", ATTR{queue/rotational}=="1", RUN+="/usr/bin/hdparm -S 36 /dev/sdc"
```

---

## ✅ Version robuste (identification par numéro de série) :

```bash
ACTION=="add|change", ENV{ID_SERIAL}=="LaCie_Rugged_Mini_STJJ5000400_XXXXXXXX", ATTR{queue/rotational}=="1", RUN+="/usr/bin/hdparm -S 36 /dev/%k"
```

### 🔁 Remplace XXXXXXXX par la valeur exacte obtenue précédemment.

---

## 🔄 Appliquer les règles

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

---

## ✅ Vérifier que la mise en veille est active

**1 - Lancer cette commande pour voir l’état du disque :**

```bash
sudo hdparm -C /dev/sdc
```

- **active/idle** → disque actif

- **standby** → disque en veille 😴

**2 - Regarder les options d'économie d’énergie :**

```bash
sudo hdparm -I /dev/sdc | grep "standby timer"
```

---

# 🧠 Note

Le paramètre -S 36 signifie :

- 36 × 5 secondes = 180 secondes (3 minutes)

Tu peux changer ce nombre selon tes besoins. Par exemple :

- **-S 0** : désactive la mise en veille automatique

- **-S 12** : 1 minute

- **-S 240** : 20 minutes

---

# 📦 Exemple de disque compatible

- **Modèle** : LaCie Rugged Mini 5 To (STJJ5000400)

- **Type** : HDD 2,5" - 5400 RPM

- **Connectique** : USB 3.0

- **Commande reconnue** : hdparm -S supportée ✅

---

# 🔐 Sécurité

⚠️ Ne pas utiliser **hdparm -S** sur un **SSD (non rotatif)** :

```bash
lsblk -d -o name,rota
```

→ Assurez-vous que ROTA=1 uniquement.

---

## 📬 Contribuer

Les contributions sont les bienvenues !  
N’hésitez pas à ouvrir une *issue* ou à soumettre une *pull request*.