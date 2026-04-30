# Exercices — Diagramme de Classes UML

Cette page contient neuf exercices progressifs, tous situés dans **NovaTrade**, une société de négoce B2B de taille moyenne. Les exercices se concentrent sur le diagramme de classes (*Class Diagram*) UML, partant d'une seule classe bien formée pour aboutir à un modèle de domaine complet avec les six types de relations, une classe abstraite (*Abstract Class*) et une interface (*Interface*) — cohérent avec le vocabulaire établi dans [[UML Use Case Exercices]] et [[UML Activity Exercices]].

Chaque exercice est autonome mais s'appuie sur le précédent. Les débutants devraient compléter les trois exercices de niveau débutant avant de passer au niveau intermédiaire.

## 🟢 Niveau Débutant

*Les exercices débutants se concentrent sur les briques de base : une seule classe avec les compartiments corrects, les types d'attributs, les marqueurs de visibilité et les opérations. Pas encore de relations.*

### Exercice 01 — Modéliser une Classe Unique

L'équipe de développement de NovaTrade démarre la conception du Système de Gestion des Commandes. Avant de dessiner la moindre relation, chaque classe doit être correctement spécifiée isolément. Marie, responsable catalogue, vient vous décrire ses besoins pour la classe la plus référencée du futur système — le `Produit` :

