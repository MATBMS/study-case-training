# Étude de Cas — Système de Gestion des Commandes NovaTrade

NovaTrade est une entreprise commerciale B2B de taille moyenne qui s'approvisionne auprès de fournisseurs européens et revend ses produits à des clients professionnels — détaillants, grossistes et services achats. L'entreprise évolue sur un marché concurrentiel où la fiabilité, la traçabilité et la rapidité sont les principaux différenciateurs : un client qui reçoit le mauvais produit, une facture en retard ou une expédition retardée sans explication transférera son contrat à un concurrent dans le trimestre.

Aujourd'hui, les opérations centrales de NovaTrade — prendre les commandes, les exécuter et encaisser les paiements — sont gérées par une combinaison de fils de courriels, de feuilles de calcul partagées et d'un système de planification des ressources (*Enterprise Resource Planning*, ERP) partiellement configuré. Les commandes passées par téléphone sont resaisies manuellement. La disponibilité des stocks est vérifiée en appelant l'entrepôt. Les factures sont suivies sur un disque partagé auquel personne ne fait totalement confiance. L'équipe Finance ne découvre les paiements en retard que lorsqu'un client appelle pour contester une relance. Résultat : des commandes sont perdues, des factures sont dupliquées, et le Directeur des Opérations consacre quinze pour cent de sa semaine à résoudre des écarts qu'un système bien conçu éviterait entièrement.

Le projet auquel vous participez est la conception et la spécification du **Système de Gestion des Commandes (*Order Management System*, OMS) de NovaTrade** — une plateforme numérique qui centralisera l'ensemble des opérations de la commande à l'encaissement (*order-to-cash*), s'intégrera au système ERP existant pour les écritures comptables et fournira une vue en temps réel de chaque commande, du passage à l'encaissement. Votre livrable n'est pas du code : il s'agit d'une **spécification multi-diagrammes complète et validée** que l'équipe de développement pourra implémenter sans ambiguïté.

---

> **ℹ️ Comment Utiliser cette Étude de Cas**
>
> Ce document est la **référence unique** pour l'ensemble des exercices de la formation. Chaque diagramme que vous tracez doit être cohérent avec les acteurs, les entités, les règles métier et les récits de cas d'utilisation décrits ici. Le même vocabulaire — les mêmes noms de classes, les mêmes noms d'opérations, les mêmes libellés d'état — doit apparaître dans chaque type de diagramme. L'incohérence entre diagrammes est le symptôme principal d'une analyse défaillante ; cette étude de cas est conçue pour vous aider à la détecter et à la corriger.
>
> Réalisez les exercices dans l'ordre indiqué dans la section 6 (Feuille de Route). Chaque exercice s'appuie sur le précédent.

> **🛂 Convention — Couloirs Acteurs / Système dans les diagrammes d'Activité**
>
> Tous les diagrammes d'Activité (*Activity Diagrams*) produits dans cette étude de cas utilisent une convention de couloirs (*swimlanes*) stricte : **un couloir par acteur effectivement impliqué** dans le flux (Client, Représentant commercial, Opérateur d'entrepôt, Contrôleur financier… selon le UC) et **un (ou plusieurs) couloir(s) Système** pour l'OMS et ses dépendances. Les acteurs du diagramme d'Activité doivent être **strictement les mêmes** que ceux identifiés dans le Use Case correspondant. **Ne jamais regrouper plusieurs acteurs distincts dans un couloir générique « Utilisateur »** — chaque rôle humain a sa propre voie, ce qui est essentiel pour identifier les écrans propres à chacun. À l'inverse, on ne trace pas le couloir d'un acteur qui n'apparaît pas dans le flux particulier modélisé.
>
> Cette convention matérialise la **règle de dérivation Activité → Classes** qui est l'objectif d'apprentissage central de la formation :
>
> | Élément du diagramme d'Activité | Correspondance dans le système |
> |---|---|
> | Action dans un **couloir d'Acteur** (Client, Opérateur d'entrepôt, Contrôleur financier…) | Un **écran** (*screen*) de l'application — point d'interaction de cet acteur avec le système |
> | Action dans un **couloir Système** | Une **méthode** (*method*) du diagramme de Classes |
> | Objet manipulé entre actions | Un **attribut** (*attribute*) du diagramme de Classes |
>
> C'est cette correspondance qui permet, en exercice 06, de construire le diagramme de classes en lisant les couloirs Système des activités produites aux exercices 03 à 05. Avant chaque exercice d'Activité, vérifier (a) que les couloirs des acteurs correspondent exactement à ceux du Use Case du flux modélisé, (b) qu'aucun acteur n'a été agrégé dans un couloir générique, et (c) que chaque action est attribuée au bon couloir.

