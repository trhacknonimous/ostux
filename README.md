# ostux
Info for some distri, live USB,.... Like kali, tsurugi, caine, kodachi, rescatux, ....
# ⚡ Dual Boot Windows 11 + Tsurugi Linux — Guide Complet

## ✅ Objectif :
Installer Windows 11 puis Tsurugi Linux sur un même disque dur (UEFI), avec un schéma de partition clair et un GRUB qui permet de démarrer l’un ou l’autre des systèmes.

---

## 🧩 Prérequis

- BIOS/UEFI configuré en **UEFI uniquement** (pas de mode Legacy/CSM)
- Disque dur (HDD/SSD) de 256 Go ou plus
- Live USB avec **Ventoy**, contenant :
  - ISO **Windows 11**
  - ISO **Tsurugi Linux**
- Connexion internet pour les MAJ et GRUB

---

## 🧱 Schéma final des partitions (exemple)

| Partition    | Format      | Taille        | Point de montage | Utilisation                 | Créée par  |
|--------------|-------------|---------------|------------------|-----------------------------|------------|
| `/dev/sda1`  | FAT32       | 512 Mo        | `/boot/efi`      | Boot EFI (GRUB + Win UEFI) | Windows    |
| `/dev/sda2`  | NTFS        | 100–200 Go    | —                | Système Windows 11         | Windows    |
| `/dev/sda3`  | ext4        | 50–100 Go     | `/`              | Système Tsurugi Linux      | Tsurugi    |
| `/dev/sda4`  | ext4        | 100 Go+       | `/home`          | Données utilisateur Linux  | Tsurugi    |
| `/dev/sda5`  | linux-swap  | 2–8 Go        | swap             | Mémoire virtuelle Linux    | Tsurugi    |

---

## 1️⃣ Installation de Windows 11

### Étapes :
1. Boot sur l’ISO Windows 11 via Ventoy
2. Suivre l'installation jusqu’à l’écran *"Où voulez-vous installer Windows ?"*
3. Supprimer toutes les partitions si le disque est vierge
4. Cliquer sur *"Nouveau"*, Windows va créer :
   - Partition EFI (512 Mo)
   - Partition principale NTFS
   - Partition MSR (réservée)
5. Installer Windows sur la **partition NTFS principale**
6. Laisser **au moins 150 Go d’espace libre non alloué** pour Tsurugi

🛑 **NE PAS UTILISER TOUT LE DISQUE !**  
✅ **Windows crée automatiquement `/dev/sda1` pour EFI**

---

## 2️⃣ Installation de Tsurugi Linux

### Étapes :
1. Booter sur l’ISO Tsurugi via Ventoy
2. Lancer l’installation graphique (Calamares)
3. Choisir *"Installation manuelle (partitions personnalisées)"*
4. Dans l’espace libre, créer les partitions :

#### ➤ `/` (racine)
- Taille : 50–100 Go
- Format : `ext4`
- Point de montage : `/`

#### ➤ `/home` *(optionnel mais recommandé)*
- Taille : 100 Go ou +
- Format : `ext4`
- Point de montage : `/home`

#### ➤ `swap`
- Taille : 2–8 Go
- Format : `linux-swap`
- Pas de point de montage

#### ➤ `/boot/efi`
- Sélectionner **`/dev/sda1`**
- Format : **NE PAS FORMATER**
- Point de montage : `/boot/efi`

5. Confirmer le schéma

---

## 3️⃣ Chargeur de démarrage GRUB

- Installer GRUB sur : **`/dev/sda`** (le disque entier, pas une partition)
- GRUB détectera automatiquement Windows 11
- Ne pas formater la partition EFI (`/dev/sda1`) !

---

## 4️⃣ Démarrage post-installation

- Redémarrer et vérifier le menu GRUB :
  - Tsurugi Linux
  - Windows Boot Manager

Si Windows ne s’affiche pas :  
```bash
sudo update-grub
```

---

## 🛠️ Conseils et vérifications

### 📌 Après installation de Tsurugi
Assure-toi que `os-prober` est installé pour détecter Windows :

```bash
sudo apt install os-prober
sudo os-prober
sudo update-grub
```
