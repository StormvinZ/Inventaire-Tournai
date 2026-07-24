# 📦 Inventaire déménagement

Application web mobile pour réaliser l'inventaire des meubles et objets d'une maison avant un déménagement, avec envoi automatique des photos dans les bons dossiers Google Drive.

Utilisable par plusieurs personnes en même temps, chacune sur son propre téléphone.

---

## ⚠️ Précision importante sur l'outil utilisé

Vous avez demandé d'intégrer un script **AppSheet**. En réalité, **AppSheet** est un outil qui construit sa propre application (avec sa propre interface), il ne peut pas servir de simple "backend" derrière une page HTML hébergée sur GitHub.

L'outil Google qui fait exactement ce qu'il vous faut ici s'appelle **Google Apps Script** : un petit script gratuit, hébergé par Google, qui reçoit les photos envoyées par l'application HTML et les dépose dans les dossiers Drive. C'est ce que ce dépôt utilise. Il fonctionne selon le même principe que vous décriviez : **une seule personne le configure une fois**, ensuite **aucun utilisateur n'a quoi que ce soit à configurer** — chacun ouvre juste le lien de l'application et indique son prénom.

---

## 📁 Contenu du dépôt

```
index.html              → l'application (à héberger sur GitHub Pages)
apps-script/Code.gs      → le script "backend" à déployer sur Google Apps Script
README.md                → ce guide
```

---

## 🧩 Étape 1 — Créer le script Google Apps Script (à faire une seule fois)

1. Rendez-vous sur **https://script.google.com** et connectez-vous avec le compte Google **propriétaire des dossiers Drive** (celui qui possède les 4 dossiers déjà créés).
2. Cliquez sur **Nouveau projet**.
3. Donnez-lui un nom, par exemple `Inventaire déménagement - backend`.
4. Supprimez le contenu par défaut du fichier `Code.gs` et collez à la place le contenu du fichier [`apps-script/Code.gs`](apps-script/Code.gs) de ce dépôt.
   - Les identifiants des 4 dossiers Drive que vous avez fournis y sont déjà renseignés (extraits automatiquement de vos liens `drive.google.com/drive/folders/...`).
5. Enregistrez (icône disquette ou `Ctrl+S`).

## 📊 Étape 1bis — Activer le tableau de suivi Google Sheets

Le script crée automatiquement, **sur le même compte Google**, un fichier **Google Sheets** nommé `Inventaire déménagement`, avec une ligne ajoutée à chaque photo envoyée : date/heure, prénom, pièce, action, nom de l'objet, nom du fichier, lien direct vers la photo dans Drive, et ID du fichier. C'est ce fichier qui vous donne l'**inventaire complet** en un coup d'œil (et qui peut être filtré, trié, exporté...).

Avant de déployer (étape suivante), il est recommandé d'exécuter le script une première fois manuellement, pour deux raisons : cela crée le Sheet tout de suite (au lieu d'attendre la première photo), et cela déclenche la demande d'autorisation d'accès à Google Sheets.

1. Dans l'éditeur Apps Script, en haut, choisissez la fonction **`setup`** dans le menu déroulant (à côté du bouton ▶️ Exécuter).
2. Cliquez sur **Exécuter**.
3. Autorisez les nouvelles permissions demandées (accès à Google Sheets), comme à l'étape 2 pour Drive.
4. Allez dans **Exécutions** (icône horloge à gauche) ou **Affichage → Journal d'exécution** : vous y trouverez l'URL du Sheet créé. Ouvrez-la et gardez-la de côté (vous pouvez aussi la retrouver directement dans votre Google Drive, fichier `Inventaire déménagement`).

> 💡 Vous pouvez aussi retrouver ce lien à tout moment en ouvrant, dans un navigateur, l'URL de votre déploiement suivie de `?info=1`, par exemple :
> `https://script.google.com/macros/s/XXXXXXXXXXXXXXXX/exec?info=1`

## 🚀 Étape 2 — Déployer le script en tant qu'application Web

1. En haut à droite, cliquez sur **Déployer** → **Nouveau déploiement**.
2. Cliquez sur l'icône ⚙️ à côté de "Sélectionner le type" et choisissez **Application Web**.
3. Renseignez :
   - **Exécuter en tant que** : `Moi` (votre compte).
   - **Qui a accès** : `Tout le monde` (indispensable : sans ce réglage, les utilisateurs ne pourront pas envoyer de photos).
4. Cliquez sur **Déployer**.
5. Google va demander une **autorisation** (accès à votre Drive) :
   - Cliquez sur **Autoriser l'accès**.
   - Un écran "Google n'a pas validé cette application" peut apparaître (normal pour un script personnel) → cliquez sur **Paramètres avancés** puis **Accéder à [nom du projet] (non sécurisé)**.
   - Acceptez les autorisations demandées (accès à Google Drive).
6. Une **URL de type** `https://script.google.com/macros/s/XXXXXXXXXXXXXXXX/exec` s'affiche. **Copiez-la précisément**, c'est elle qui relie l'application HTML à votre Drive.

