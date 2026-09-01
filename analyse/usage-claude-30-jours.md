# Comment j'utilise Claude — 30 derniers jours (31/07 → 01/09/2026)

> **Version corrigée du 01/09/2026.** Les deux premières versions de ce document
> affirmaient que l'automatisation Brevo ne fonctionnait pas et que ~120 prospects
> n'avaient reçu aucun email. **C'était faux.** L'erreur, son origine et ce qu'elle
> coûte sont détaillés en section 2. Les mesures sur Make, elles, sont confirmées.

---

## 1. Ce que montrent les sessions

| Date | Session | Durée | Coût | Sortie |
|---|---|---|---|---|
| 17/08 | Application traduction FR-albanais | 5 min | 0,44 $ | bloquée sur une question |
| 19/08 | Analyse CV + recherche emploi | 1 h 32 | **18,96 $** | 7 CV + brouillon Gmail, jamais poussés |
| 28/08 | Formation ChatGPT 200 € | 3 h 46 | n.c. | session interrompue |
| 29/08 | Formation IA « Papiers Réglés » | 10 min | 2,51 $ | blueprint poussé |
| 29/08 | Activité récurrente 400 €/mois | 2 h 56 | 6,00 $ | 5 commits, bloquée sur stats TikTok |
| 02/08 | Automatisation commentaires Facebook | n.c. | n.c. | scénario Make en production |

Environ **8 h 40** et **28 $** mesurés. La session CV pèse 67 % du coût à elle seule.

### Quatre constats

1. **Tout part du téléphone**, souvent le soir. De la stratégie de fond sur un écran de 6 pouces.
2. **Claude est utilisé pour penser, pas pour faire tourner.** 5 sessions sur 6 produisent un
   document de réflexion ; une seule a produit une machine.
3. **Deux sessions sur six se sont arrêtées faute de données** à relever à la main.
4. **Aucune routine planifiée.** Chaque session est déclenchée manuellement, et son résultat
   reste dans une branche `claude/*` jamais fusionnée — d'où le commit « Reprend la réflexion
   à zéro » : une analyse re-payée.

---

## 2. Brevo : il n'y avait pas de panne

### Ce que disent les preuves

L'**Automatisation #4** (créée le 08/08, activée le 24/08) fonctionne et n'a pas cessé de
fonctionner. L'export des logs du scénario le montre sans ambiguïté :

| Date | Contacts entrés dans l'automatisation |
|---|---|
| 24/08 | 4 |
| 25/08 | 5 |
| 26/08 | 7 |
| 27/08 | 4 |
| 28/08 | 46 |
| 29/08 | 28 |
| 30/08 | 15 |
| 31/08 | 10 |
| 01/09 | 7 |
| **Total** | **126** |

Première entrée le 24/08 à 11 h 49, une heure et demie après l'activation. Depuis, chaque
nouveau lead y entre, jour et nuit. Sur la même période, 122 contacts sont arrivés dans la
liste 10 : **ce sont les mêmes personnes, il n'y a aucun écart, aucune période creuse.**

Et les emails partent bien. Recoupement entre les inscriptions Brevo et la boîte de réception :

| Personne | Inscription | Réponse reçue |
|---|---|---|
| alain nabaloum | 01/09 07 h 22 min 21 | 07 h 29 — « Re: **Bienvenue !** Je peux te poser 2 questions ? » |
| valery magonda | 31/08 18 h 43 min 24 | 31/08 — « Re: **Bienvenue !** … » |
| Mahamat Djidda | 30/08 19 h 59 min 52 | 31/08 — « Re: Merci pour votre message » |

Sept minutes entre l'inscription d'alain nabaloum et sa réponse à l'email de bienvenue.

La continuité est complète : les inscrits d'avant le 24/08 ont reçu les 7 campagnes classiques
du 11 au 24 août ; ceux d'après entrent dans l'automatisation. Personne n'est passé à travers.

### D'où venait l'erreur

Le diagnostic initial reposait sur l'endpoint « statistiques de campagne par contact » de
l'API Brevo. Cet endpoint **ne voit que les campagnes classiques** ; il est aveugle aux envois
d'automatisation. Comme il renvoyait vide pour tout le monde — y compris pour des gens qui
avaient reçu ET répondu à l'email de bienvenue — j'ai lu « rien n'est envoyé » là où il fallait
lire « je ne peux pas voir ».

La leçon vaut au-delà de Brevo : **un indicateur vide n'est pas une preuve d'absence.** Avant de
conclure à une panne, il faut vérifier que l'instrument mesure bien la chose observée. Ici, une
seule vérification par un autre canal — la boîte de réception — aurait suffi dès le premier jour.

### Ce qui reste vrai côté Brevo

