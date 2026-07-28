README

Premier Script

# 📻 YouTube Retro Recommendations (2009-2015)

Un Userscript pour **Tampermonkey** / **Greasemonkey** qui remplace l'intégralité des recommandations modernes de YouTube et de l'extension **7ktTube** par une sélection de **~100 vidéos rétro** publiées entre **2009 et 2015**.

---

## 🚀 Fonctionnalités

- **100 % Gratuit & Sans Clé API :** Fonctionne de manière autonome sans aucune configuration Google, sans compte développeur et sans limite de quota quotidien.
- **Remplacement Total :** Masque automatiquement les vidéos modernes recommandées par YouTube pour ne laisser **que** du contenu rétro.
- **Support Double Page :**
  - **Sur la page d'accueil :** Affiche une grille défilante de vignettes rétro basées sur des thèmes populaires de l'époque.
  - **Sur la page de visionnage (`/watch`) :** Affiche une colonne défilante de vidéos rétro en lien direct avec le sujet de la vidéo consultée.
- **Scraping Multi-requêtes Parallèle :** Lance 5 recherches simultanées découpées par tranches d'années pour cumuler jusqu'à **100 vidéos uniques** (sans doublons).
- **Intégration Parfaite avec 7ktTube :** S'adapte dynamiquement aux conteneurs du thème rétro.

---

## 🛠️ Installation

1. Installez une extension de gestionnaires de scripts sur votre navigateur (ex: [Tampermonkey](https://www.tampermonkey.net/)).
2. Ouvrez le tableau de bord de Tampermonkey et cliquez sur **Créer un nouveau script**.
3. Copiez l'intégralité du code JavaScript de la **Version 9.0** et collez-le dans l'éditeur.
4. Enregistrez le script (`Ctrl + S` ou `Cmd + S`).
5. Rendez-vous sur YouTube. Lors du premier chargement, Tampermonkey peut vous demander l'autorisation d'effectuer une requête vers `www.youtube.com` : cliquez sur **"Toujours autoriser ce domaine"**.

---

## 🔍 Comment ça marche sous le capot ?

### 1. Extraction intelligente des sujets
* **En mode vidéo (`/watch`) :** Le script extrait le titre de la vidéo en cours, le nettoie des termes parasites (`YouTube`, `HD`, `Official`, etc.) et isole les 3 mots-clés les plus pertinents pour trouver des vidéos similaires de l'époque.
* **En mode accueil :** Il tire au sort parmi un lot de thématiques rétro populaires (*Minecraft*, *vlogs 2011*, *parodies*, *gaming*, etc.) pour diversifier le flux.

### 2. Contournement d'API via `GM_xmlhttpRequest`
Plutôt que de passer par l'API officielle de YouTube (restreinte et limitée), le script effectue des requêtes HTTP directes en arrière-plan vers le moteur de recherche de YouTube en injectant les filtres de dates natifs :  
`search_query = [Mots-clés] after:2008 before:2016`

### 3. Multiplexage pour atteindre ~100 vidéos
Une seule page de recherche YouTube ne renvoie que ~20 résultats. Pour contourner cette limite, le script exécute **5 requêtes en parallèle** en découpant la période 2008-2016 en plusieurs tranches :
- `2008` à `2010`
- `2010` à `2012`
- `2012` à `2014`
- `2014` à `2016`
- Recherche globale `2008` à `2016`

Tous les résultats sont rassemblés et filtrés via un objet `Set()` JavaScript pour éliminer les doublons, garantissant jusqu'à 100 vidéos uniques.

### 4. Parsing du JSON natif YouTube (`ytInitialData`)
Le script analyse la reponse HTML brute reçue pour y extraire la variable globale JavaScript `ytInitialData`. Il y récupère :
- L'ID de la vidéo (`videoId`)
- Le titre original
- Le nom de la chaîne
- La date de publication
- La miniature officielle stockée sur les serveurs d'images YouTube (`i.ytimg.com`)

### 5. Masquage continu et réinjection dans le DOM
- Le script injecte sa propre boîte (`.custom-api-recs-box`) équipée d'une barre de défilement CSS (`overflow-y: auto`).
- Un **`MutationObserver`** surveille le DOM en permanence : chaque fois que YouTube ou 7ktTube tente de charger ou de réinsérer des recommandations modernes, le script leur applique automatiquement un style `display: none !important`.

---

## ❓ Foire Aux Questions / Dépannage

### Le bloc reste bloqué sur "⏳ Chargement..."
- Vérifiez si Tampermonkey n'attend pas une validation de sécurité. Si une pop-up apparaît en haut de l'écran, autorisez les requêtes vers `www.youtube.com`.
- Rechargez la page (`F5`).

### Les vidéos d'origine de YouTube réapparaissent
- Assurez-vous qu'aucun autre script ne vient interférer avec le DOM.
- Le script utilise un observateur de mutations pour masquer les éléments natifs dès qu'ils apparaissent ; un rafraîchissement rapide de la page règle généralement les petits décalages de chargement réseau.