> 💡 Vous pouvez tester tout de suite : collez cette URL dans un navigateur. Vous devez voir apparaître `{"ok":true,"message":"API Inventaire déménagement prête."}`.

### Si vous aviez déjà déployé le script avant l'ajout du Sheet
Remplacez le contenu de `Code.gs` par la nouvelle version, exécutez `setup` une fois (étape 1bis ci-dessus) pour autoriser l'accès à Sheets, puis créez une **nouvelle version** du déploiement existant (voir juste en dessous) — l'URL utilisée dans `index.html` ne change pas.

### Si vous modifiez le script plus tard
Toute modification de `Code.gs` nécessite de créer une **nouvelle version** du déploiement pour être prise en compte :
`Déployer` → `Gérer les déploiements` → icône crayon ✏️ → menu déroulant "Version" → `Nouvelle version` → `Déployer`.
L'URL reste identique, vous n'avez rien à changer côté application HTML.

---

## 🔗 Étape 3 — Relier l'application HTML au script

1. Ouvrez le fichier `index.html`.
2. Repérez, tout en haut du `<script>`, ce bloc :
   ```js
   const CONFIG = {
     SCRIPT_URL: "COLLE_ICI_L_URL_DE_TON_APPS_SCRIPT_/exec"
   };
   ```
3. Remplacez la valeur par l'URL copiée à l'étape précédente :
   ```js
   const CONFIG = {
     SCRIPT_URL: "https://script.google.com/macros/s/XXXXXXXXXXXXXXXX/exec"
   };
   ```
4. Enregistrez le fichier.

C'est la **seule** configuration technique nécessaire. Une fois faite, plus personne n'a besoin d'y retoucher.

---

## 🌐 Étape 4 — Héberger l'application sur GitHub Pages

1. Créez un dépôt GitHub (public ou privé, peu importe pour GitHub Pages gratuit sur un dépôt public).
2. Ajoutez-y les fichiers `index.html` (à la racine) — vous pouvez aussi garder le dossier `apps-script/` et ce `README.md` pour la documentation.
3. Allez dans **Settings** (Paramètres du dépôt) → **Pages**.
4. Dans **Source**, choisissez la branche `main` et le dossier `/ (root)`.
5. Cliquez sur **Save**. Au bout de quelques minutes, votre application sera accessible à une adresse du type :
   ```
   https://votre-nom-utilisateur.github.io/nom-du-depot/
   ```
6. Partagez ce lien avec toutes les personnes qui doivent faire l'inventaire (famille, aidants...). Chacun peut l'ouvrir en même temps sur son téléphone.

---

## 📱 Utilisation par les utilisateurs (aucune configuration requise)

1. Ouvrir le lien de l'application.
2. À la première ouverture, indiquer son **prénom** (mémorisé sur le téléphone, réutilisé automatiquement ensuite).
3. Choisir la **pièce** dans laquelle on se trouve.
4. Pour chaque meuble ou objet :
   - choisir ce qu'il doit devenir (donner, déchetterie, reprendre à Amiens, marraine Saint-Jean) ;
   - donner un nom à l'objet ;
   - appuyer sur **📷 Prendre une photo** (ouvre l'appareil photo) ou **🖼️ Choisir un fichier** (sélectionne une photo déjà prise) ;
   - la photo part automatiquement dans le bon dossier Google Drive, avec un nom de fichier qui contient la pièce, le nom de l'objet, le prénom et la date/heure (ex. `salon_canape-cuir_marie_2026-07-24_1432.jpg`).
5. Après l'envoi, deux boutons permettent soit de traiter un **autre objet dans la même pièce**, soit de revenir à l'**accueil** pour changer de pièce.

---

## 🔒 Confidentialité et sécurité à connaître

- Le script s'exécute avec les droits Drive de **votre** compte Google (celui qui a déployé le script), pas ceux des utilisateurs : c'est ce qui permet aux utilisateurs de ne rien configurer.
- Les 4 identifiants de dossiers sont fixés dans le script lui-même (`Code.gs`), pas modifiables depuis l'application : les photos ne peuvent partir que dans l'un de ces 4 dossiers.
- L'URL du script étant réglée sur "Tout le monde", toute personne qui la connaîtrait pourrait théoriquement y envoyer des fichiers. Ne publiez pas cette URL ailleurs que dans le code de l'application, et envisagez de la régénérer (nouveau déploiement) une fois le déménagement terminé si vous voulez fermer l'accès.

---

## 🛠️ Dépannage rapide

| Problème | Cause probable | Solution |
|---|---|---|
| Message "L'URL du script Google n'est pas configurée" | `CONFIG.SCRIPT_URL` non renseignée | Reprendre l'étape 3 |
| "Échec de l'envoi" | Le déploiement Apps Script n'est pas accessible à "Tout le monde" | Revérifier l'étape 2, point 3 |
| Erreur d'autorisation lors du déploiement | Compte Google différent de celui propriétaire des dossiers Drive | Redéployer avec le bon compte |
| La photo n'apparaît pas dans Drive | Mauvais `folderId` dans `Code.gs` | Vérifier les identifiants dans `FOLDERS` |
