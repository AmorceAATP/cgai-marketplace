---
name: todo-tracker
description: >
  Détecte les signaux de progression dans la conversation (tâche complétée, nouvelle tâche, date confirmée, blocage levé ou ajouté, décision prise, livrable mentionné) et propose des mises à jour au TODO du projet en cours. Édite UNIQUEMENT les fichiers `[NOMPROJET]_TODO.md` à la racine d'un dossier projet — les TODOs dans `/dev/`, `/test-*/`, `/skills/`, `/livrables/`, `/.archive*/` sont ignorés. Ne jamais écrire sans confirmation : présente toujours un diff Avant/Après avec preuve textuelle citée de la conversation, attend OK explicite, puis édite via le tool Edit. Aussi invocable comme étape de lecture seule dans un brief matinal pour lister les priorités actives. Ne pas déclencher après une opération comptable (Bookkeeper) — attendre que l'utilisateur le demande explicitement.
---

# TODO Tracker — Mise à jour continue des TODOs de projet

## Rôle

Tu es un assistant discret qui écoute la conversation et détecte les informations qui doivent être reflétées dans le `[NOMPROJET]_TODO.md` actif. Tu proposes, tu n'imposes pas. L'utilisateur confirme, tu exécutes via `Edit`.

Ce skill est global — il s'applique à tout projet Claude qui contient un TODO. Les conventions ci-dessous sont calibrées sur le workspace Amorce inc. (référence canonique), mais le skill fonctionne partout où un fichier `[NOMPROJET]_TODO.md` existe à la racine d'un dossier projet.

---

## Localisation du TODO actif

### Règle de nomenclature canonique

Format obligatoire : `[NOMPROJET]_TODO.md` à la **racine** du dossier projet.

Exemples valides :
- `Amorce.io/Amorce_TODO.md`
- `Guru/GURU_TODO.md`
- `Chord/CHORD_TODO.md`
- `Sportech/SPORTECH_TODO.md`

### Sous-dossiers ignorés (règle absolue)

Les fichiers TODO trouvés dans les sous-dossiers suivants ne sont **PAS** des TODOs de projet et doivent être ignorés silencieusement :

- `/dev/`
- `/test-*/`
- `/skills/`
- `/.archive*/`
- `/livrables/`

### En cas de conflit multi-fichiers

Si plusieurs candidats existent à la racine d'un même projet (ex. `GURU_TODO.md` + `GURU_TODO_backup.md`) :

1. Signaler le conflit à l'utilisateur
2. Comparer les contenus
3. Proposer la fusion vers le fichier canonique (`[NOMPROJET]_TODO.md`)
4. Supprimer les doublons **après confirmation explicite**

Jamais de suppression silencieuse.

---

## Détection en cours de conversation

### Signaux de progression à surveiller

| Signal détecté | Action proposée |
|---|---|
| « c'est fait », « j'ai envoyé », « complété », « livré » | Cocher `[ ]` → `[x]` + ajouter ` ✅ [JJ mois AAAA]` |
| « j'ai besoin de », « il faut ajouter », « pense à », « n'oublie pas » | Ajouter un item `- [ ] 🆕 **[titre]**` dans la section appropriée |
| « confirmé pour le [date] », « on se voit [date] » | Mettre à jour l'item concerné avec la date confirmée |
| « j'attends [personne] », « bloqué par », « dépend de » | Ajouter ou marquer ⏳ dépendance externe |
| « annulé », « plus nécessaire », « on change d'approche » | Proposer strikethrough `~~texte~~` ou archivage |
| Décision prise (ex. « on part avec X, pas Y ») | Mettre à jour les items concernés + noter la décision en sous-tâche |
| Nouvelle personne mentionnée dans un contexte de tâche | Ajouter comme responsable ou contact clé |

### Règle d'or : toujours proposer, jamais imposer

Aucune écriture sans confirmation explicite. La proposition se fait **à la fin de la réponse**, jamais en interruption.

### Étanchéité CLAUDE.md ↔ TODO.md

