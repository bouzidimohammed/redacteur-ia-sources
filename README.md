# Rédacteur IA sourcé

Agent d'IA qui recherche l'actualité dans une sélection de journaux choisie par l'utilisateur et rédige un article de blog **sourcé**, prêt à être publié sur WordPress.

Projet personnel, conçu pour alimenter un blog sur l'entrepreneuriat (premier cas d'usage : un nouveau statut d'auto-entrepreneur).

---

## Le besoin

Rédiger régulièrement des articles d'actualité fiables prend du temps : il faut chercher dans plusieurs journaux, recouper les informations, puis écrire un texte accessible. L'objectif est d'automatiser la recherche et le premier jet, **tout en gardant la maîtrise des sources**.

## Fonctionnement

```
Mots-clés + sujet
      │
      ▼
Agent LangChain (GPT-5.1) ──► outil « analyse_presse » ──► Tavily
      │    (plusieurs recherches)        limité aux journaux choisis 
      ▼
Article en Markdown + section Sources
      │
      ▼
Contrôle des URL consultées ──► export HTML pour WordPress
```

1. **Recherche ciblée** : l'outil interroge Tavily en se limitant aux domaines configurés (`include_domains`). La configuration actuelle utilise 7 journaux francophones et arabophones.
2. **Rédaction** : l'agent fait plusieurs recherches avec des mots-clés différents, puis rédige l'article selon des consignes éditoriales (ton, structure, langue).
3. **Vérification** : une cellule liste les URL réellement renvoyées à l'agent, pour contrôler l'origine de chaque information.
4. **Export** : l'article est converti en HTML, prêt à coller dans l'éditeur WordPress (Gutenberg).

## Garde-fous mis en place

Les premières versions produisaient des articles agréables à lire mais difficiles à vérifier. Les choix suivants viennent de l'analyse de ces résultats :

| Problème constaté | Correction |
|---|---|
| Chiffres sans source identifiable (le modèle complétait avec ses propres connaissances) | Consigne d'utiliser uniquement les faits renvoyés par l'outil |
| Références sans liens, impossibles à vérifier | L'outil transmet titre + URL ; section Sources obligatoire, sans URL inventée |
| Une seule recherche, sources peu variées | Consigne de multiplier les recherches avec des mots-clés différents |
| Mots en arabe dans un article en français | Règle de langue : tout traduire en français |
| Formules du type « selon la presse » | Style direct imposé, sources regroupées en fin d'article |
| Script cassé par des mises à jour de librairies | Versions encadrées dans `requirements.txt` |

## Limites connues

- **Relecture humaine indispensable** : le modèle peut encore mal recopier un chiffre. Les données clés doivent être vérifiées dans les articles sources avant publication.
- **Sources déséquilibrées** : avec des mots-clés en français, les résultats viennent surtout d'un seul journal. Les journaux arabophones nécessitent des mots-clés en arabe.
- **Résultats parfois hors sujet** remontés par la recherche (filtrés par le modèle, mais pas systématiquement).

## Pistes d'amélioration

- Mots-clés bilingues français / arabe pour équilibrer les sources
- Lecture du texte complet des articles (et non des seuls extraits) pour fiabiliser les chiffres
- Étape automatique de vérification : chaque chiffre de l'article doit apparaître dans une source
- Publication directe en brouillon via l'API WordPress

## Utilisation

1. Ouvrir `redacteur_presse.ipynb` dans [Google Colab](https://colab.research.google.com/) (*Fichier → Importer le notebook*, onglet GitHub).
2. Ajouter deux secrets Colab (icône 🔑) : `OPENAI_API_KEY` et `TAVILY_API_KEY`.
3. Adapter `JOURNAUX`, `MOTS_CLES` et `SUJET` dans la section « Paramètres ».
4. *Exécution → Tout exécuter*. Les fichiers `article.html` et `article.md` sont téléchargés à la fin.

Aucune clé n'est stockée dans le code : chaque utilisateur utilise ses propres clés.

## Stack

Python · LangChain 1.x · OpenAI (GPT-5.1) · Tavily Search API · Google Colab · WordPress
