# Comment j'utilise Claude — 30 derniers jours (31/07 → 30/08/2026)

Analyse basée sur des données mesurées : sessions Claude Code, branches Git,
scénarios Make (compte kone segba), listes et campagnes Brevo.

---

## 1. Ce que montrent les chiffres

### Les sessions

| Date | Session | Durée | Coût | Sortie |
|---|---|---|---|---|
| 17/08 | Application traduction FR-albanais | 5 min | 0,44 $ | rien — bloquée sur une question |
| 19/08 | Analyse CV + recherche emploi | 1 h 32 | **18,96 $** | 7 CV, 1 brouillon Gmail, 1 artifact — **jamais poussé** |
| 28/08 | Formation ChatGPT 40+ à 200 € | 3 h 46 | n.c. | session interrompue (conteneur injoignable) |
| 29/08 | Formation IA 40+ « Papiers Réglés » | 10 min | 2,51 $ | blueprint + landing — poussé |
| 29/08 | Activité récurrente 400 €/mois | 2 h 56 | 6,00 $ | 5 commits de stratégie — **bloquée**, en attente de tes stats TikTok |
| 02/08 | Automatisation commentaires Facebook | n.c. | n.c. | scénario Make — **en production** |

Environ **8 h 40 de session** et **≈ 28 $** mesurés sur la période.
La session CV du 19/08 pèse **67 % du coût** à elle seule (17 M de tokens de cache relus :
un seul fil très long plutôt que plusieurs sessions ciblées).

### Quatre constats

1. **Tout part du téléphone.** Six sessions sur six lancées depuis Android, souvent le soir
   (19 h – 23 h). Tu fais de la stratégie de fond sur un écran de 6 pouces.