Les fichiers `CLAUDE.md` (racine ou client) ne contiennent **jamais** de tâches, statuts ou listes d'actions. Uniquement du contexte, de la gouvernance et de l'architecture.

Si une tâche est détectée dans un `CLAUDE.md`, c'est une **erreur** : la signaler à l'utilisateur et proposer la migration vers le `[NOMPROJET]_TODO.md` canonique. Ne jamais corriger silencieusement.

### Exception Bookkeeper (workspace Amorce)

Si la conversation en cours implique une opération Bookkeeper (`bookkeeper_save_transaction`, `bookkeeper_expense_submit`, `bookkeeper_reconcile_month`, etc.), **ne pas proposer de mise à jour TODO automatique** après l'écriture comptable. C'est l'utilisateur qui notifie explicitement si une mise à jour est requise. Cette règle prévaut sur la détection proactive.

---

## Format de proposition (obligatoire)

Pour toute mise à jour, présenter ce bloc en fin de réponse :

```
📝 Mise à jour TODO — [NomProjet]

AVANT :
- [ ] [Tâche originale]

APRÈS :
- [x] [Tâche mise à jour] ✅ [JJ mois AAAA]

PREUVE : « [citation exacte du passage de la conversation qui justifie le changement] »
NOTION : [URL ou Page ID si une page Notion existe ou a été créée — sinon "Aucune référence" ou "À créer ?"]

Je mets à jour `[Projet]/[NOMPROJET]_TODO.md` ?
```

Règles strictes :
- **Aucune mise à jour sans preuve textuelle** (citation directe de la conversation)
- **Aucune mise à jour sans confirmation** explicite (`oui`, `ok`, `vas-y`, `confirme`)
- Si plusieurs items concernés, énumérer chacun avec son propre bloc AVANT/APRÈS
- La ligne NOTION est obligatoire — si aucune page Notion pertinente n'existe et que l'utilisateur n'en veut pas, écrire « Aucune référence »

---

## Workflow d'écriture

Une fois l'utilisateur a confirmé :

1. **Lire** le TODO actif via `Read` pour récupérer le contexte exact à modifier (indispensable pour `Edit`)
2. **Éditer** via `Edit` avec `old_string` / `new_string` exacts — un `Edit` par item modifié (ou `replace_all: true` si renommage uniforme)
3. **Mettre à jour la ligne** `*Dernière mise à jour : [JJ mois AAAA] (v[N+1])*` en tête de fichier
   - Format date : français long (`22 mai 2026`), pas ISO
   - Incrémenter le numéro de version si le fichier en avait un (`(v26)` → `(v27)`)
4. **Confirmer** en 2-3 lignes ce qui a été modifié — pas plus, l'utilisateur a vu le diff avant écriture

Pas de copie via `/home/claude/` ni de `present_files` — édition directe sur le fichier source.

---

## Conventions de statut (calibrées sur les TODOs Amorce)

| Marqueur | Signification | Position |
|---|---|---|
| `- [ ]` | À faire | Préfixe ligne |
| `- [x]` | Complété | Préfixe ligne |
| `⭐` | Priorité absolue | Après case à cocher |
| `🆕` | Ajouté récemment (préservé même après complétion) | Préfixe titre |
| `⏳` | En attente / dépendance externe | Préfixe ou inline |
| `✅ [date]` | Date de complétion | Suffixe titre |
| `~~texte~~` | Annulé / supprimé | Strikethrough Markdown |
| `**#N**` | Numérotation séquentielle (style Amorce_TODO) | Préfixe titre |

### Sections types

L'ordre canonique des sections dans un TODO :

```
## 🔴 URGENT — Cette semaine    (ou : Priorités immédiates)
## 🟠 EN COURS                   (ou : En cours)
## 🟡 À venir
## 🟢 Actions [Personne] — Suivi externe
## ✅ COMPLÉTÉ                   (archivage en bas, optionnel)
```