> « Pour chaque article qu'on vend, j'ai besoin de pouvoir le retrouver rapidement, donc il lui faut un identifiant unique — un code interne. Il y a évidemment un nom commercial, celui qu'on affiche sur le site, et une description plus longue qui détaille les caractéristiques. Côté commercial, on doit avoir un prix unitaire en euros et savoir combien d'unités on a en stock à tout instant. Chaque produit appartient aussi à une catégorie qu'on utilise pour classer le catalogue.
>
> Au quotidien, mes équipes doivent pouvoir : savoir si un produit est disponible avant de le proposer (réponse oui ou non, c'est tout), appliquer une remise commerciale en pourcentage sur le prix lors d'opérations promotionnelles, et déclarer un réapprovisionnement quand on reçoit une nouvelle palette pour augmenter le stock du nombre d'unités correspondant. »

À ce stade on reste au niveau analyse : pas encore d'encapsulation, donc tout est visible publiquement. Suivez les conventions UML standard : nom de classe en PascalCase, attributs et opérations en camelCase, et types primitifs en PascalCase (`String`, `Integer`, `Decimal`, `Boolean`).

Votre mission est de **lire la description de Marie** et d'en extraire la classe `Produit` avec sa structure à trois compartiments : tous les attributs (avec le type que vous jugez approprié pour chacun), toutes les opérations qu'elle demande (avec leurs signatures complètes — paramètres typés et type de retour explicite, en utilisant `void` quand rien n'est retourné).

### Exercice 02 — Attributs, Visibilité et Valeurs par Défaut

Sophie, responsable conformité chez NovaTrade, vous explique ce qu'elle attend de la classe `Client` (*Customer*) :

> « Pour chaque client, on stocke un identifiant interne, son prénom, son nom et son email. Mais attention, ces données sont sensibles : je veux qu'elles ne soient **jamais accessibles directement de l'extérieur de la classe**. Toute lecture passe par une méthode dédiée — une qui renvoie le nom complet (concaténation prénom + nom), une pour l'email, et une pour la limite de crédit. Pas d'accès direct aux champs.
>
> Côté règles métier : tout nouveau client commence en "Standard" à sa création, et il pourra devenir Premium plus tard via une promotion. Tous les clients sont actifs à la création ; on peut suspendre un compte plus tard si quelque chose tourne mal. La limite de crédit est à zéro à la création — on n'accorde pas de crédit "à l'aveugle" tant qu'aucune vérification financière n'a été faite. Ces trois règles doivent apparaître **explicitement** comme valeurs par défaut sur les attributs concernés.
>
> Pour les opérations métier : la classe doit pouvoir faire passer un client en Premium quand il atteint un certain seuil, suspendre un compte à la demande, et permettre au client de passer une commande à partir de son panier — cette dernière opération doit retourner la commande qui a été créée (oui, elle dépend implicitement de classes `Panier` et `Commande` qu'on définira ailleurs).
>
> Et un dernier point pour l'audit : j'ai besoin d'un mécanisme interne pour journaliser tout événement (par exemple un changement de statut) avec un libellé d'événement en paramètre. Mais cette opération ne doit pas être accessible au reste du système — uniquement aux **sous-classes** futures comme par exemple un `ClientPremium` qui pourrait avoir besoin de logger ses propres événements. C'est important : ni privé, ni public, mais entre les deux. »

UML offre exactement ce qu'il faut pour traduire les trois niveaux d'accès demandés par Sophie : `-` pour ce qui ne sort pas de la classe, `+` pour l'interface publique, `#` pour ce qui est partagé uniquement avec les sous-classes. Les **valeurs par défaut** s'écrivent à la suite du type sous la forme `= false`, `= true`, `= 0.00`.

Votre mission est d'extraire de la description de Sophie tous les attributs avec leur niveau de visibilité approprié, leurs types et leurs valeurs par défaut quand mentionnées, ainsi que toutes les opérations avec leur signature complète — en plaçant chacune dans le bon niveau de visibilité.

### Exercice 03 — Modéliser un Fragment à Trois Classes

Marc, chef de projet du module Commandes, vient vous voir avec le brouillon des règles métier qu'il a recueillies auprès des équipes commerciales et logistiques :

> « Au cœur de notre système, il y a le concept de Commande. Chaque commande a son propre identifiant, une date de création, un statut courant qui évolue au fil du processus (Brouillon, Confirmée, etc. — pour l'instant on garde ça simple, c'est juste un texte), et un montant total qu'il faut pouvoir recalculer à la demande. Une commande doit pouvoir être confirmée, annulée, et son total recalculé.
>
> Une commande contient des lignes — c'est la liste des articles commandés. Chaque ligne représente un article spécifique avec une certaine quantité et un prix unitaire **figé au moment de la commande** (très important : si le prix d'un produit change demain au catalogue, le prix sur les commandes passées ne bouge pas !). On doit pouvoir calculer le total d'une ligne — ce sera juste quantité × prix unitaire. Chaque ligne a son propre identifiant interne.
>
> Maintenant, les règles métier qu'il faut absolument respecter dans le modèle :
>
> - Une commande sans aucune ligne, ça n'a pas de sens — on doit garantir qu'il y a **au moins une ligne** par commande.
>
> - Une ligne ne peut pas exister toute seule dans le système : elle fait partie intégrante de sa commande. Si je supprime une commande, ses lignes disparaissent automatiquement avec elle, c'est non négociable.
>
> - À l'inverse, chaque ligne pointe vers un produit du catalogue (un seul, jamais deux). Le produit lui-même reste bien sûr dans le catalogue même si on supprime la ligne — on ne va pas effacer un produit du catalogue parce qu'on annule une commande, ce serait absurde !
>
> - Et un même produit peut apparaître dans plein de lignes différentes — on en vend des unités à différents clients, c'est normal.
>
> Côté Produit, le minimum nécessaire pour ce fragment : son identifiant, son nom, son prix unitaire actuel, la quantité en stock, et savoir s'il est en stock. »

À ce stade on reste au niveau analyse, donc tous les attributs et opérations sont publics (`+`). Le **vrai défi de cet exercice** est de lire attentivement les règles de Marc pour décider, pour chaque relation, du **type UML correct** (Association dirigée, Agrégation, ou Composition) et des **multiplicités** aux deux extrémités. Les indices sont dans son discours : « ça n'a pas de sens sans », « disparaissent avec elle », « au moins une », « un seul jamais deux », « peut apparaître dans plein de »…

Votre mission est de produire les trois classes avec leurs attributs et opérations, puis de transformer chaque règle métier énoncée par Marc en une relation UML correctement typée, dirigée, étiquetée et avec les bonnes multiplicités à chaque extrémité.

## 🟡 Niveau Intermédiaire

*Les exercices intermédiaires introduisent les six types de relations, les décisions Agrégation vs. Composition, et des diagrammes multi-classes nécessitant une multiplicité correcte partout. Chaque scénario présente un choix métier qui détermine quel type de relation est correct.*

### Exercice 04 — Choisir le Bon Type de Relation

L'architecte de NovaTrade vous présente cinq mini-scénarios issus de réunions de cadrage avec différentes équipes. Pour chacun, vous devez déterminer le **bon type de relation UML** entre Association, Agrégation, Composition, Dépendance, Généralisation ou Réalisation, et **justifier votre choix en une phrase** en pointant l'indice clé de la description. Pour les cas où c'est pertinent, indiquez aussi les **multiplicités** correctes aux deux extrémités.

**Scénario 1 — la responsable logistique :**
> « Notre Entrepôt principal détient des produits dans son inventaire — parfois quelques dizaines, parfois plusieurs milliers. Mais ce qu'il faut comprendre, c'est qu'un produit n'appartient pas à l'entrepôt : si on désaffecte un Entrepôt parce qu'on déménage, les produits sont simplement transférés ailleurs, ils ne disparaissent pas du catalogue. »

**Scénario 2 — la responsable comptable :**
> « Une Facture est constituée de ses lignes de facture. Sans aucune ligne, une facture n'a aucun sens — il faut au moins une ligne. Et le jour où je supprime une facture du système, je supprime toutes ses lignes en même temps, c'est cohérent : une ligne de facture appartient à sa facture, point. »

**Scénario 3 — le développeur du module reporting :**
> « Mon générateur de rapports a besoin d'un objet "intervalle de dates" pour savoir sur quelle période générer le rapport. Mais cet intervalle, on le lui passe **uniquement comme paramètre** quand on appelle la méthode `genererRapport()` — il ne le stocke pas dans un attribut, il l'utilise puis l'oublie. »

**Scénario 4 — l'architecte fonctionnel :**
> « Un Client Premium, c'est un Client. Tout simplement. Il hérite de tous les attributs et de toutes les opérations d'un Client de base, et il en ajoute quelques-uns à lui, comme une limite de crédit augmentée. »

**Scénario 5 — l'architecte technique :**
> « On a deux moyens de paiement aujourd'hui : `PaiementStripe` (pour les cartes bancaires) et `VirementBancaire`. Les deux s'engagent à fournir exactement les mêmes opérations standard — autoriser, capturer, rembourser — pour qu'on puisse les utiliser de manière interchangeable dans le code de paiement. Cette liste d'opérations est définie dans une **interface** technique appelée `ProcesseurPaiement`. »

Votre mission est de dessiner les cinq relations dans des blocs séparés, avec le bon type, la bonne direction des flèches, les multiplicités quand c'est pertinent, et une **justification d'une phrase** par relation pointant l'indice clé du discours.

### Exercice 05 — Le Fragment du Domaine Facture et Paiement

Sophie revient vous voir, cette fois avec un cadrage plus large : il faut modéliser tout le fragment **Facture–Paiement** du domaine. C'est le morceau le plus riche en types de relations. Voici sa note de cadrage :

> « Quand une commande est validée, elle peut donner lieu à une facture — mais pas systématiquement (par exemple les commandes annulées avant émission n'en génèrent jamais). Donc une commande peut générer une facture, ou aucune. Pour modéliser une facture, j'ai besoin d'un identifiant, de la date d'émission, de la date d'échéance, du montant total et du statut courant. On doit pouvoir la marquer comme payée.
>
> Une facture est composée de ses lignes — c'est exactement la même logique que les lignes de commande qu'on a vues : il faut au moins une ligne par facture, et si je supprime la facture, ses lignes disparaissent avec elle. Chaque ligne de facture a un identifiant, une description courte, un montant, et on peut calculer son total.
>
> Maintenant, le lien avec les commandes : chaque ligne de facture est **issue d'**une ligne de commande spécifique — c'est ce qui assure la traçabilité comptable. On doit pouvoir naviguer de la ligne de facture vers la ligne de commande qui l'a générée. **Mais attention** : si on supprime une ligne de facture (par exemple lors d'une correction), la ligne de commande correspondante reste intacte — la commande d'origine n'est pas modifiée pour autant.
>
> Pour le paiement : une facture est réglée par exactement un paiement, et un paiement règle exactement une facture aujourd'hui. Mais important : on se réserve la possibilité dans le futur qu'un même paiement règle plusieurs factures (par exemple un virement groupé). Donc le paiement doit exister **indépendamment** de la facture — il n'en est pas une "partie", c'est un objet à part.
>
> Côté types de paiement, on accepte deux moyens : `PaiementStripe` (carte bancaire) et `VirementBancaire`. Tous les deux ont en commun un identifiant de paiement, un montant et un statut. Tous les deux doivent pouvoir traiter le paiement et procéder à un remboursement. Mais le mécanisme concret de "traiter un paiement" est complètement différent entre Stripe (token, API REST, webhooks) et un virement bancaire (IBAN, fichier SEPA) — donc cette opération doit obligatoirement être implémentée séparément par chaque type concret. La classe parente `Paiement` ne doit jamais être instanciée toute seule : on a toujours soit un Stripe soit un Virement.
>
> Stripe a son propre attribut `tokenStripe` ; un virement a son `iban`. Et techniquement, ces deux types de paiement implémentent une **interface** technique nommée `ProcesseurPaiement`, qui formalise le contrat de paiement : autoriser un montant (réponse oui/non), capturer la somme, et rembourser un montant. C'est ce contrat que le reste du code utilisera pour invoquer le paiement de manière interchangeable. »

Votre mission est d'extraire de cette description les **classes concrètes**, la **classe abstraite** (avec son opération abstraite explicite), l'**interface** (avec ses opérations), et toutes les **relations** entre elles — Composition, Association dirigée, Généralisation, Réalisation — chacune avec ses multiplicités correctes aux deux extrémités et une étiquette pertinente. Soyez attentif aux **indices de cycle de vie** dans le discours de Sophie qui distinguent une Composition d'une Association.

### Exercice 06 — Hiérarchie de Généralisation et Classes Abstraites

L'équipe Communication veut centraliser la gestion des notifications envoyées aux clients — actuellement c'est éparpillé dans plusieurs modules avec du code dupliqué. Antoine, l'architecte logiciel, vous présente sa vision :

> « Aujourd'hui on envoie trois types de notifications : par email, par SMS, et bientôt par notification push pour notre application mobile. À chaque envoi, on traite à peu près les mêmes informations communes : qui est le destinataire, quel est le sujet (utilisé surtout pour les emails mais on le garde pour homogénéiser), le corps du message, l'horodatage de l'envoi, et un statut de livraison. Et à chaque fois, on a besoin des mêmes opérations de base : envoyer la notification, l'annuler avant envoi si nécessaire, et consulter son statut courant. Chaque notification a aussi son propre identifiant interne pour la traçabilité.
>
> Le souci, c'est que chaque type a aussi des spécificités. Un email a une liste de destinataires en copie et un nombre de pièces jointes. Un SMS a un numéro de téléphone et est limité à un nombre maximum de caractères. Un push a un jeton d'appareil et un niveau de priorité. Et surtout — point crucial — **la manière d'envoyer est complètement différente** entre un email (passage par notre serveur SMTP), un SMS (appel à l'API Twilio) et un push (Firebase). C'est trois mécanismes techniques distincts.
>
> Ce que je veux : factoriser ce qui est commun dans une **classe parente** qui ne soit jamais instanciée toute seule — une "notification" sans type concret, ça n'a aucun sens. Et l'opération d'envoi doit obligatoirement être redéfinie par chaque type, c'est non négociable, parce qu'il n'y a pas de comportement par défaut commun pour l'envoi. À l'inverse, l'annulation et la consultation du statut peuvent rester avec une implémentation commune dans la classe parente.
>
> Par ailleurs, j'ai un autre besoin technique transverse : **les trois types concrets** doivent tous fournir une opération `livrer()` qui retourne un booléen indiquant le succès ou l'échec de la livraison effective. Cette opération est différente de "envoyer" — c'est une notion de **canal de livraison**, plus bas niveau, qui sera utilisée par notre couche de monitoring. Je veux que ce contrat technique soit exprimé dans une **interface** séparée appelée `CanalNotification`, et que ce soient les **trois types concrets** qui s'engagent à l'implémenter — pas la classe parente. C'est important parce que c'est le canal de livraison qui implémente cette logique, pas le concept abstrait de notification.
>
> Dernière chose : ces notifications sont déclenchées par les commandes (confirmation, expédition, retard de paiement, etc.). Une commande peut donc déclencher zéro, une ou plusieurs notifications dans son cycle de vie — pensez à faire le lien sur le diagramme avec une étiquette claire. »

Votre mission est d'extraire de la description d'Antoine la **classe parente abstraite** (avec son opération abstraite explicite — pensez à la marquer correctement), les **trois sous-classes concrètes** (sans répéter les attributs hérités), l'**interface** technique réalisée par chaque type concret, et la relation entre `Commande` et la hiérarchie de notifications avec ses multiplicités.

## 🔴 Niveau Avancé

*Les exercices avancés exigent le modèle de domaine NovaTrade complet, le diagnostic et la correction d'un diagramme défectueux, et la production d'un Diagramme de Classes entièrement spécifié servant de référence vocabulaire pour les diagrammes de Séquence et de Machine à États.*

### Exercice 07 — Construire le Modèle de Domaine NovaTrade Complet

Le comité de pilotage a validé la spécification fonctionnelle du Système de Gestion des Commandes. Avant de lancer le développement, l'architecte principal vous demande de produire le **modèle de domaine complet**. Il vous brief en réunion :

> « On a beaucoup de classes à mettre ensemble — celles qu'on a vues dans les exercices précédents — donc pour rester lisible je veux **deux vues séparées** sur la même page, l'une sur le bloc Client/Commande, l'autre sur le bloc Facture/Paiement. Et bien sûr, il faut que le tout soit cohérent.
>
> **Première vue — Client et Commande.** Au cœur de cette vue, le concept de Client. On distingue désormais deux types : le **Client Standard**, qui accumule des points fidélité, et le **Client Premium**, qui a une limite de crédit augmentée, un gestionnaire de compte attitré (juste son nom pour l'instant), et qui peut demander une livraison prioritaire. Mais attention : on a aussi des "vieux" clients dans la base qui ne sont ni Standard ni Premium — ils ont été migrés depuis l'ancien système et on ne veut pas casser cette donnée historique. Donc le `Client` parent **doit rester instanciable directement**, on n'en fait pas une classe abstraite — c'est une décision consciente.
>
> Le client a un identifiant, un email, un drapeau qui dit s'il est Premium (par défaut non), et il peut passer une commande à partir d'un panier — opération qui retourne la commande créée.
>
> Côté flux d'achat : un Client peut **avoir** un Panier à un instant T, ou pas (s'il n'en a pas encore créé, ou s'il vient de l'abandonner). Important : un panier est un concept **éphémère**. Il peut être abandonné, recréé, vidé sans que ça affecte le Client lui-même. C'est pour ça que je ne veux pas qu'on lie le panier au client comme une "partie inséparable" — la suppression d'un panier ne doit jamais menacer l'existence du client. Le panier a un identifiant, une date de création, et permet d'ajouter ou retirer des articles, et d'obtenir le total. Il contient des produits du catalogue (au moins un, sinon il n'a pas d'intérêt) — et bien sûr ces produits restent dans le catalogue indépendamment du panier.
>
> Quand le client passe la commande à partir du panier, on crée une `Commande`. Une commande appartient à un client, et un client peut avoir passé plusieurs commandes au fil du temps. Une commande est composée de ses lignes (au moins une, et elles meurent avec la commande comme on l'a vu à l'Ex 03). Chaque ligne pointe vers le produit catalogue.
>
> **Deuxième vue — Facture et Paiement.** Cette vue reprend tout le fragment Facture/Paiement de l'Ex 05 — `Facture`, `LigneFacture`, `LigneCommande` (côté facture), `Paiement` abstraite, `PaiementStripe`, `VirementBancaire`, l'interface `ProcesseurPaiement` — plus la hiérarchie de Notifications de l'Ex 06 avec les types Email et SMS. Laissons Push de côté ici pour ne pas surcharger la vue.
>
> **Important** : la classe `Commande` apparaît dans cette deuxième vue **uniquement comme référence**, juste son identifiant et son statut. C'est elle qui sert de point de jonction entre les deux vues — c'est par elle qu'on passe de l'une à l'autre. Mettez-moi sur le diagramme une **note explicite** sur l'association `Commande → Facture` qui signale que c'est le **point de couture** entre les deux vues.
>
> Dernière chose : certaines décisions méritent d'être justifiées explicitement dans un encadré d'avertissement, parce qu'elles ne sont pas évidentes au premier coup d'œil. Au minimum trois : pourquoi `Client` est concret et pas abstrait, pourquoi le lien Client↔Panier n'est pas une Composition, et pourquoi le lien Facture↔Paiement n'est pas non plus une Composition. Une à deux phrases par décision suffisent. »

Votre mission est de produire les **deux vues** du modèle de domaine avec toutes les classes correctement annotées (concrète, abstraite, interface), tous les attributs essentiels typés, toutes les opérations métier importantes avec leurs signatures, et toutes les relations correctement typées avec multiplicités et étiquettes. Ajoutez la **note de frontière** sur la jonction Commande↔Facture, et le **callout `[!warning]`** documentant au moins trois décisions de modélisation.

### Exercice 08 — Diagnostiquer et Corriger un Diagramme de Classes Défectueux

Vous êtes architecte senior chez NovaTrade. Un analyste junior, Jordan, vous soumet pour revue son premier brouillon du fragment de domaine **Gestion des Fournisseurs**. Voici comment il vous le présente, fier de son travail :

> « J'ai modélisé quatre classes pour la gestion des fournisseurs : `Fournisseur`, `contactFournisseur`, `BonCommande` et `traiteurCommande`.
>
> - `Fournisseur` a comme attributs `nom`, `emailContact` et `commandes`.
> - `contactFournisseur` a `nom` et `telephone`.
> - `BonCommande` a `id`, `Date`, `articles` et `approuve`.
> - `traiteurCommande` a trois opérations : `traiterCommande()`, `valider()` et `envoyerAuFournisseur()`.
>
> Côté relations, j'ai mis une **Composition** entre `Fournisseur` et `contactFournisseur` (avec le losange creux côté Fournisseur, c'est ça qui veut dire Composition non ?). J'ai aussi une **Généralisation** de `BonCommande` vers `Fournisseur` parce qu'un bon de commande a forcément un fournisseur derrière. Et `traiteurCommande` est connecté à `BonCommande` par une **Dépendance** que j'ai dessinée avec une flèche pleine. Je n'ai pas mis de multiplicité — c'est nécessaire ? »

En relisant attentivement la description de Jordan, vous identifiez **plusieurs catégories d'erreurs** : conventions de nommage non respectées (PascalCase pour classes, camelCase pour attributs), attributs qui devraient être des relations, types d'attributs absents ou incorrects, mauvais choix de type de relation, mauvais remplissage de losange, multiplicités totalement absentes, sens de Généralisation aberrant sémantiquement, et même une classe nommée d'une manière qui suggère qu'elle ne représente pas un concept métier.

Pour produire le **diagramme corrigé**, gardez en tête le contexte NovaTrade : un Fournisseur est une entreprise partenaire avec un nom commercial, un email général, un pays, des opérations métier comme savoir s'il est approuvé ou le suspendre. Chaque Fournisseur a plusieurs **contacts** (personnes physiques chez ce fournisseur) avec un nom complet, un téléphone, un rôle — et un contact n'a évidemment aucun sens sans son fournisseur. Le Fournisseur **reçoit** des bons de commande de notre part : un bon a son propre identifiant, une date, un drapeau d'approbation, un montant total, et peut être approuvé ou rejeté. Et chaque bon de commande est composé de ses **lignes** — chaque ligne ayant une quantité, un prix unitaire, une méthode pour calculer son total et ajuster la quantité.

Concernant la classe `traiteurCommande` : une classe nommée avec un verbe décrit une **fonction**, pas un concept métier. Vous devez prendre position et **justifier en une phrase** : soit la renommer proprement (par ex. `ServiceBonCommande`) si vous estimez qu'une couche service est nécessaire, soit la supprimer du diagramme et réassigner ses opérations à la classe métier appropriée. Précisez à Jordan dans une note quelle décision vous prenez et pourquoi.

Chaque classe finale doit avoir **au moins quatre attributs typés** et **au moins deux opérations** avec signatures complètes. Les noms d'identifiants suivent la convention `id<NomClasse>` (par ex. `idFournisseur`).

Votre mission est de produire (a) une **analyse des écarts numérotée** identifiant chaque erreur du brouillon avec une explication d'une à deux phrases, (b) le **diagramme corrigé** avec quatre classes métier (attributs typés, opérations avec signatures, relations correctement typées avec multiplicités), (c) une **note finale** justifiant le sort réservé à `traiteurCommande`.

### Exercice 09 — Spécification de Classes Complète comme Référence Vocabulaire

L'architecte principal de NovaTrade vous reçoit pour la dernière étape de la conception. Il vous brief :

> « C'est l'exercice de consolidation : je veux que ce diagramme soit **la source de vérité** sur le vocabulaire du système. Tous les autres diagrammes — Séquence, Machine à États, schéma de Base de données — viendront le référencer. Donc reprenez **toutes** les classes du modèle de domaine qu'on a vues — Client et ses sous-types, Panier, Commande, LigneCommande, Produit, Facture, LigneFacture, Paiement et ses sous-types, ProcesseurPaiement, Notification et ses sous-types — et donnez-moi **la spécification complète** : tous les attributs avec leur type et leur visibilité, toutes les opérations avec leur signature complète (paramètres typés, type de retour).
>
> Deux points spécifiques sur lesquels je veux **vraiment** être explicite, parce qu'on a longtemps hésité :
>
> Premièrement, le statut de la commande. On a tergiversé pendant des semaines, mais au final j'ai tranché : ça doit être une **énumération typée** nommée `StatutCommande`, avec exactement six valeurs possibles : `BROUILLON`, `CONFIRMEE`, `EXPEDIEE`, `LIVREE`, `ANNULEE`, `CLOTUREE`. Pas un simple texte, pas un entier — une vraie énumération, qui doit apparaître **explicitement** sur le diagramme comme un bloc séparé, lié à la classe `Commande` par son attribut `statut`.
>
> Deuxièmement, même logique pour la facture : son attribut `statut` doit être typé par une énumération `StatutFacture` avec cinq valeurs : `BROUILLON`, `ENVOYEE`, `PAYEE`, `EN_RETARD`, `ANNULEE`. Encore un bloc d'énumération séparé sur le diagramme.
>
> Pour les classes `Commande` et `Facture`, ajoutez aussi une **note de référence croisée** pointant vers `[[UML State]]` (Machine à États), parce que c'est là que sera spécifié leur cycle de vie en détail — les diagrammes d'états documenteront les transitions entre les valeurs de ces énumérations.
>
> En plus du diagramme, je veux un **registre des classes** sous forme de tableau Markdown, avec quatre colonnes : nom de la classe, son type (concrète, abstraite, interface), ses attributs clés, et la liste des autres diagrammes (Cas d'Utilisation, Activité, Séquence, État, BDD) qui la référencent. C'est ce tableau qui me permettra plus tard de prioriser les classes à réviser quand les exigences changent — une classe référencée dans cinq diagrammes a un poids architectural beaucoup plus important qu'une classe référencée dans un seul. Couvrez au moins dix classes.
>
> Enfin — et c'est crucial pour la qualité du livrable — je vous demande de comparer les **opérations** de votre diagramme de classes avec les **messages** appelés dans `[[UML Sequence]]`. Quand une opération est invoquée comme message dans le diagramme de Séquence, elle doit exister dans le diagramme de Classes avec **exactement la même signature** — même nom, mêmes paramètres, même type de retour. Si vous trouvez des divergences (et il y en aura, on n'a jamais réussi à tout aligner du premier coup), listez-les dans un callout `[!warning]` avec, pour chaque divergence, une **recommandation d'une phrase** : modifier le diagramme de Classes ou modifier le diagramme de Séquence ? »

Votre mission est de produire (a) le **diagramme de classes complet** consolidant les deux vues de l'Exercice 07 avec spécification exhaustive de chaque classe, (b) les **deux énumérations** `StatutCommande` et `StatutFacture` comme blocs séparés, (c) les **notes de référence croisée** vers `[[UML State]]` sur `Commande` et `Facture`, (d) le **registre des classes** en tableau Markdown couvrant au moins dix classes, et (e) le **callout `[!warning]`** listant les divergences identifiées avec leurs recommandations de résolution.