2. **Tu utilises Claude comme stratège, pas comme ouvrier.** Sur 6 sessions, 5 produisent
   un document de réflexion (analyse d'offre, blueprint de formation, stratégie de revenu).
   Une seule a produit une machine qui tourne toute seule : le scénario Make du 2 août.

3. **Deux sessions sur six se sont arrêtées faute de données.** Le 29/08 attend tes stats
   TikTok (pays / âge / sexe / taux d'engagement) que tu dois relever à la main.
   Le 17/08 attend une réponse. Ces sessions coûtent leur temps de réflexion et ne
   produisent rien tant que tu ne reviens pas avec un chiffre.

4. **Rien n'est planifié.** Zéro routine configurée sur ton compte. Chaque session est
   déclenchée à la main, repart d'un dépôt Git servant d'atelier, et le résultat reste
   dans une branche `claude/*` jamais fusionnée. D'où le commit du 29/08 :
   *« Reprend la réflexion à zéro »* — tu as re-payé une analyse déjà faite.

---

## 2. Ce qui tourne déjà — et qui fuit

### Le scénario Make « Integration Facebook Pages » (créé le 02/08, actif)

Il répond automatiquement aux commentaires contenant `guide` / `prompt` / `boîte à outils`
sous les posts de la page **IA POUR 40/50 ANS**, avec le lien du lead magnet.

| Mesure | Valeur |
|---|---|
| Fréquence | toutes les 4 h (6 exécutions/jour) |
| Exécutions depuis le 2 août | 180 |
| Opérations consommées | **10 409** |
| Quota du forfait Core | **10 000 / mois** |
| Opérations par exécution | 92 le 22/08 → **115 le 30/08** |

**Le problème.** Le scénario ne réagit pas à un commentaire : il **relit toute la page**
(`ListPosts` → `ListComments` → dédoublonnage en datastore) six fois par jour, qu'il y ait
un commentaire ou non. Le coût grimpe donc **avec le volume de ta page** : +25 % d'opérations
en huit jours. Au rythme actuel (~660 op/jour), il consomme **~19 800 opérations/mois pour
un quota de 10 000**. Conséquence : le scénario s'arrête en cours de mois, en silence, et
les gens qui commentent « guide » ne reçoivent plus rien.

Effet secondaire : jusqu'à **4 h de latence** entre le commentaire et la réponse.

### La séquence Brevo (7 emails, 11/08 → 24/08)

| | |
|---|---|
| Format | 7 campagnes **classiques** (envois ponctuels), pas une automatisation |
| Cibles | listes 4 « IA 40+ » (58), 7 « 6 Prompts en Or » (11), 10 « mon diagnostic ia » (554) |
| Ouvertures uniques (liste 10) | 72 sur l'email 1 (≈ 20 %) → 53 sur l'email 7 (≈ 12 %) |
| Clics uniques, les 7 emails cumulés | **≈ 12** |
| Liste 11 « VSL CHATGPT 4 SEMAINES » | **0 inscrit** — tunnel préparé, jamais branché |

**Le problème.** Une campagne classique part une fois, à une date fixe, vers les gens déjà
présents. **Toute personne inscrite après le 24 août n'a reçu aucun des 7 emails.**
Le scénario Make capte des leads → ils atterrissent dans une liste où il ne se passe
plus rien. Le contenu est écrit, testé, envoyé — et il ne travaille plus.

---

## 3. Ce que tu dois automatiser en premier

### № 1 — Transformer la séquence des 7 emails en automatisation Brevo (~1 h)

Déclencheur : *contact ajouté à la liste 4, 7 ou 10* → les 7 emails s'enchaînent à J+0, J+2,
J+4, J+6, J+8, J+10, J+13. Le contenu existe déjà (campagnes 12 à 20), il n'y a qu'à le
recopier dans un workflow.

Pourquoi en premier : c'est le seul point où de l'argent se perd **par lead déjà capté**.
Chaque inscrit depuis le 24 août est un prospect qui n'a jamais vu l'offre. Coût : une heure.
Bénéfice : la séquence tourne pour toujours, sur chaque nouveau contact, sans toi.

À faire dans la foulée : renvoyer la séquence aux inscrits post-24/08, et brancher la liste 11
(vide) sur la page VSL.

### № 2 — Reconstruire le scénario Make en déclencheur instantané (~1 h, à faire le même jour)

Remplacer `ListPosts` + `ListComments` toutes les 4 h par le **webhook Facebook Pages**
(Watch Comments en mode instantané) → filtre mots-clés → réponse.

De ~660 opérations par jour à **~2 par commentaire réel**. Tu récupères ~95 % de ton quota
et tu passes de 4 h de latence à quelques secondes — au moment précis où la personne est
encore devant son téléphone. C'est une échéance, pas une amélioration : au rythme actuel le
scénario dépasse son quota et s'arrête sans prévenir.

### № 3 — Une routine hebdomadaire « tableau de bord » (~30 min)

Lundi 8 h, une session programmée qui rassemble en une page : ouvertures/clics/nouveaux
inscrits par liste Brevo, état et opérations consommées du scénario Make, trafic des pages
Netlify.

Pourquoi : deux de tes six sessions se sont arrêtées faute de chiffres que tu dois relever à
la main. Avec ce brief, tes sessions de stratégie **commencent** avec les données au lieu de
**s'arrêter** dessus.

### Et une habitude à changer, qui ne coûte rien

Fusionne tes branches `claude/*` dans `main` d'un seul dépôt-atelier. Aujourd'hui trois
branches coexistent sans jamais se rejoindre, et `main` n'a pas bougé depuis avril : chaque
session repart de zéro et te refacture une analyse déjà payée.

---

## 4. Ce qu'il ne faut PAS automatiser tout de suite

- **La création de contenu** (ebooks, formations, romans). Tu as déjà les skills ; le goulot
  d'étranglement n'est pas la production, c'est la distribution. Tu as plus d'offres écrites
  que de tunnels qui tournent.
- **La recherche d'emploi / les CV.** Session la plus chère du mois (19 $), mais ponctuelle.
  À automatiser seulement si la recherche est toujours active — auquel cas une routine
  hebdomadaire Indeed + brouillons Gmail vaut le coup.

---

## 5. En une phrase

Tu utilises Claude pour **penser** et presque jamais pour **faire tourner**. Les deux seules
machines que tu as construites sont en panne silencieuse : l'une brûle son quota à interroger
Facebook dans le vide, l'autre a cessé d'envoyer les emails qui vendent. Répare-les avant
d'écrire la formation suivante.