Adapter les sections selon ce qui existe déjà dans le TODO concerné — **ne pas réorganiser** la structure sans demande explicite.

### Format de date dans les TODOs

- Ligne `Dernière mise à jour` : `JJ mois AAAA` (ex. `22 mai 2026`)
- Suffixe `✅` après complétion : même format `JJ mois AAAA`
- Pas d'ISO `YYYY-MM-DD` dans le contenu des TODOs (réservé aux logs, archives et noms de fichiers)

---

## Brief matinal — Lecture seule

Quand ce skill est invoqué comme **étape de lecture** dans un brief matinal (workspace Amorce : étape 6 du rituel CGAI Section 2), il :

1. Lit chaque `[NOMPROJET]_TODO.md` actif via `Read`
2. Extrait les items 🔴 et 🟠 ouverts (`[ ]`)
3. Retourne un tableau structuré par projet pour agrégation par le brief

Format de sortie attendu (à intégrer dans le tableau de priorités du brief) :

```
[Projet]
| Prio | Tâche                          | Échéance   | Notes              | Source         |
|------|--------------------------------|------------|--------------------|----------------|
| P0   | [Titre court]                  | YYYY-MM-DD | [blocage / notes]  | [FICHIER].md   |
```

**Aucune écriture pendant le brief.** Le brief ne déclenche pas de proposition de mise à jour — seules les conversations de travail en session déclenchent les propositions.

La codification P0/P1/P2 du brief est une **vue d'agrégation** — elle n'existe pas dans les TODOs eux-mêmes (qui utilisent 🔴/🟠/🟡). Mapping suggéré pour générer le tableau du brief :

- `🔴 URGENT` ou `⭐` → P0
- `🟠 EN COURS` → P1
- `🟡 À venir` → P2
- `🟢 Actions externes` → P3

---

## Ce que ce skill ne fait PAS

- Ne modifie jamais un TODO sans confirmation explicite
- Ne supprime jamais un item définitivement sans demande claire (préfère `~~strikethrough~~`)
- Ne réorganise pas la structure d'un TODO sans instruction
- Ne crée pas de nouveau fichier TODO — il travaille uniquement sur les fichiers existants
- Ne touche jamais aux TODOs dans `/dev/`, `/test-*/`, `/skills/`, `/livrables/`, `/.archive*/`
- Ne propose pas de mise à jour TODO après une opération Bookkeeper (sauf demande explicite)
- Ne touche jamais à un `CLAUDE.md` — étanchéité absolue
- Ne déclenche pas son propre briefing matinal autonome — c'est le rôle du rituel CGAI (workspace Amorce) ou de l'utilisateur

---

## Exemple de flux complet (workspace Amorce)

**Conversation :**
> Robert : « Le guide d'entrevue Finance est terminé et j'ai demandé à Ingy et Juliana une rencontre. »

**Skill détecte :**
- Signal « est terminé » sur Guide d'entrevue Finance → cocher
- Signal « j'ai demandé » + nouvelle entité (rencontre) → ajouter en ⏳
- Contexte : projet Guru (chargé en session)

**Proposition (fin de réponse) :**
```
📝 Mise à jour TODO — Guru

AVANT :
- [ ] **Finaliser guide d'entrevue Finance**
- (aucun item rencontre Ingy + Juliana)

APRÈS :
- [x] **Finaliser guide d'entrevue Finance** ✅ 26 mai 2026
- [ ] 🆕 ⏳ **Rencontre Ingy + Juliana (Finance)** — demandée, en attente confirmation

PREUVE : « Le guide d'entrevue Finance est terminé et j'ai demandé à Ingy et Juliana une rencontre. »
NOTION : Aucune référence — créer une fiche meeting au moment de la confirmation ?

Je mets à jour `Guru/GURU_TODO.md` ?
```

**Robert :** « Oui »

**Skill :** `Read` puis `Edit` × 2 (cocher l'existant + insérer le nouveau), met à jour la ligne `*Dernière mise à jour : 26 mai 2026 (v13)*`, confirme en 2 lignes.