> **🛂 Convention — Diagrammes spécifiques (Séquence et État)**
>
> Le diagramme de Séquence (*Sequence Diagram*) et le diagramme d'États (*State Diagram*) sont, dans la pédagogie de cette formation, des **diagrammes spécifiques** : on ne les produit pas systématiquement, mais uniquement lorsque leur usage est justifié.
>
> - **Diagramme de Séquence — justifié dès qu'on documente une interaction avec un système externe.** Dans cette étude de cas, chaque UC implique au moins un acteur secondaire de type système (Système ERP, Service de notification, Passerelle de paiement) ou un acteur externe (Transporteur). Les trois UC produisent donc un diagramme de Séquence — la justification précise est rappelée dans la rubrique « Diagrammes à produire » de chaque UC.
> - **Diagramme d'États — justifié pour modéliser le cycle de vie d'une entité statefull.** Dans cette étude de cas, les entités `Order`, `Shipment`, `Invoice` et `Payment` ont chacune un cycle de vie complexe (états multiples, transitions conditionnelles, branches d'exception) qui justifie un diagramme d'États dédié. À l'inverse, `Customer`, `Product`, `OrderLine` ou `StockReservation` ne reçoivent pas de diagramme d'États dans cette étude de cas : leur cycle de vie est trivial, ou il est porté par une autre entité.
>
> Hors de ces conditions de justification, ne pas produire ces diagrammes : ils sont coûteux à maintenir et n'apportent rien si l'entité ou l'interaction ne le justifie pas. Cette posture — *« quel diagramme apporte vraiment de l'information ici ? »* — est elle-même un objectif d'apprentissage de la formation.

---

## 1. Acteurs et Rôles

Les acteurs suivants interagissent avec l'OMS de NovaTrade. Chaque acteur (*Actor*) dans un diagramme de cas d'utilisation (*Use Case Diagram*), chaque piscine (*Pool*) ou couloir (*Lane*) dans un diagramme BPMN, et chaque ligne de vie (*lifeline*) dans un diagramme de séquence (*Sequence Diagram*) doit provenir de cette liste. Les couloirs des diagrammes d'Activité (*Activity Diagram*) suivent une chaîne dérivée : ils correspondent aux acteurs du Use Case modélisé (eux-mêmes issus de cette liste) plus un couloir Système — cf. la convention « Couloirs Acteurs / Système » en début de document.

| Acteur | Type | Description |
|---|---|---|
| **Client** (*Customer*) | Primaire (humain) | Un client professionnel enregistré. Passe des commandes, reçoit les expéditions, paie les factures. Peut être Standard ou Premium. |
| **Représentant commercial** (*Sales Representative*) | Primaire (humain) | Employé NovaTrade. Assiste les clients lors du passage de commande, résout les litiges et gère les comptes clients. |
| **Opérateur d'entrepôt** (*Warehouse Operator*) | Primaire (humain) | Employé NovaTrade. Prépare les expéditions, effectue les prélèvements en stock, confirme l'envoi. |
| **Contrôleur financier** (*Finance Controller*) | Primaire (humain) | Employé NovaTrade. Examine et approuve les conditions de paiement, gère les factures en retard, déclenche les relances (*dunning*). |
| **Système ERP** (*ERP System*) | Secondaire (système) | Le système de planification des ressources (*Enterprise Resource Planning*) existant de NovaTrade. Gère les enregistrements d'inventaire, les écritures comptables et le rapprochement des paiements. |
| **Passerelle de paiement** (*Payment Gateway*) | Secondaire (système) | Processeur de paiement externe. Autorise et capture les paiements par carte ou virement bancaire. |
| **Service de notification** (*Notification Service*) | Secondaire (système) | Service interne qui envoie les confirmations par courriel et SMS aux clients et aux équipes internes. |
| **Transporteur** (*Carrier*) | Secondaire (externe) | Prestataire logistique tiers. Récupère les expéditions, fournit les numéros de suivi, confirme la livraison. |

> **⚠️ Acteur vs. Rôle**
>
> Un acteur représente un **rôle**, pas une personne. Le même individu (par exemple un Représentant commercial) peut jouer le rôle de Client dans un scénario de test interne. Ce qui compte, c'est le rôle joué *dans le processus modélisé*, et non son intitulé de poste.

> **⚠️ L'OMS n'est pas un acteur**
>
> L'OMS est le **système qu'on conçoit**, pas un acteur. Dans le diagramme de cas d'utilisation (*Use Case Diagram*), il apparaît comme la **frontière du système** (*system boundary*) qui contient les cas d'utilisation, jamais comme une silhouette d'acteur à l'extérieur. En BPMN (exercice 01), il peut en revanche être représenté comme **piscine** (*Pool*) ou support transversal — c'est cohérent : BPMN modélise le processus métier dans lequel l'OMS est un participant, tandis que le Use Case Diagram modélise les fonctionnalités du système avec ses utilisateurs externes. Tous les acteurs du tableau ci-dessus sont des entités **externes** à l'OMS.

---

## 2. Vocabulaire du Domaine

Les entités suivantes constituent le **vocabulaire partagé** de l'OMS NovaTrade. Chaque attribut de diagramme de classes (*Class Diagram*), chaque libellé d'état dans un diagramme d'états (*State Diagram*), chaque valeur de retour dans un diagramme de séquence, et chaque objet de données (*Data Object*) BPMN doit utiliser les noms exacts définis ici. Ce tableau est la référence faisant autorité.

> **🛂 Convention de nommage — entités, attributs et états**
>
> Les **noms d'entités** (`Customer`, `Order`, `Shipment`…) et leur traduction française (Client, Commande, Expédition…) sont **bilingues** comme le reste du contenu pédagogique : on parle de « Commande » dans la prose et on écrit `Order` dans les diagrammes. En revanche, les **noms d'attributs** (`customerId`, `companyName`, `isPremium`, `stockQuantity`) et les **libellés d'états** (`Submitted`, `Confirmed`, `OnHold`, `Dispatched`) sont conservés en **anglais** et en *camelCase* / *PascalCase*. Ce sont des **identifiants techniques** destinés à être directement transposables dans le code (Java, C#, Python…) — ils ne suivent donc pas la règle bilingue qui s'applique aux concepts BPMN et UML eux-mêmes.
>
> Cette convention vous donne deux référentiels distincts : le **vocabulaire métier** (français pour la communication avec les parties prenantes) et le **vocabulaire technique** (anglais pour la communication avec les développeurs).

| Entité | Description | Attributs Clés | États Possibles |
|---|---|---|---|
| **Client** (*Customer*) | Une entité commerciale enregistrée qui passe des commandes. | `customerId`, `companyName`, `email`, `isPremium`, `creditLimit` | `Active`, `Suspended`, `Blocked` |
| **Commande** (*Order*) | Une demande d'achat d'un ou plusieurs produits. | `orderId`, `orderDate`, `status`, `totalAmount`, `deliveryAddress` | `Draft`, `Submitted`, `Confirmed`, `OnHold`, `InFulfilment`, `Shipped`, `Delivered`, `Closed`, `Cancelled` |
| **Ligne de commande** (*OrderLine*) | Une ligne produit unique au sein d'une Commande. | `quantity`, `unitPrice`, `lineTotal`, `productId` | *(cycle de vie lié à la Commande)* |
| **Produit** (*Product*) | Un article négociable du catalogue NovaTrade. | `productId`, `name`, `unitPrice`, `stockQuantity`, `reorderLevel` | `Available`, `LowStock`, `OutOfStock`, `Discontinued` |
| **Réservation de stock** (*StockReservation*) | Une mise en attente temporaire de stock pour une Commande spécifique. | `reservationId`, `quantity`, `expiresAt`, `orderId`, `productId` | `Active`, `Released`, `Expired` |
| **Expédition** (*Shipment*) | L'envoi physique des marchandises pour une Commande confirmée. | `shipmentId`, `carrier`, `trackingNumber`, `shippedDate`, `estimatedDelivery` | `Preparing`, `Ready`, `Dispatched`, `Delivered`, `Exception` |
| **Facture** (*Invoice*) | Une demande de paiement émise au Client après expédition. | `invoiceId`, `issueDate`, `dueDate`, `amount`, `status` | `Draft`, `Sent`, `PartiallyPaid`, `Paid`, `Overdue`, `Escalated`, `Cancelled` |
| **Paiement** (*Payment*) | Un enregistrement abstrait d'une transaction de paiement. | `paymentId`, `amount`, `status`, `processedAt` | `Pending`, `Authorised`, `Captured`, `Failed` |
| **Paiement Stripe** (*StripePayment*) | Un paiement concret par carte via la passerelle Stripe. Hérite de Paiement. | `stripeToken`, `cardLast4` | *(hérite des états de Paiement)* |
| **Virement bancaire** (*BankTransfer*) | Un paiement concret par virement bancaire. Hérite de Paiement. | `iban`, `bankReference` | *(hérite des états de Paiement)* |

> **📘 Définition — Commande à l'Encaissement (*Order-to-Cash*)**
>
> **Standard :** processus métier de référence, commun à l'ensemble des systèmes ERP/OMS.
>
> Le processus **Commande à l'Encaissement (*Order-to-Cash*, O2C)** est le processus métier de bout en bout qui débute lorsqu'un client passe une commande et se termine lorsque le paiement est reçu et rapproché dans le système comptable. Il couvre trois domaines fonctionnels : la **Gestion des Commandes** (saisie et confirmation), l'**Exécution** (prélèvement, emballage et expédition), et la **Facturation et Recouvrement** (facturation, paiement et rapprochement). L'OMS NovaTrade couvre l'intégralité du cycle O2C.

---

## 3. Règles Métier

Les règles suivantes régissent les opérations de NovaTrade. Chaque condition de passerelle (*gateway*) dans un diagramme BPMN ou d'activité, chaque condition de garde (*guard condition*) dans un diagramme d'états, et chaque fragment `alt` ou `opt` dans un diagramme de séquence doit s'appuyer sur l'une de ces règles. Référencez-les par leur identifiant (par exemple, *selon BR-04*) dans vos annotations.

### Règles relatives aux Commandes

| ID | Règle |
|---|---|
| **BR-01** | Un Client doit être `Active` pour passer une Commande. Les clients `Suspended` ou `Blocked` ne peuvent pas passer de nouvelles commandes. |
| **BR-02** | Une Commande doit contenir au moins une Ligne de commande pour être soumise. |
| **BR-03** | Le stock de chaque Ligne de commande doit être réservé au moment de la confirmation de la Commande. Si le stock est insuffisant pour une seule ligne, la Commande entière est mise en attente jusqu'à une décision de réapprovisionnement. |
| **BR-04** | Les Clients Premium (`isPremium = true`) bénéficient d'un traitement prioritaire : leurs Commandes contournent la file d'attente standard et sont traitées le jour même. |
| **BR-05** | Une Commande peut être annulée par le Client à tout moment avant son passage à l'état `InFulfilment`. Au-delà, seul un processus de Retour (hors périmètre de cette étude de cas) peut inverser la transaction. |
| **BR-06** | Un Représentant commercial peut outrepasser une mise en attente pour stock insuffisant pour un Client Premium en autorisant manuellement une expédition partielle. |

### Règles d'Exécution

| ID | Règle |
|---|---|
| **BR-07** | Une Expédition ne peut être créée que pour une Commande en statut `Confirmed`. |
| **BR-08** | L'Opérateur d'entrepôt doit confirmer le prélèvement et l'emballage avant qu'une Expédition ne passe à l'état `Ready`. |
| **BR-09** | Dès qu'un Transporteur récupère une Expédition (`Dispatched`), la Commande passe à l'état `Shipped` et le Client reçoit une notification de suivi automatisée. |
| **BR-10** | La livraison est confirmée soit par le scan du Transporteur, soit par l'accusé de réception du Client. La Commande passe à `Delivered` lors de la confirmation, puis automatiquement à `Closed` après 7 jours sans litige. |

### Règles de Facturation et de Paiement

| ID | Règle |
|---|---|
| **BR-11** | Une Facture est générée automatiquement par l'OMS lorsqu'une Expédition passe à l'état `Dispatched`. Sa date d'échéance est de 30 jours après la date d'émission pour les clients Standard ; 45 jours pour les clients Premium. |
| **BR-12** | Si une Facture n'est pas payée à sa date d'échéance, elle passe à `Overdue` et le Contrôleur financier reçoit une alerte. |
| **BR-13** | Le Contrôleur financier peut émettre une relance (*dunning reminder*) (jusqu'à 3 fois, espacées de 7 jours) avant escalade vers une procédure de recouvrement formelle. |
| **BR-14** | Un Paiement n'est considéré comme complet que lorsque la Passerelle de paiement confirme le statut `Captured`. Un statut `Authorised` seul n'est pas suffisant. |
| **BR-15** | Lors de la capture du Paiement, l'OMS envoie une écriture de rapprochement au Système ERP et marque la Facture comme `Paid`. |

---

## 4. Cas d'Utilisation Clés

Cette étude de cas s'articule autour de **trois cas d'utilisation (*use cases*) clés** qui couvrent ensemble l'intégralité du cycle commande-à-encaissement. Chaque cas d'utilisation sera modélisé avec un ensemble ciblé de diagrammes UML — Activité (essentiel), État et Séquence (justifiés par le contexte de chaque UC, cf. la convention « Diagrammes spécifiques » en début de document) — puis vérifié pour garantir la cohérence inter-diagrammes.

---

### UC-01 — Passer une Commande (*Place Order*)

**Déclencheur :** Un Client enregistré (ou un Représentant commercial agissant en son nom) initie une nouvelle commande dans l'OMS.

**Acteur Principal :** Client / Représentant commercial
**Acteurs Secondaires :** Système ERP (vérification du stock), Service de notification

**Préconditions :**
- Le compte Client existe et est `Active` (BR-01).
- Au moins un Produit est présent dans le catalogue de l'OMS.

---

**Chemin nominal (*happy path*) — narration**

Le Client se connecte au portail OMS et sélectionne un ou plusieurs Produits dans le catalogue, en spécifiant une quantité pour chacun. Pour chaque Produit sélectionné, l'OMS vérifie le `stockQuantity` actuel par rapport à la quantité demandée (BR-03). Le Client fournit une adresse de livraison et examine le récapitulatif de la Commande, incluant le total calculé. Le Client soumet la Commande. L'OMS crée la Commande en statut `Draft`, puis la fait passer à `Submitted` lors de la soumission. L'OMS valide la Commande (au moins une ligne, BR-02 ; statut Client `Active`, BR-01) et réserve le stock pour chaque Ligne de commande. La Commande passe à `Confirmed`. L'OMS envoie une notification de confirmation au Client (numéro de commande, estimation de livraison) via le Service de notification et crée un enregistrement de réservation de stock pour chaque ligne dans le Système ERP. Si le Client est Premium (BR-04), la Commande est marquée pour traitement prioritaire et placée en tête de la file d'exécution.

---

**Chemins d'Exception**

| Exception | Déclencheur | Résolution |
|---|---|---|
| **Client suspendu** | Le compte Client est `Suspended` ou `Blocked` au moment de la soumission (BR-01) | L'OMS rejette la Commande ; le Représentant commercial reçoit une alerte pour contacter le client. |
| **Stock insuffisant** | Une ou plusieurs Lignes de commande ne peuvent pas être entièrement réservées (BR-03) | La Commande est placée en statut `OnHold`. L'OMS notifie le Représentant commercial. Pour les Clients Premium, le Représentant commercial peut autoriser une expédition partielle (BR-06) ; sinon, la Commande attend le réapprovisionnement. |
| **Le Client annule avant confirmation** | Le Client annule la Commande en `Draft` avant de la soumettre | La Commande passe à `Cancelled` ; aucune réservation de stock n'est effectuée. |

---

**Transitions d'États Concernées**

```
Order: Draft → Submitted → Confirmed
                          → OnHold (stock insuffisant)
       Draft → Cancelled (annulation client)
```

**Diagrammes à produire pour UC-01 :**
- Diagramme d'activité : logique de validation de Commande et de réservation de stock, avec un couloir par acteur effectivement impliqué dans ce flux (Client et/ou Représentant commercial, selon le scénario) plus un couloir **Système** (les actions du couloir Système deviendront les méthodes du Class Diagram en exercice 06)
- Diagramme d'états : cycle de vie de la `Order` (limité aux états atteignables dans UC-01)
- Diagramme de séquence : interactions entre Client, OMS, Système ERP et Service de notification pour le chemin nominal + exception de stock — justifié par la présence de deux acteurs secondaires de type système

---

### UC-02 — Exécuter la Commande (*Fulfil Order*)

**Déclencheur :** Une Commande en statut `Confirmed` entre dans la file d'exécution. Cela se produit automatiquement après l'aboutissement de UC-01, ou après qu'un Représentant commercial ait résolu une mise en attente pour stock.

**Acteur Principal :** Opérateur d'entrepôt
**Acteurs Secondaires :** Système ERP, Transporteur, Service de notification

**Préconditions :**
- La Commande est en statut `Confirmed` (BR-07).
- Toutes les Réservations de stock pour la Commande sont `Active`.

---

**Chemin nominal — narration**

L'OMS place la Commande confirmée dans la file d'exécution, crée un enregistrement d'Expédition en statut `Preparing`, et fait passer la Commande à l'état `InFulfilment`. L'Opérateur d'entrepôt récupère la liste de prélèvement (*pick list*) depuis l'OMS (générée à partir des Lignes de commande). L'Opérateur d'entrepôt prélève et emballe physiquement les articles. Une fois l'emballage terminé, l'Opérateur marque l'Expédition comme `Ready` dans l'OMS (BR-08). L'OMS notifie le Transporteur affecté qu'une expédition est prête à être collectée. Le Transporteur arrive et récupère le colis. L'OMS enregistre le numéro de suivi du Transporteur et fait passer l'Expédition à `Dispatched`. La Commande passe à `Shipped` (BR-09). L'OMS envoie une notification de suivi automatisée au Client via le Service de notification. Le Transporteur met à jour le statut de l'expédition en temps réel. Lorsque le Transporteur confirme la livraison (ou que le Client accuse réception), l'OMS fait passer l'Expédition à `Delivered` et la Commande à `Delivered` (BR-10). Après 7 jours sans litige, la Commande passe automatiquement à `Closed`.

---

**Chemins d'Exception**

| Exception | Déclencheur | Résolution |
|---|---|---|
| **Article manquant lors du prélèvement** | L'Opérateur d'entrepôt ne peut pas localiser un article réservé | L'Opérateur signale un écart dans l'OMS. Le Représentant commercial est notifié. L'OMS peut autoriser une expédition partielle ou annuler la ligne concernée. |
| **Le Transporteur ne collecte pas** | Aucune collecte par le transporteur dans la fenêtre attendue | L'Expédition reste en `Ready`. L'OMS envoie une alerte après 24h. Une nouvelle réservation auprès d'un transporteur est effectuée. |
| **Exception de livraison** | Le Transporteur signale une livraison échouée (mauvaise adresse, client absent) | L'Expédition passe à `Exception`. Le Client est notifié. L'Opérateur d'entrepôt planifie une nouvelle livraison ou un retour à l'entrepôt. |
| **Le Client conteste la livraison** | Le Client déclare ne pas avoir reçu après que le Transporteur ait confirmé la livraison | La Commande reste en `Delivered` au lieu de se clôturer automatiquement. Le Contrôleur financier est alerté. Le cas est escaladé en dehors du périmètre de l'OMS. |

---

**Transitions d'États Concernées**

```
Order: Confirmed → InFulfilment → Shipped → Delivered → Closed
Shipment: Preparing → Ready → Dispatched → Delivered
                                          → Exception
```

**Diagrammes à produire pour UC-02 :**
- Diagramme d'activité : flux de prélèvement, d'emballage et d'expédition, avec un couloir par acteur impliqué (Opérateur d'entrepôt, Transporteur — qui agissent à des moments distincts) plus un couloir **Système** ; introduit Fork/Join (*Fork/Join*) pour les activités parallèles (prélèvement + génération d'étiquettes)
- Diagramme d'états : cycle de vie de la `Shipment` (complet, depuis `Preparing` jusqu'à `Delivered` ou `Exception`)
- Diagramme de séquence : interactions entre OMS, Transporteur, Système ERP et Service de notification — justifié par la présence du Transporteur (acteur externe) et de deux systèmes secondaires

---

### UC-03 — Traiter la Facture et le Paiement (*Process Invoice and Payment*)

**Déclencheur :** Une Expédition passe à l'état `Dispatched`. L'OMS crée automatiquement une Facture pour la Commande associée.

**Acteur Principal :** Contrôleur financier (pour le chemin de retard) ; Client (pour le paiement)
**Acteurs Secondaires :** Système ERP, Passerelle de paiement, Service de notification

**Préconditions :**
- La Commande est en statut `Shipped`.
- Aucune Facture n'existe déjà pour cette Commande.

---

**Chemin nominal — narration**

Lorsque l'Expédition est expédiée (UC-02), l'OMS génère automatiquement une Facture en statut `Draft` pour la Commande associée. Le montant de la Facture est égal au `totalAmount` de la Commande. La date d'échéance est définie selon le niveau du Client : 30 jours pour Standard, 45 jours pour Premium (BR-11). L'OMS fait passer la Facture à `Sent` et l'envoie au courriel du Client via le Service de notification. L'OMS publie également la créance en attente dans le Système ERP en tant qu'écriture comptable. Le Client examine la Facture dans le portail OMS et initie le paiement en sélectionnant un mode de paiement (carte via Stripe ou virement bancaire). L'OMS appelle la Passerelle de paiement pour autoriser le paiement (BR-14). En cas d'autorisation réussie, la Passerelle de paiement capture le paiement. L'OMS confirme la capture (BR-14), fait passer le Paiement à `Captured` et la Facture à `Paid`. L'OMS envoie un événement de rapprochement au Système ERP (BR-15), qui impute le paiement reçu sur la créance ouverte. L'OMS envoie une confirmation de paiement au Client.

---

**Chemins d'Exception**

| Exception | Déclencheur | Résolution |
|---|---|---|
| **Échec d'autorisation du paiement** | La Passerelle de paiement renvoie un statut refusé | L'OMS journalise l'échec et notifie le Client de réessayer avec un autre moyen. La Facture reste `Sent`. |
| **Facture en retard** | La date d'échéance est atteinte avec une Facture toujours en `Sent` (BR-12) | L'OMS fait passer la Facture à `Overdue`. Le Contrôleur financier est alerté. Le processus de relance débute (BR-13). |
| **Relance — rappel 1** | 7 jours après l'échéance, Facture toujours `Overdue` | Le Contrôleur financier envoie une première relance via l'OMS. Compteur incrémenté. |
| **Relance — rappel 2** | 14 jours après l'échéance | Le Contrôleur financier envoie une deuxième relance. Compteur = 2. |
| **Relance — rappel 3** | 21 jours après l'échéance | Le Contrôleur financier envoie une troisième et dernière relance. Compteur = 3. |
| **Escalade** | 3 relances envoyées, aucun paiement reçu | Le Contrôleur financier escalade vers un recouvrement formel (hors périmètre OMS). Le compte Client peut être `Suspended` (BR-01). |
| **Paiement partiel** | Le Client paie un montant inférieur au total de la Facture | La Facture passe à `PartiallyPaid`. Le solde restant est suivi. Le Contrôleur financier est notifié. |

---

**Transitions d'États Concernées**

```
Invoice: Draft → Sent → Paid
                      → Overdue → (relance x3) → Escalated
              → PartiallyPaid → Paid
Payment: Pending → Authorised → Captured
                             → Failed
```

**Diagrammes à produire pour UC-03 :**
- Diagramme d'activité : logique de traitement du paiement, avec un couloir pour le **Client** (initiation du paiement), un couloir pour le **Contrôleur financier** (chemin de retard et relances) et un couloir **Système** ; introduit la décision sur le mode de paiement et la boucle de relance
- Diagramme d'états : cycle de vie de la `Invoice` (complet, incluant le chemin de relance et le paiement partiel) ; un diagramme d'états séparé pour `Payment` est également produit
- Diagramme de séquence : interactions entre OMS, Client, Passerelle de paiement, Système ERP et Service de notification pour le chemin nominal + chemin de retard — justifié par la présence de la Passerelle de paiement (système externe critique)

---

## 5. Référence de Cohérence Inter-Diagrammes

La cohérence inter-diagrammes de cette étude de cas repose sur **deux référentiels complémentaires**. La **table de transposition BPMN ↔ UML** ci-dessous indique comment chaque élément BPMN se traduit en UML — c'est la clef du passage du métier (exercice 01) vers le système (exercices 02 à 06). La **table de correspondance par concept** qui suit ensuite trace chaque entité ou opération nommée à travers les différents diagrammes — c'est l'outil d'audit ligne par ligne pour l'exercice d'Intégration Complète (exercice 07).

### 5.1 Frontière BPMN ↔ UML — table de transposition

Lorsque vous passez du diagramme BPMN (exercice 01) au diagramme de cas d'utilisation (exercice 02), puis aux diagrammes UML détaillés (exercices 03 à 06), les éléments BPMN se transposent en éléments UML selon les règles ci-dessous.

| Élément BPMN (métier) | Élément UML correspondant | Remarque |
|---|---|---|
| Piscine (*Pool*) représentant l'OMS | Frontière du système (*system boundary*) du diagramme de cas d'utilisation | L'OMS est *à l'intérieur* de la frontière ; il n'apparaît jamais comme acteur. |
| Piscine (*Pool*) externe **fermée** (*black-box pool*) — Client, Transporteur… | Acteur (*Actor*) du diagramme de cas d'utilisation | Une piscine fermée représente un participant externe dont on ne modélise pas le détail interne ; chacune devient un acteur dans le diagramme de cas d'utilisation. |
| Couloir (*Lane*) d'une piscine NovaTrade | Acteur (*Actor*) du diagramme de cas d'utilisation | Chaque couloir correspond à un rôle humain (Représentant commercial, Opérateur d'entrepôt, Contrôleur financier…). |
| Tâche métier (*Activity / Task*) supportée par le système | Cas d'utilisation (*Use Case*) | C'est par cette transposition qu'on extrait les UC du BPMN à l'exercice 02. |
| Tâche métier non supportée par le système | Pas de transposition UML | Reste dans le BPMN seul ; signale les zones encore manuelles. |
| Objet de données (*Data Object*) | Attribut d'une classe (ou classe entière) du diagramme de classes | Le nom de l'objet de données doit correspondre au vocabulaire de la Section 2. |
| Flux de message (*Message Flow*) vers un système externe | Interaction modélisée par un diagramme de Séquence | Justifie la production d'un Sequence Diagram (cf. convention en début de document). |
| Événement (*Event*) déclenchant une tâche métier | Déclencheur (*trigger*) du cas d'utilisation correspondant | |
| Événement (*Event*) marquant un changement d'état d'une entité statefull | Transition d'un diagramme d'États | Pour `Order`, `Shipment`, `Invoice`, `Payment`. |

> **⚠️ Ne pas confondre la frontière BPMN ↔ UML et la dérivation interne à UML**
>
> La table ci-dessus traduit la **frontière BPMN → UML** (passage du métier au système). À l'intérieur d'UML, une **seconde dérivation** s'applique : la **chaîne Cas d'Utilisation → Activité → Classes** (cf. la convention « Couloirs Acteurs / Système » en début de document). Ce sont deux étapes distinctes : la première est la traversée de la frontière métier ↔ système ; la seconde structure l'analyse fonctionnelle interne au système.

### 5.2 Vocabulaire transversal — table de correspondance par concept

Le tableau suivant trace chaque élément nommé qui apparaît dans plus d'un type de diagramme. Utilisez-le pendant l'exercice d'Intégration Complète pour vérifier qu'aucun nom, état ou opération n'a dérivé entre diagrammes.

> **🛂 La cohérence Activité ↔ Classes — fil conducteur de l'audit**
>
> Les colonnes **Action d'Activité (couloir)** et **Classe / Attribut** sont liées par la **règle de dérivation** énoncée en début de document : chaque action d'un couloir **Système** doit correspondre à une **méthode** (ou à un effet sur un attribut) d'une classe identifiée, et chaque action d'un couloir d'**Acteur** correspond à un **écran** de l'application (point d'interaction de cet acteur avec le système ; non listé dans ce tableau). L'audit de cohérence consiste donc, en grande partie, à parcourir ce tableau ligne par ligne pour vérifier que :
>
> - chaque action Système possède bien une **méthode ou un attribut correspondant** dans la classe pertinente ;
> - les **noms utilisés** sont identiques dans les deux colonnes (et conformes au vocabulaire de la Section 2) ;
> - aucune méthode du diagramme de classes ne reste **orpheline** (c'est-à-dire sans action d'activité qui la déclenche).
>
> Une action Système sans méthode correspondante, ou une méthode sans action d'activité qui la justifie, signale toujours une incohérence à corriger.

| Concept | Élément BPMN | Cas d'Utilisation UML | Classe / Attribut | Libellé d'État | Message de Séquence | Action d'Activité (couloir) |
|---|---|---|---|---|---|---|
| Le Client soumet une commande | Événement de début message (*Start Message Event*) | `Place Order` | `Customer.placeOrder()` | `Order: Draft→Submitted` | `submitOrder()` | Action *Submit Order* (couloir Client) |
| L'OMS valide la commande | Tâche de service (*Service Task*) | — | Logique de validation `Order` | `Order: Submitted→Confirmed` | `validateOrder()` | Action *Validate Order* (couloir Système) |
| Réservation de stock | Tâche de service | — | `StockReservation` | `StockReservation: Active` | `reserveStock()` | Action *Reserve Stock* (couloir Système) |
| Commande mise en attente | Événement intermédiaire (*Intermediate Event*) | — | `Order.status = OnHold` | `Order: OnHold` | `notifyHold()` | Action *Notify Sales Rep* (couloir Système) |
| Expédition créée | Tâche de service | — | `Shipment` | `Shipment: Preparing` | `createShipment()` | Action *Create Shipment* (couloir Système) |
| Collecte par le transporteur | Événement intermédiaire de réception message (*Message Catch Event*) | — | `Shipment.trackingNumber` | `Shipment: Dispatched` | `confirmPickup()` | Action *Record Tracking* (couloir Système) |
| Facture générée | Tâche de service | `Process Invoice` | `Invoice` | `Invoice: Draft→Sent` | `generateInvoice()` | Action *Generate Invoice* (couloir Système) |
| Autorisation de paiement | Tâche de service | `Process Payment` | `Payment.process()` | `Payment: Pending→Authorised` | `authorisePayment()` | Action *Authorise Payment* (couloir Système) |
| Capture de paiement | Tâche de service | — | `Payment.capture()` | `Payment: Authorised→Captured` | `capturePayment()` | Action *Capture Payment* (couloir Système) |
| Facture en retard | Événement de bordure temporel (*Timer Boundary Event*) | — | `Invoice.status = Overdue` | `Invoice: Overdue` | `markOverdue()` | Action *Transition to Overdue* (couloir Système) |
| Rapprochement ERP | Tâche de service | — | `ERP.postEntry()` | — | `postReconciliation()` | Action *Post to ERP* (couloir Système) |

---

## 6. Feuille de Route des Exercices de Formation

Les exercices ci-dessous doivent être réalisés dans l'ordre. Cet ordre suit la **chaîne d'analyse** imposée par la formation : on modélise d'abord le métier (BPMN), puis on en extrait les fonctionnalités du système (Cas d'Utilisation), puis on détaille le comportement de chaque cas d'utilisation (Activité avec un couloir par acteur du Use Case et un couloir Système), et c'est seulement à partir de ces activités que se construit le modèle de classes (méthodes ← actions Système ; attributs ← objets manipulés). Chaque exercice est un fichier autonome qui s'appuie sur le présent document pour le contexte métier.

| # | Fichier d'Exercice | Pilier | Diagrammes | Durée Estimée |
|---|---|---|---|---|
| 01 | `Study Case - BPMN.md` | BPMN | BPMN de bout en bout du processus Commande à l'Encaissement (NovaTrade en piscine unique avec couloirs Vente, Exécution, Finance ; piscines externes pour Client et Transporteur) | 2h |
| 02 | `Study Case - Use Case Diagram.md` | UML | Diagramme de cas d'utilisation extrait du BPMN ; périmètre OMS ; acteurs primaires & secondaires | 1h30 |
| 03 | `Study Case - UC01 Place Order.md` | UML | Activité (couloirs par acteur du UC + Système) · État (Order) · Séquence (ERP, Notification) | 3h |
| 04 | `Study Case - UC02 Fulfil Order.md` | UML | Activité (couloirs par acteur du UC + Système) · État (Shipment) · Séquence (Transporteur, ERP, Notification) | 3h |
| 05 | `Study Case - UC03 Invoice Payment.md` | UML | Activité (couloirs par acteur du UC + Système) · État (Invoice, Payment) · Séquence (Passerelle de paiement, ERP, Notification) | 3h |
| 06 | `Study Case - Class Diagram.md` | UML | Diagramme de classes émergeant des activités (méthodes ← actions Système ; attributs ← objets manipulés) ; héritage Payment / StripePayment / BankTransfer | 2h |
| 07 | `Study Case - Full Integration.md` | Intégration | Audit de cohérence inter-diagrammes (vocabulaire, états, opérations, frontière BPMN ↔ UML) | 1h30 |

**Durée totale estimée : ~16 h, soit 3 à 4 jours**

---

## 7. Limite de Périmètre

Les sujets suivants sont explicitement **hors périmètre** pour cette étude de cas — ils ne doivent apparaître dans aucun diagramme produit :

- La gestion des fournisseurs et des achats
- La gestion du catalogue produits (création, mise à jour, retrait d'articles)
- L'enregistrement client et la conformité KYC (*Know Your Customer*)
- Les retours et remboursements
- Le calcul de la taxe et de la TVA

Définir le périmètre est l'**Étape 1 de toute méthodologie de modélisation** : connaître ce qu'on ne modélise *pas* est aussi important que connaître ce qu'on modélise.

---

*Premier livrable : `Study Case - BPMN.md`*
