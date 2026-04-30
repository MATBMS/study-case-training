# UML — Diagramme de Cas d'Utilisation — Énoncés des Exercices

Tous les exercices se déroulent au sein de la même entreprise fictive — **NovaTrade**, société de négoce B2B. Vous serez chaque fois reçu par un membre de l'équipe NovaTrade qui vous décrira son besoin avec ses propres mots ; à vous d'écouter, d'identifier les acteurs (*actors*), les cas d'utilisation (*use cases*), les frontières de système et les relations à mettre en œuvre, et de mobiliser les pages théoriques pour produire un diagramme correct.

> [!important] Le Diagramme de Cas d'Utilisation dans la chaîne d'analyse
> Le Diagramme de Cas d'Utilisation est le **deuxième maillon** de la chaîne d'analyse, juste après BPMN : on extrait des processus métier les **fonctionnalités** que le système doit offrir et on les attribue à des acteurs. Il définit le **périmètre fonctionnel** du système ; il ne décrit ni la dynamique interne (c'est le rôle du diagramme d'Activité) ni la structure des données (c'est le rôle du diagramme de Classes). Sa qualité conditionne tout l'aval.

---

## 🟢 Niveau Débutant

### Exercice 01 — Portail Libre-Service Client

Aurélie, product owner du futur Portail Client de NovaTrade, vous reçoit en réunion de cadrage. C'est elle qui pilote le projet ; elle doit fournir à son équipe IT une vision claire de ce que le portail devra offrir avant qu'aucune ligne de code ne soit écrite.

> « On va lancer un portail web en libre-service pour nos clients pros. Au lancement — version minimale — il leur permettra de faire trois choses, pas plus : parcourir notre catalogue produits, passer une nouvelle commande, et consulter l'historique de leurs commandes passées. C'est tout pour la V1, on garde ça simple. »
>
> « Bien sûr, ils doivent être connectés pour faire quoi que ce soit. La connexion fait *partie intégrante* de chacune des trois interactions — ce n'est pas un objectif en soi, personne ne va sur notre portail juste pour se connecter et puis partir. C'est une étape préalable obligatoire à chaque action métier. »
>
> « Mon équipe IT a besoin d'une cartographie visuelle de tout ça avant de commencer à concevoir les écrans. Ce qu'il leur faut, c'est un diagramme de cas d'utilisation qui montre clairement : qui interagit avec le système, ce qu'il peut y faire, et la frontière entre ce qui est dans notre périmètre et ce qui ne l'est pas. »

Votre mission est de modéliser cette première version du Portail Client sous forme d'un diagramme de cas d'utilisation (*Use Case Diagram*) UML, en faisant ressortir le statut particulier de la connexion par rapport aux trois fonctionnalités métier qu'elle conditionne.

---

### Exercice 02 — Ajouter un Second Acteur

Aurélie revient deux semaines plus tard avec une nouvelle qui change la donne pour le portail.

> « Le portail va finalement servir à plus de monde que prévu. On vient de nous confirmer que nos **fournisseurs** y auront accès aussi — pas pour les mêmes choses que nos clients, attention : leur besoin à eux est de soumettre leurs factures électroniquement et de vérifier où en est le paiement de chacune de leurs factures. Ils n'ont rien à faire dans notre catalogue, et ils ne passent évidemment pas de commandes — ces fonctions-là restent strictement réservées aux clients. »
>
> « Et tant qu'on y est, j'ai discuté avec l'équipe IT : il faut aussi un compte administrateur pour gérer les comptes utilisateurs (créer, suspendre, réinitialiser les mots de passe). L'admin n'a pas à toucher aux commandes ni aux factures — c'est juste de la gestion d'accès. »
>
> « Petit ajustement de présentation : pour ne pas surcharger le diagramme à ce stade, retire la connexion. On va la traiter pour l'instant comme une simple précondition textuelle commune sous le diagramme — "tous les utilisateurs doivent être connectés". On la réintroduira proprement plus tard, quand on aura un autre besoin transverse à factoriser ensemble. »

Votre mission est d'étendre le diagramme de l'Exercice 01 pour intégrer ces deux nouveaux acteurs et leurs cas d'utilisation propres, en remplaçant la modélisation explicite de la connexion par une précondition commune sous le diagramme.

---

### Exercice 03 — Rédiger une Description Textuelle de Cas d'Utilisation

Bruno, lead développeur dans l'équipe e-commerce de NovaTrade, regarde le diagramme produit à l'Exercice 02 et constate, comme souvent, qu'il n'a pas assez d'informations pour développer.

> « Le diagramme c'est très bien comme carte des fonctionnalités, mais à partir du moment où mon équipe doit coder, on a besoin de bien plus de détails. Pour chaque cas d'utilisation, il nous faut une *description textuelle* complète — qu'est-ce qui se passe étape par étape, qu'est-ce qui peut mal tourner, dans quel état le système est-il avant et après. Sans ça, chaque dev va inventer sa propre version, et on va se retrouver avec des comportements incohérents. »
>
> « Commence par le plus important — `Passer une commande`. Voilà comment ça doit fonctionner. Le client est connecté, son panier contient au moins un produit, et il a déjà une adresse de livraison enregistrée chez nous. Il regarde son panier, confirme l'adresse de livraison qu'il veut utiliser, et clique sur "Confirmer la commande". À partir de là, le système prend la main. Il vérifie que tous les articles sont bien en stock en quantité suffisante. Il vérifie ensuite que le total ne dépasse pas la limite de crédit qu'on a accordée à ce client. Si ces deux contrôles passent, le système crée la commande avec le statut "Confirmée", réserve le stock pour chaque ligne, envoie un courriel de confirmation au client, et lui affiche une page de confirmation avec le numéro de référence. »
>
> « Maintenant, deux choses peuvent mal tourner. Si un ou plusieurs articles sont en rupture de stock, on affiche un message au client listant ce qui n'est pas disponible et le stock actuel, et on s'arrête là — pas de commande créée. Si la limite de crédit est dépassée, on affiche un avertissement qui montre la limite et le total demandé, et là aussi on s'arrête. »
>
> « Petit détail métier qu'il faut documenter : la quantité minimale par ligne de commande est de 1 unité — pas 0, on n'accepte pas les lignes vides. Et le seuil de crédit qu'on vérifie, c'est exactement la limite stockée dans l'ERP, pas de dérogation manuelle au moment du paiement. »

Votre mission est de rédiger la description textuelle (*Textual Use Case Description*) complète du cas d'utilisation `Passer une commande`, en utilisant le modèle standard (identifiant, nom, acteur, préconditions, flux principal, flux alternatifs, postconditions, règles métier) et en restituant fidèlement chacun des éléments décrits par Bruno.

---

## 🟡 Niveau Intermédiaire

### Exercice 04 — Refactorisation avec Include

Vincent, architecte principal chez NovaTrade, vient de relire le diagramme et les descriptions textuelles produits aux Exercices 02 et 03. Il propose un refactoring.

> « Quand je relis les descriptions textuelles, je vois que tous nos cas d'utilisation métier — les six — commencent par la même phrase : "le client/fournisseur/admin doit être authentifié". Cette précondition se répète six fois. Et plus on va ajouter de cas d'utilisation, plus elle va se répéter. Or en UML, quand un comportement est *systématiquement* invoqué par d'autres, il y a un mécanisme exprès pour ça : la relation `«include»`. C'est exactement ce qu'on faisait à l'Exercice 01 avec la connexion, mais on l'avait retirée pour simplifier — il est temps de la réintroduire, proprement cette fois, et en l'étendant aux six cas d'utilisation, pas seulement à trois. »
>
> « Et pendant qu'on y est, on a un nouveau besoin transverse qui vient de tomber côté conformité : pour des raisons d'audit, **chaque cas d'utilisation qui modifie des données dans le système** doit obligatoirement écrire une entrée dans notre Journal d'Audit — qui est bien identifié comme une fonctionnalité technique du système. Concrètement, ça concerne `Passer une commande`, `Soumettre une facture`, et `Gérer les comptes utilisateurs`. Les autres — parcourir le catalogue, consulter l'historique, vérifier le statut de paiement — sont des opérations en lecture seule, elles ne modifient rien, donc elles n'ont aucune raison d'écrire dans le journal d'audit. »
>
> « Les deux préoccupations se modélisent de la même façon : on les sort comme des cas d'utilisation à part entière, et on les "branche" sur les cas métier qui en dépendent. Ils ne sont jamais associés directement à un acteur — personne ne se connecte juste pour se connecter, personne ne se rend dans le portail uniquement pour écrire dans le journal d'audit. »

Votre mission est de refactoriser le diagramme de l'Exercice 02 pour réintroduire `Se connecter` comme cas d'utilisation transverse inclus par tous les cas métier, et ajouter `Écrire dans le journal d'audit` inclus uniquement par les cas d'utilisation qui modifient des données — en accompagnant le diagramme d'une note expliquant pourquoi les cas en lecture seule ne sont pas concernés par l'audit.

---

### Exercice 05 — Modéliser un Comportement Optionnel avec Extend

Léna, product manager du module Paiement chez NovaTrade — déjà rencontrée sur le diagramme de séquence — vient vous voir avec deux fonctionnalités optionnelles à intégrer dans le flux de commande.

> « On a deux choses à ajouter au cas `Passer une commande`, et toutes les deux ont la particularité d'être *optionnelles* — c'est-à-dire qu'elles peuvent ou non se produire pendant le déroulement de la commande, sans qu'on puisse les promouvoir en étapes systématiques. »
>
> « **Première fonctionnalité : application d'un code promotionnel.** Quand un client passe une commande, il *peut* avoir reçu un code promo (par e-mail, sur les réseaux sociaux, peu importe). S'il en a un, il le saisit dans un champ pendant le processus. S'il n'en a pas, il ne se passe rien — pas d'erreur, pas de branche alternative, juste rien. C'est purement opportuniste. »
>
> « **Seconde fonctionnalité : suggestion de remise sur quantité.** Là, c'est différent : ce n'est pas le client qui déclenche, c'est *le système* qui le fait. Si pendant la commande, le total du panier dépasse 10 000 €, on propose automatiquement au client une remise sur quantité — typiquement quelques pour cent. Le client peut accepter ou refuser, mais c'est le système qui prend l'initiative de la proposer, pas lui. Si le total est inférieur à 10 000 €, le système ne propose simplement rien. »
>
> « Donc dans les deux cas : (a) c'est optionnel — pas de branche obligatoire à modéliser dans le flux principal — et (b) ça n'a de sens que dans le contexte de l'exécution de `Passer une commande`. Personne ne va dans le portail "appliquer un code promo" tout seul. C'est un comportement qui s'injecte dans la commande à un moment précis, pas un objectif autonome. En UML il y a une relation faite pour ça, c'est la `«extend»`. »

Votre mission est de modéliser ces deux extensions du cas d'utilisation `Passer une commande`, en faisant apparaître les points d'extension (*extension points*) — c'est-à-dire les endroits où l'extension se branche dans le cas de base — et les conditions de déclenchement, et en accompagnant le diagramme d'une note de deux phrases qui distingue les deux modes de déclenchement (par le client vs. par le système).

---

### Exercice 06 — Généralisation d'Acteurs et Frontières de Système

Vincent revient avec une revue du portail qui aboutit à deux changements structurels.

> « Premier point : depuis qu'on a lancé la V1, le marketing a segmenté nos clients en deux paliers. On a maintenant des **Clients Standard** et des **Clients Premium**. Le Premium, c'est exactement un Standard à qui on a accordé en plus une limite de crédit majorée, l'éligibilité à la remise sur quantité dont on parlait avec Léna, et l'accès à un nouveau cas d'utilisation : `Demander une consultation au gestionnaire de compte`. Tout ce qu'un Standard peut faire, un Premium le peut aussi — plus une chose en plus. C'est une relation de spécialisation pure, pas de hiérarchie organisationnelle. En UML il y a un mécanisme exprès pour ça, qui s'appelle la **Généralisation d'Acteurs** (*Actor Generalization*). »
>
> « Petit point d'attention : je ne veux pas qu'on introduise un acteur abstrait `Client` supplémentaire au-dessus des deux. Premium spécialise directement Standard — c'est plus économique et c'est exactement ce qu'on veut exprimer. La flèche de généralisation va du sous-type vers le parent ; donc Premium pointe vers Standard. »
>
> « Deuxième point, qui est architectural : on va déployer le portail comme **deux applications distinctes**. La partie publique côté clients et fournisseurs, qui s'appellera "Portail Client & Fournisseur", et la partie back-office côté admins, qui s'appellera "Back-Office Administrateur". Ce sont des frontières de déploiement différentes, des serveurs différents, des contextes de sécurité différents. Donc je veux qu'on le voie clairement sur le diagramme : deux frontières de système distinctes, chacune avec ses propres cas d'utilisation. Et profite-en pour ajouter un cas d'utilisation côté back-office qui était dans la backlog : `Générer un rapport d'utilisation` — utilisable par l'admin pour l'audit interne. »

Votre mission est de restructurer le diagramme pour refléter à la fois la spécialisation Standard ↔ Premium et la séparation en deux frontières de système indépendantes, en veillant à ce que la généralisation d'acteurs n'introduise pas de duplication d'associations.

---

## 🔴 Niveau Avancé

### Exercice 07 — Cadrage du Système Complet de Gestion des Commandes

Pauline, Directrice des Opérations chez NovaTrade — déjà rencontrée sur les diagrammes BPMN et de séquence — vous reçoit avec un mandat de la Direction Générale.

> « On démarre une refonte complète de notre Système de Gestion des Commandes — l'OMS pour *Order Management System*. C'est notre projet stratégique de l'année, et avant qu'on parte en conception, le Comité de Direction veut un diagramme de cas d'utilisation **complet** qui couvre tout le périmètre. C'est ce diagramme qui va permettre de valider qu'on est tous d'accord sur ce que le système doit offrir, et qu'on n'oublie rien d'important. »
>
> « Cinq grandes catégories d'acteurs interagissent avec l'OMS. Le **Client** — qui passe ses commandes et suit leurs livraisons. Le **Représentant Commercial** — qui crée et modifie des commandes au nom des clients (typiquement quand le client appelle le commercial directement plutôt que de passer par le portail), et qui peut accorder des remises manuelles dans certains cas. L'**Opérateur d'Entrepôt** — qui réceptionne les marchandises, met à jour les niveaux de stock, prépare les expéditions. Le **Contrôleur Financier** — qui valide les factures, approuve les paiements, génère des rapports financiers. Et puis on a aussi un acteur particulier : notre **Système ERP**, qui n'est pas humain — c'est un système externe avec lequel l'OMS dialogue en automatique pour synchroniser les niveaux de stock et confirmer les paiements. C'est ce qu'on appelle un acteur secondaire. »
>
> « En plus de ces cinq, on a un **Administrateur Système** qui gère les comptes utilisateurs et configure des seuils techniques d'approbation (par exemple le montant à partir duquel un paiement nécessite une double validation). »
>
> « Hors périmètre, je précise : la gestion de l'adresse de facturation client est traitée par un CRM séparé, donc ça ne nous regarde pas, et l'intégration avec les transporteurs est gérée par une plateforme logistique tierce — on lui passe le bon de livraison et c'est elle qui s'occupe du transporteur. Ces deux choses doivent être clairement marquées comme hors périmètre. »
>
> « Avant de dessiner, je veux qu'on parte d'un mémo de périmètre — acteurs, principaux cas d'utilisation, hors périmètre. Ensuite le diagramme. Et pour finir, une description textuelle complète pour le cas d'utilisation prioritaire qu'on a identifié avec le commercial : `Créer une commande au nom du Client` — celui qu'utilise le Représentant Commercial. »
>
> « Et profite des relations avancées qu'on a vues ensemble — au moins un `«include»`, un `«extend»` et une généralisation d'acteurs si tu en vois l'occasion — chacun justifié par une note. »

Votre mission est de produire le mémo de périmètre, le diagramme de cas d'utilisation complet de l'OMS NovaTrade, et la description textuelle complète du cas d'utilisation `Créer une commande au nom du Client`.

---

### Exercice 08 — Identifier les Exigences Manquantes

Khalid, analyste junior fraîchement arrivé dans l'équipe de Vincent, vous montre un brouillon de diagramme de cas d'utilisation pour l'OMS qu'il a produit en autonomie. Il vous le décrit oralement avec une certaine fierté.

> « Bon alors voilà, j'ai trois acteurs : `Client`, `Manager`, et `Système`. Le Client peut faire quatre choses : `Acheter un produit`, `Se connecter`, `Payer`, et `Obtenir un reçu`. Le Manager peut `Consulter les rapports`. Le Système peut `Envoyer un courriel`. Les trois acteurs sont connectés à `Se connecter` parce qu'ils doivent tous s'authentifier. J'ai aussi mis une flèche `«include»` qui part de `Se connecter` vers `Acheter un produit` — comme ça, quand on se connecte, ça inclut l'achat. Et le `Système` je l'ai mis à l'intérieur de la frontière du système avec les cas d'utilisation, parce que c'est un acteur du système. Voilà, qu'est-ce que tu en penses ? »

Votre mission est double : (1) produire une **analyse des écarts** (*gap analysis*) numérotée du brouillon de Khalid, en identifiant **au moins six problèmes** distincts (placement d'acteur, direction de relation, granularité de cas d'utilisation, couverture de périmètre, acteurs manquants, cas d'utilisation manquants, granularité technique vs. métier), avec pour chacun l'explication de l'erreur et la correction proposée ; (2) produire un diagramme corrigé pour l'OMS qui intègre les éléments manquants en s'appuyant sur le contexte NovaTrade établi à l'Exercice 07, et qui ajoute au moins une relation `«extend»` et une Généralisation d'Acteurs absentes du brouillon.

---

### Exercice 09 — Spécification Complète de Cas d'Utilisation pour le Périmètre Order-to-Cash

Vincent vous reçoit pour la **dernière itération** : produire la spécification de cas d'utilisation canonique du périmètre Order-to-Cash, qui servira de référence d'exigences à toute la suite de la conception (Diagrammes de Classes, de Séquence, de Machine à États).

> « C'est le document le plus important du projet. C'est lui qui va être lu par l'architecte qui va construire le diagramme de classes, par les développeurs qui vont écrire les diagrammes de séquence, par l'analyste qui va modéliser les cycles de vie en machines à états. Si quelque chose manque ici, ça manquera partout en aval. »
>
> « Le Comité de Direction veut un modèle à **deux niveaux**. Un diagramme de haut niveau, suffisamment dense pour qu'on puisse le présenter en 15 minutes au Comité — entre cinq et sept cas d'utilisation, tous repliés en grands objectifs métier (`Gérer les commandes`, `Gérer l'inventaire`, `Traiter les paiements`, etc.), avec tous les acteurs identifiés. Et puis un diagramme détaillé qui prend le seul domaine `Gérer les commandes` et le déploie en cas d'utilisation individuels — au minimum cinq cas distincts couvrant le cycle de vie d'une commande de bout en bout. C'est dans ce diagramme détaillé que tu pourras montrer les relations Include, Extend et Généralisation à plein. »
>
> « En plus du diagramme, je veux **trois descriptions textuelles complètes** — choisis les trois cas d'utilisation les plus importants du domaine commande. Chaque description doit avoir tout le canon : ID, nom, acteurs, préconditions, flux principal détaillé (au moins sept étapes), au moins deux flux alternatifs, postconditions de succès et d'échec, règles métier, et — c'est ce qu'on rate souvent — au moins une exigence non fonctionnelle (temps de réponse, disponibilité, contraintes de sécurité). »
>
> « Et un point critique : la **cohérence des noms**. Si un acteur s'appelle `Représentant Commercial` au Niveau 1, il doit s'appeler exactement pareil au Niveau 2 et dans les descriptions textuelles. Pareil pour les cas d'utilisation. Je veux qu'on puisse, sans ambiguïté, mapper chaque description textuelle à une boîte du diagramme. C'est cette discipline qui va éviter qu'on construise demain trois implémentations différentes pour la même fonctionnalité. »
>
> « Pour finir, joins une **note de mappage** par description textuelle, qui anticipe les éléments que je vais retrouver dans les diagrammes de classes et de séquence à venir — quelles classes vont émerger de la description, quelles opérations, quels messages clés dans la séquence. »

Votre mission est de produire cette spécification complète : le diagramme de Niveau 1, le diagramme détaillé de Niveau 2 sur `Gérer les commandes` (avec au moins un Include, un Extend, une Généralisation), trois descriptions textuelles complètes (chacune avec exigence non fonctionnelle), la vérification de cohérence des noms entre les deux niveaux, et les notes de mappage vers les diagrammes UML aval.

---

*Page suivante : [[UML Use Case Solutions]] — solutions modèles avec justifications.*
