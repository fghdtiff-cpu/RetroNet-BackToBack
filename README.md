README

Premier Script

# 📻 YouTube Retro Recommendations (Édition Redux & V3 Vorapis)

Un Userscript pour **Tampermonkey** / **Greasemonkey** qui remplace l'intégralité des recommandations modernes de YouTube par une sélection de **~100 vidéos rétro** publiées entre **2009 et 2015**. 

Cette version est **spécialement optimisée pour fonctionner avec YouTube Redux, V3 Vorapis et 7ktTube**, en s'adaptant à leurs modifications d'interface (design 2013-2015).

---

## 🚀 Fonctionnalités

- **Compatibilité Totale "Retro UI" :** S'intègre parfaitement dans les interfaces YouTube restaurées par Vorapis V3, YouTube Redux ou 7ktTube sans casser leur mise en page.
- **100 % Gratuit & Sans Clé API :** Fonctionne de manière autonome sans aucune configuration Google et sans limite de quota quotidien.
- **Remplacement Strict :** Masque agressivement (via des forçages CSS) les vidéos modernes injectées par YouTube ou par les extensions tierces pour ne laisser **que** du contenu rétro.
- **Support Double Page :**
  - **Sur la page d'accueil :** Affiche une grille de vignettes rétro basées sur des thèmes populaires de l'époque, avec sa propre barre de défilement.
  - **Sur la page de visionnage (`/watch`) :** Affiche une colonne défilante de vidéos rétro en lien direct avec le sujet de la vidéo que vous regardez.
- **Scraping Multi-requêtes Parallèle :** Lance 5 recherches simultanées découpées par tranches d'années pour cumuler jusqu'à **100 vidéos uniques** (sans doublons).

---

## 🛠️ Installation

1. Installez une extension de gestionnaires de scripts sur votre navigateur (ex: [Tampermonkey](https://www.tampermonkey.net/)).
2. Ouvrez le tableau de bord de Tampermonkey et cliquez sur **Créer un nouveau script**.
3. Copiez l'intégralité du code JavaScript de la **Version 10.0** et collez-le dans l'éditeur.
4. Enregistrez le script (`Ctrl + S` ou `Cmd + S`).
5. Rendez-vous sur YouTube. Lors du premier chargement, Tampermonkey peut vous demander l'autorisation d'effectuer une requête vers `www.youtube.com` : cliquez sur **"Toujours autoriser ce domaine"**.

---

## 🔍 Comment ça marche sous le capot ?

### 1. Extraction intelligente des sujets
* **En mode vidéo (`/watch`) :** Le script extrait le titre de la vidéo en cours, le nettoie des termes parasites (`YouTube`, `HD`, `Official`, etc.) et isole les 3 mots-clés les plus pertinents. Il gère les différents sélecteurs de titres modifiés par Redux et Vorapis.
* **En mode accueil :** Il tire au sort parmi un lot de thématiques rétro populaires (*Minecraft*, *vlogs 2011*, *parodies*, *gaming*, etc.) pour diversifier le flux.

### 2. Contournement d'API via `GM_xmlhttpRequest`
Le script effectue des requêtes HTTP directes en arrière-plan vers le moteur de recherche de YouTube en injectant les filtres de dates natifs :  
`search_query = [Mots-clés] after:2008 before:2016`

### 3. Multiplexage pour atteindre ~100 vidéos
Pour contourner la limite de ~20 résultats par page de recherche, le script exécute **5 requêtes en parallèle** en découpant la période 2008-2016 en tranches (2008-2010, 2010-2012, etc.). Tous les résultats sont filtrés via un objet `Set()` JavaScript pour éliminer les doublons.

### 4. Parsing du JSON natif YouTube
Le script analyse la reponse HTML brute reçue pour y extraire la variable globale `ytInitialData` et récupérer : IDs, titres originaux, noms de chaînes, dates de publication et miniatures officielles (`i.ytimg.com`).

### 5. Isolation CSS (Protection contre Vorapis/Redux)
Les extensions comme Vorapis V3 ou Redux injectent de lourdes feuilles de style (CSS) pour recréer l'aspect de YouTube en 2013. Pour éviter que ces anciens styles ne déforment la boîte de recommandations, **tous les éléments du script sont protégés par des balises `!important`**.

### 6. Masquage continu par `MutationObserver`
Un observateur surveille le DOM (Document Object Model) en permanence. Chaque fois que YouTube, Vorapis ou Redux tente de charger de nouvelles recommandations modernes (ex: chargement infini sur l'accueil), le script leur applique instantanément un style `display: none !important`.

---

## ❓ Foire Aux Questions / Dépannage

### Le bloc reste bloqué sur "⏳ Chargement..."
- Vérifiez si Tampermonkey n'attend pas une validation de sécurité. Si une pop-up apparaît, autorisez les requêtes vers `www.youtube.com`.
- Rechargez la page (`F5`).

### L'interface semble déformée ou les vidéos d'origine clignotent
- Si Vorapis V3 ou Redux met du temps à injecter son propre code, il peut y avoir un très léger délai. Le `MutationObserver` se charge de nettoyer l'écran en continu. Un changement rapide de page ou un rafraîchissement résout généralement la superposition.