- La liste 11 « VSL CHATGPT 4 SEMAINES » compte **0 inscrit** : tunnel préparé, jamais branché.
- Les listes 4 et 7 ne reçoivent plus personne depuis le 14/08 et le 30/07 ; tout le trafic
  arrive dans la liste 10. Sans conséquence, le déclencheur couvre les deux.
- **96 des 97 contacts récents examinés n'ont pas de PRENOM.** Toute personnalisation
  `{{ contact.PRENOM }}` sans valeur de repli affiche un blanc.
- Sur les 7 campagnes classiques d'août : ≈ 20 % d'ouverture au premier email, ≈ 12 % au
  septième, **≈ 12 clics uniques au total**. C'est le vrai sujet de travail — l'engagement,
  pas la plomberie.

---

## 3. Make : la seule panne réelle, mesurée et corrigée

Le scénario « Integration Facebook Pages » répond automatiquement aux commentaires contenant
`guide` / `prompt` / `boîte à outils` sur la page IA POUR 40/50 ANS.

| | Avant | Après |
|---|---|---|
| Opérations par passage | **115** | **45** |
| Passages par jour | 6 | 6 |
| Projection mensuelle | ~20 700 | **~8 100** |
| Quota du forfait Core | 10 000 | 10 000 |

**La cause principale : le scénario lisait ses propres réponses.** En mode « stream », le module
récupérait tous les commentaires, réponses imbriquées comprises — donc chaque réponse envoyée par
le robot devenait un commentaire de plus, relu à chaque passage, pour toujours. Le coût croissait
avec le succès (92 → 115 opérations en huit jours). Le passage en « toplevel » a supprimé cette
boucle : 86 → 50 opérations, mesuré.

**Un bug corrigé au passage :** l'ordre de lecture des commentaires n'était pas défini, donc
Facebook les renvoyait du plus ancien au plus récent. Avec un plafond de 20, sur un post
dépassant 20 commentaires, les nouveaux n'étaient jamais lus — plus un post marchait, moins ses
commentateurs recevaient le lien. Réglé en `reverse_chronological`.

**Réglage final :** 3 posts les plus récents, 10 commentaires les plus récents chacun, premier
niveau uniquement, toutes les 4 h. Mots-clés, message et dédoublonnage inchangés.

**Deux enseignements contre-intuitifs, tirés des mesures :**

- Passer de 5 à 3 posts surveillés n'a économisé que 5 opérations : les commentaires sont
  massivement concentrés sur les 3 publications les plus récentes.
- Le paramètre « nombre de commentaires par post » ne plafonne pas réellement (limite à 10,
  ~14 renvoyés). Seuls le nombre de posts et la fréquence ont un effet.

À ton volume, les 10 000 opérations du forfait Core restent la contrainte structurelle. Tant que
le scénario doit relire au lieu d'être notifié, il consommera 8 000 à 9 000 par mois.

---

## 4. Ce qu'il faut faire maintenant

### № 1 — Une routine hebdomadaire de mesure

C'est devenu la priorité, et pour une raison que cette analyse illustre elle-même : pendant deux
jours, ni toi ni moi ne savions si ta séquence email fonctionnait. Toi parce que tu n'as aucun
tableau de bord ; moi parce que je lisais le mauvais indicateur.

Une session programmée le lundi matin qui rassemble en une page : contacts entrés dans
l'Automatisation #4 sur 7 jours, ouvertures et clics de la séquence, opérations Make consommées
et état du scénario, trafic des pages Netlify. Et surtout **une vérification croisée** : un envoi
réel confirmé par un canal indépendant, pas par un compteur.

### № 2 — Les deux vrais défauts, petits et concrets

Ajouter une valeur de repli à toute personnalisation par prénom (`{{ contact.PRENOM | default : "" }}`),
puisque la quasi-totalité des contacts entrants n'en ont pas. Et brancher la liste 11 sur la page
VSL, ou la supprimer.

### № 3 — Le sujet de fond : l'engagement

12 clics uniques sur 7 emails envoyés à ~480 personnes, c'est le chiffre qui mérite du travail.
La machine tourne ; c'est ce qu'elle transporte qui doit progresser.

---

## 5. Ce qu'il ne faut PAS automatiser

- **La création de contenu.** Le goulot d'étranglement n'est pas la production : tu as plus
  d'offres écrites que de tunnels qui tournent.
- **La recherche d'emploi.** Session la plus chère du mois (19 $) mais ponctuelle. À automatiser
  seulement si la recherche est toujours active.

---

## 6. En une phrase

Tu utilises Claude pour penser et presque jamais pour faire tourner ; sur les deux machines que
tu as construites, une seule était en panne — celle que les chiffres montraient, pas celle que
je croyais.
