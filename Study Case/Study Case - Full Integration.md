# Solution — Audit d'Intégration Inter-Diagrammes

**Livrable du Study Case :** 07 (septième et dernier livrable de la feuille de route)
**Énoncé :** voir le document maître `Study Case.md`, Section 5 (Référence de Cohérence Inter-Diagrammes)
**Périmètre couvert :** audit systématique de cohérence entre les six livrables précédents — diagramme BPMN (livrable 01), diagramme de Cas d'Utilisation (livrable 02), trois ensembles de diagrammes UC (livrables 03 à 05), diagramme de Classes (livrable 06)

Ce document est la solution attendue pour le livrable 07 du Study Case NovaTrade. Il consolide en une seule grille de vérification l'ensemble des contrôles de cohérence prescrits par la Section 5 du `Study Case.md`. C'est le **dernier filet** avant que la spécification ne parte chez l'équipe de développement : toute incohérence non détectée ici se paiera dans le code.

---

## 1. Cadrage de l'Audit

### Objectif

L'audit vérifie qu'aucune incohérence — vocabulaire divergent, état orphelin, méthode non déclenchée, règle métier non implémentée, relation tracée différemment d'un diagramme à l'autre — ne subsiste entre les six livrables précédents. Le critère de réussite est binaire : **chaque concept du Study Case doit être tracé, sans dérive, dans tous les diagrammes où il intervient**.

### Posture de l'auditeur

L'auditeur ne juge pas la qualité d'un diagramme isolé (cela a été fait dans chaque livrable individuellement). Il se concentre **strictement sur les frontières inter-diagrammes** : un nom utilisé dans le BPMN se retrouve-t-il à l'identique dans le Class Diagram ? Un état listé dans la Section 2 du `Study Case.md` est-il atteignable dans au moins un diagramme d'États ? Une méthode du Class Diagram a-t-elle au moins une action d'Activité qui la déclenche, et inversement ?

### Référentiels de l'audit

| Référentiel | Source |
|---|---|
| **Liste des acteurs** | `Study Case.md` — Section 1 |
| **Vocabulaire du domaine** (entités, attributs, états) | `Study Case.md` — Section 2 |
| **Règles métier** (BR-01 à BR-15) | `Study Case.md` — Section 3 |
| **Frontière BPMN ↔ UML** | `Study Case.md` — Section 5.1 |
| **Table de correspondance transversale** | `Study Case.md` — Section 5.2 |
| **Limite de périmètre** | `Study Case.md` — Section 7 |

---

## 2. Méthodologie

L'audit se déroule en **sept passes**, chacune ciblant une dimension de cohérence. Chaque passe produit un tableau de résultats avec un statut explicite :

| Symbole | Signification |
|---|---|
| ✓ | Conformité totale — l'élément est tracé de façon cohérente sur tous les diagrammes pertinents |
| ⚠ | Anomalie mineure — l'élément est tracé, mais avec une dérive (terminologie, multiplicité, ou simplification volontaire à documenter) |
| ✗ | Non-conformité — l'élément est manquant ou contredit dans un diagramme |
| n/a | Non applicable — l'élément n'a pas vocation à apparaître dans ce diagramme |

Les sept passes sont :

1. **Audit des acteurs** — chaque rôle de la Section 1 est-il représenté de façon cohérente ?
2. **Audit du vocabulaire** — chaque entité de la Section 2 est-elle tracée avec les mêmes noms d'attributs partout ?
3. **Audit des états** — chaque état listé est-il atteignable dans un diagramme d'États et nommé identiquement dans le Class Diagram ?
4. **Audit des règles métier** — chaque BR est-elle implémentée par au moins un élément de diagramme ?
5. **Audit de la frontière BPMN ↔ UML** — la transposition Section 5.1 est-elle respectée ?
6. **Audit de la dérivation Activité → Classes** — chaque action Système a-t-elle sa méthode, et inversement ?
7. **Audit de la table de correspondance transversale** — chaque ligne de la Section 5.2 est-elle vérifiée ?

---

## 3. Passe 1 — Audit des Acteurs

| Acteur (Section 1) | BPMN (livrable 01) | Use Case (livrable 02) | UC-01 Activité | UC-02 Activité | UC-03 Activité | Class Diagram | Statut |
|---|---|---|---|---|---|---|---|
| **Client** | Piscine externe (boîte noire) | Acteur primaire (gauche) | Couloir Client | n/a (pas d'interaction directe dans flux nominal) | Couloir Client | `Customer` (classe) | ✓ |
| **Représentant commercial** | Couloir Vente | Acteur primaire (gauche) | Mention en note (extension `Override Stock Hold`) | Mention en note (article manquant) | n/a | n/a (rôle, pas entité) | ⚠ — voir 3.1 |
| **Opérateur d'entrepôt** | Couloir Exécution | Acteur primaire (gauche) | n/a | Couloir Opérateur d'entrepôt | n/a | n/a (rôle, pas entité) | ✓ |
| **Contrôleur financier** | Couloir Finance | Acteur primaire (gauche) | n/a | n/a | Couloir Contrôleur financier | n/a (rôle, pas entité) | ✓ |
| **Système ERP** | Piscine fermée externe (livrable 01 niveau 0) | Acteur secondaire (droite) | Mention dans actions Système (réservation, vérif stock) | Mention dans actions Système (déduction inventaire) | Mention dans actions Système (créance, rapprochement) | `ErpService` (interface) | ✓ |
| **Passerelle de paiement** | Piscine fermée externe (livrable 01 niveau 2.3) | Acteur secondaire (droite) | n/a | n/a | Ligne de vie Séquence | `PaymentGatewayService` (interface) | ✓ |
| **Service de notification** | Implicite dans tâches d'envoi | Acteur secondaire (droite) | Mention dans actions Système | Ligne de vie Séquence | Ligne de vie Séquence | `NotificationService` (interface) | ✓ |
| **Transporteur** | Piscine externe (boîte noire) | Acteur secondaire (droite) | n/a | Couloir Transporteur + ligne de vie Séquence | n/a | `CarrierService` (interface) | ✓ |

### 3.1 Anomalie mineure — Représentant commercial

Le Représentant commercial apparaît comme **acteur primaire** du Use Case Diagram (livrable 02) initiant `Place Order`, `Cancel Order` et `Override Stock Hold`. En revanche, il **n'apparaît pas comme couloir** dans les diagrammes d'Activité de UC-01 et UC-02. Justification trouvée dans les livrables :

- UC-01 (livrable 03, Section 2) : « Lorsqu'il agit *au nom du* Client, il opère via les mêmes écrans (couloir Client) » → choix défensable ; à la condition que l'écran « Passer une commande » soit conçu pour les deux profils (Client et Représentant commercial). À documenter dans la spécification de l'écran.
- UC-01 et UC-02 : `Override Stock Hold` est une extension (BR-06) qui aurait son propre diagramme d'Activité avec couloir Représentant commercial — cet UC d'extension **n'est pas couvert** dans le Study Case.

**Verdict :** ⚠ acceptable. Recommandation pour le livrable de spécification destiné au développement : ajouter un chapitre court documentant le cas `Override Stock Hold` (acteur, déclencheur, transitions d'état) — sans nécessiter un diagramme d'Activité complet.

---

## 4. Passe 2 — Audit du Vocabulaire (Entités, Attributs)

### 4.1 Entités

| Entité (Section 2) | BPMN | UC | Activité | Séquence | Class Diagram | Statut |
|---|---|---|---|---|---|---|
| `Customer` | Objet de données | n/a (acteur) | Mention | Mention | Classe `Customer` | ✓ |
| `Order` | Objet de données central | `Place Order`, `Cancel Order`, `Track Order` | Objet manipulé tout au long | Lignes de vie / messages | Classe `Order` | ✓ |
| `OrderLine` | Objet de données (Niveau 2.1) | n/a | Mention dans liste de prélèvement | Mention dans `pickList` | Classe `OrderLine` (composition) | ✓ |
| `Product` | Objet de données | n/a | Mention dans vérification stock | Mention dans `deductInventory` | Classe `Product` | ✓ |
| `StockReservation` | Objet de données (Niveau 2.1) | n/a | Action Réserver le stock | Mention | Classe `StockReservation` | ✓ |
| `Shipment` | Objet de données central (Niveau 2.2) | `Prepare Shipment`, `Confirm Dispatch` | Objet créé et tracé | Lignes de vie / messages | Classe `Shipment` | ✓ |
| `Invoice` | Objet de données central (Niveau 2.3) | `View Invoice`, `Pay Invoice`, `Issue Dunning Reminder` | Objet créé et tracé | Lignes de vie / messages | Classe `Invoice` | ✓ |
| `Payment` (abstrait) | Objet de données (Niveau 2.3) | `Pay Invoice`, `Handle Payment Failure` | Objet créé et tracé | Lignes de vie / messages | Classe abstraite `Payment` | ✓ |
| `StripePayment` | n/a | n/a | Mention en variante (mode de paiement) | Mention dans `initiatePayment(method)` | Classe concrète `StripePayment` (héritage) | ✓ |
| `BankTransfer` | n/a | n/a | Mention en variante (mode de paiement) | Mention dans `initiatePayment(method)` | Classe concrète `BankTransfer` (héritage) | ✓ |

### 4.2 Attributs critiques

| Attribut | Source du vocabulaire | Présence Class Diagram | Cohérence avec Section 2 | Statut |
|---|---|---|---|---|
| `Customer.customerId` | Section 2 | ✓ | identique | ✓ |
| `Customer.isPremium` | Section 2 (BR-04, BR-11) | ✓ | identique — pilote `markPriority()` et `setDueDate()` | ✓ |
| `Customer.creditLimit` | Section 2 | ✓ | identique — non exploité dans les Activités (sera utile en évolution) | ⚠ — voir 4.3 |
| `Order.totalAmount` | Section 2 | ✓ | identique | ✓ |
| `Order.deliveryAddress` | Section 2 | ✓ | identique — typé `String` (cf. variante `Address` dans Class Diagram) | ⚠ — voir 4.3 |
| `Order.isPriority` | dérivé de BR-04 | ✓ | présent dans Class Diagram, non listé en Section 2 | ⚠ — voir 4.3 |
| `OrderLine.lineTotal` | Section 2 | ✓ | identique | ✓ |
| `Product.stockQuantity` | Section 2 | ✓ | identique | ✓ |
| `Product.reorderLevel` | Section 2 | ✓ | identique — non exploité dans les Activités | ⚠ — voir 4.3 |
| `StockReservation.expiresAt` | Section 2 | ✓ | identique — la transition `Active → Expired` n'est pas tracée dans un diagramme | ⚠ — voir 4.3 |
| `Shipment.trackingNumber` | Section 2 | ✓ | identique | ✓ |
| `Invoice.amount` | Section 2 | ✓ | identique | ✓ |
| `Invoice.paidAmount` | dérivé du paiement partiel | ✓ | présent dans Class Diagram, non listé en Section 2 | ⚠ — voir 4.3 |
| `Invoice.dunningCount` | dérivé de BR-13 | ✓ | présent dans Class Diagram, non listé en Section 2 | ⚠ — voir 4.3 |
| `Payment.processedAt` | Section 2 | ✓ | identique | ✓ |
| `StripePayment.cardLast4` | Section 2 | ✓ | identique | ✓ |
| `BankTransfer.iban` | Section 2 | ✓ | identique | ✓ |

### 4.3 Anomalies mineures — Attributs dérivés non listés en Section 2

Plusieurs attributs apparaissent dans le Class Diagram **sans être listés explicitement** dans la Section 2 du `Study Case.md` : `Order.isPriority`, `Invoice.paidAmount`, `Invoice.dunningCount`. Ces attributs sont **justifiés par les règles métier** (BR-04 pour `isPriority`, BR-13 pour `dunningCount`, paiement partiel pour `paidAmount`) mais non préfigurés dans le vocabulaire de référence.

**Verdict :** ⚠ acceptable. Recommandation : enrichir la Section 2 du `Study Case.md` en y ajoutant ces trois attributs avec leur règle d'origine, pour que le vocabulaire métier reflète exhaustivement les besoins issus de l'analyse.

Inversement, certains attributs listés en Section 2 (`Customer.creditLimit`, `Product.reorderLevel`) sont présents dans le Class Diagram mais **jamais consultés** dans les diagrammes d'Activité. Ils sont tracés au cas où une évolution future les utiliserait, mais leur traçabilité reste faible. À mentionner dans le rapport de remise comme « attributs en réserve d'évolution ».

L'attribut `StockReservation.expiresAt` mérite une attention particulière : il définit la durée de vie d'une réservation, mais **aucun diagramme** ne trace la transition `Active → Expired` (un minuteur métier). C'est une omission consciente du Study Case (les minuteurs implicites ne sont pas modélisés dans les diagrammes d'Activité ; ils relèveraient d'un Bordure Minuteur BPMN). À documenter en règle BR-XX dans une évolution du document.

---

## 5. Passe 3 — Audit des États

Tous les états listés en Section 2 du `Study Case.md` doivent (a) être atteignables dans au moins un diagramme d'États, (b) être présents dans l'énumération correspondante du Class Diagram, (c) apparaître au moins une fois dans un diagramme de Séquence ou d'Activité comme transition.

### 5.1 États de `Order`

| État | Diagrammes d'États (UC-01) | Activité (UC-01/02) | Séquence (UC-01/02) | Énum `OrderStatus` | Statut |
|---|---|---|---|---|---|
| `Draft` | UC-01 | UC-01 (S1) | UC-01 | ✓ | ✓ |
| `Submitted` | UC-01 | UC-01 (S2) | UC-01 | ✓ | ✓ |
| `Confirmed` | UC-01 | UC-01 (S8) | UC-01 | ✓ | ✓ |
| `OnHold` | UC-01 | UC-01 (S12) | UC-01 | ✓ | ✓ |
| `InFulfilment` | UC-02 (encadré) | UC-02 (S2) | UC-02 | ✓ | ✓ |
| `Shipped` | UC-02 (encadré) | UC-02 (S7) | UC-02 | ✓ | ✓ |
| `Delivered` | UC-02 (encadré) | UC-02 (S10) | UC-02 (alt fragment) | ✓ | ✓ |
| `Closed` | UC-02 (encadré) | UC-02 (S12) | UC-02 (note temporisée) | ✓ | ✓ |
| `Cancelled` | UC-01 | UC-01 (chemin annulation) | n/a | ✓ | ✓ |

### 5.2 États de `Shipment`

| État | Diagramme d'États (UC-02) | Activité UC-02 | Séquence UC-02 | Énum `ShipmentStatus` | Statut |
|---|---|---|---|---|---|
| `Preparing` | ✓ | S1 | ✓ | ✓ | ✓ |
| `Ready` | ✓ | O3 | ✓ | ✓ | ✓ |
| `Dispatched` | ✓ | S6 | ✓ | ✓ | ✓ |
| `Delivered` | ✓ | S10 | ✓ | ✓ | ✓ |
| `Exception` | ✓ | (chemin exception, fragment alt) | ✓ | ✓ | ✓ |

### 5.3 États de `Invoice`

| État | Diagramme d'États (UC-03) | Activité UC-03 | Séquence UC-03 | Énum `InvoiceStatus` | Statut |
|---|---|---|---|---|---|
| `Draft` | ✓ | S1 | ✓ | ✓ | ✓ |
| `Sent` | ✓ | S3 | ✓ | ✓ | ✓ |
| `PartiallyPaid` | ✓ | S14 | ✓ (alt imbriqué) | ✓ | ✓ |
| `Paid` | ✓ | S9 | ✓ | ✓ | ✓ |
| `Overdue` | ✓ | S16 | ✓ (opt fragment) | ✓ | ✓ |
| `Escalated` | ✓ | S21 | ✓ | ✓ | ✓ |
| `Cancelled` | listé pour cohérence, non tracé | n/a (hors périmètre — retours) | n/a | ✓ | ⚠ — voir 5.5 |

### 5.4 États de `Payment`

| État | Diagramme d'États (UC-03) | Activité UC-03 | Séquence UC-03 | Énum `PaymentStatus` | Statut |
|---|---|---|---|---|---|
| `Pending` | ✓ | (état initial à `S6`) | implicite | ✓ | ✓ |
| `Authorised` | ✓ | (étape avant `S8`) | ✓ | ✓ | ✓ |
| `Captured` | ✓ | S9 | ✓ | ✓ | ✓ |
| `Failed` | ✓ | S12 | ✓ (alt branche) | ✓ | ✓ |

### 5.5 Anomalie mineure — État `Invoice.Cancelled`

L'état `Cancelled` est listé dans la Section 2 du `Study Case.md` pour `Invoice` mais **aucune transition** ne l'atteint dans les diagrammes UC-03 (ni dans le diagramme d'États, ni dans l'Activité, ni dans la Séquence). Justification trouvée dans le livrable 05 (Section 3) : « `Cancelled` correspond à un retour, hors périmètre du Study Case (cf. Section 7) ». L'état est volontairement listé pour cohérence avec la Section 2 mais non tracé.

**Verdict :** ⚠ acceptable et documenté. Recommandation : ajouter une note explicite dans le diagramme d'États `Invoice` (« État `Cancelled` réservé au processus de retour, hors périmètre »).

### 5.6 États de `Customer`, `Product`, `StockReservation`

| Entité | États listés Section 2 | Couverture | Statut |
|---|---|---|---|
| `Customer` | `Active`, `Suspended`, `Blocked` | `Active` (préconditions UC-01) ; `Suspended` (BR-13 escalade UC-03) ; `Blocked` non tracé (état initial possible avant `Active`) | ⚠ — `Blocked` jamais tracé |
| `Product` | `Available`, `LowStock`, `OutOfStock`, `Discontinued` | `Available` (implicite) ; `LowStock`/`OutOfStock` indirectement liés à BR-03 (stock insuffisant) ; `Discontinued` jamais tracé | ⚠ — `Discontinued` jamais tracé |
| `StockReservation` | `Active`, `Released`, `Expired` | `Active` (création UC-01) ; `Released` (BR-09 UC-02) ; `Expired` jamais tracé (minuteur implicite) | ⚠ — `Expired` jamais tracé |

**Verdict :** ⚠ acceptable. Ces états correspondent à des **chemins hors périmètre** du Study Case (gestion catalogue produits, blocage manuel d'un compte client, expiration automatique de réservation) — ils sont listés en Section 2 pour exhaustivité du vocabulaire, sans être consommés. Recommandation : annoter la Section 2 du `Study Case.md` pour distinguer les états couverts par les UC du Study Case et ceux laissés en réserve d'évolution.

---

## 6. Passe 4 — Audit des Règles Métier

| Règle | Énoncé synthétique | Diagramme(s) où elle apparaît | Statut |
|---|---|---|---|
| **BR-01** | Client `Active` requis pour passer Commande | UC-01 (décision S4 dans Activité ; transition `Submitted → Confirmed/Cancelled` dans États) | ✓ |
| **BR-02** | Au moins une `OrderLine` par Commande | UC-01 (mention validation S2-S3) ; Class Diagram (composition `1..*`) | ✓ |
| **BR-03** | Réservation de stock à la confirmation | UC-01 (S5-S7 + état `OnHold`) ; Class Diagram (`StockReservation.reserve()`) | ✓ |
| **BR-04** | Clients Premium prioritaires | UC-01 (décision S9 + action S10) ; Class Diagram (`Order.markPriority()`, `Customer.isPremium`) | ✓ |
| **BR-05** | Annulation possible avant `InFulfilment` | UC-01 (transition `Draft → Cancelled` dans diagramme d'États) ; Class Diagram (`Order.cancel()`) | ⚠ — voir 6.1 |
| **BR-06** | Représentant commercial peut autoriser expédition partielle Premium | UC-01 (note) ; Use Case Diagram (`Override Stock Hold` extend) | ⚠ — voir 6.1 |
| **BR-07** | Expédition créée à partir d'une Commande `Confirmed` | UC-02 (S1, garde implicite) ; Class Diagram (`Shipment.createShipment(order)`) | ✓ |
| **BR-08** | Opérateur valide prélèvement/emballage avant `Ready` | UC-02 (O3 dans Activité ; transition `Preparing → Ready` dans États) | ✓ |
| **BR-09** | Collecte transporteur ⇒ Commande `Shipped` + notification | UC-02 (S6→S7→S8 ; transition `Dispatched` dans États) | ✓ |
| **BR-10** | Livraison confirmée par Transporteur OU Client ⇒ `Delivered` ; clôture après 7 j | UC-02 (S10-S12 ; alt Séquence ; transition `Delivered → Closed` dans États) | ✓ |
| **BR-11** | Facture générée à `Shipment Dispatched` ; échéance 30/45 j | UC-03 (S1-S2 ; déclencheur initial du diagramme) | ✓ |
| **BR-12** | Échéance dépassée ⇒ `Overdue` + alerte Contrôleur financier | UC-03 (S15→S16→S17 ; transition `Sent → Overdue` dans États) | ✓ |
| **BR-13** | Jusqu'à 3 relances espacées de 7 j ; au-delà escalade | UC-03 (boucle F1→F2→S18-S20 ; loop fragment Séquence ; transition réflexive `Overdue → Overdue`) | ✓ |
| **BR-14** | Paiement complet seulement à la capture | UC-03 (deux étapes distinctes Authorise/Capture dans Activité, États `Payment`, et Séquence) | ✓ |
| **BR-15** | Capture déclenche rapprochement ERP + `Invoice.Paid` | UC-03 (S10 dans Activité ; `postReconciliation` dans Séquence ; `ErpService.postReconciliation()` dans Class Diagram) | ✓ |

### 6.1 Anomalies mineures — BR-05 et BR-06

**BR-05 (Annulation possible avant `InFulfilment`)** : la règle est correctement tracée dans le diagramme d'États UC-01 (`Draft → Cancelled`), mais aucune **garde** explicite n'apparaît dans le diagramme d'Activité de UC-01 sur `Order.cancel()`. Pas de garde pour empêcher l'invocation de `cancel()` sur une Commande déjà en `InFulfilment` ou plus loin. La logique de protection est portée par la machine à états — ce qui est correct architecturalement, mais la règle n'est pas visible dans le seul diagramme d'Activité. **Verdict :** ⚠ acceptable, à documenter dans la spécification d'implémentation comme « `Order.cancel()` doit lever une exception si `status` ∉ {`Draft`, `Submitted`, `Confirmed`, `OnHold`} ».

**BR-06 (Override Stock Hold par Représentant commercial pour Client Premium)** : la règle est citée comme **extension** dans le Use Case Diagram (livrable 02) et mentionnée en note dans UC-01 (livrable 03), mais **aucun diagramme d'Activité, d'États ni de Séquence** ne la modélise concrètement. C'est une omission délibérée du Study Case (l'extension UC est mentionnée mais non développée). **Verdict :** ⚠ acceptable. Recommandation : produire au minimum un diagramme d'Activité d'extension pour `Override Stock Hold` dans une seconde itération de la spécification, avec un couloir Représentant commercial et une transition `OnHold → InFulfilment` (plus expédition partielle).

---

## 7. Passe 5 — Audit de la Frontière BPMN ↔ UML

Vérification ligne par ligne de la table de transposition (Section 5.1 du `Study Case.md`).

| Règle de transposition | Application dans le Study Case | Statut |
|---|---|---|
| Piscine OMS NovaTrade ⇒ frontière du système (Use Case) | BPMN livrable 01 a une piscine NovaTrade ; Use Case livrable 02 a la frontière `OMS NovaTrade — Frontière du système` qui contient tous les UC | ✓ |
| Piscine externe fermée ⇒ Acteur primaire/secondaire | Piscines `Client`, `Transporteur` du BPMN deviennent acteurs `Client` (primaire) et `Transporteur` (secondaire) du Use Case | ✓ |
| Couloir d'une piscine NovaTrade ⇒ Acteur primaire | Couloirs `Vente`, `Exécution`, `Finance` du BPMN deviennent acteurs `Représentant commercial`, `Opérateur d'entrepôt`, `Contrôleur financier` | ✓ |
| Tâche métier supportée par le système ⇒ Cas d'utilisation | Tâches BPMN « Soumettre commande », « Préparer expédition », « Capturer paiement » deviennent UC `Place Order`, `Prepare Shipment`, `Pay Invoice`… | ✓ |
| Tâche métier non supportée par le système ⇒ pas de transposition UML | Aucune dans le périmètre Study Case — toutes les tâches du BPMN sont supportées par l'OMS | ✓ |
| Objet de données ⇒ attribut/classe Class Diagram | Objets BPMN `Order`, `Shipment`, `Invoice`, `Payment` deviennent classes Class Diagram avec mêmes noms | ✓ |
| Flux de message vers système externe ⇒ diagramme de Séquence | Flux BPMN vers `Transporteur`, `Passerelle de paiement`, `Service ERP` ⇒ lignes de vie correspondantes dans les diagrammes de Séquence UC-02/UC-03 | ✓ |
| Événement déclenchant tâche métier ⇒ déclencheur UC | Événement de Début Message du BPMN « Réception commande » ⇒ déclencheur du UC `Place Order` ; Événement Minuteur « Échéance dépassée » ⇒ déclencheur de `Issue Dunning Reminder` | ✓ |
| Événement de changement d'état d'entité statefull ⇒ transition diagramme d'États | Événement BPMN « Expédition expédiée » ⇒ transition `Shipment: Ready → Dispatched` ; « Facture en retard » ⇒ `Invoice: Sent → Overdue` | ✓ |

**Verdict global Passe 5 :** ✓ — la frontière BPMN ↔ UML est strictement respectée.

---

## 8. Passe 6 — Audit de la Dérivation Activité → Classes

C'est la **passe centrale** de l'audit, conformément à la convention de couloirs Acteurs/Système (encadré du `Study Case.md`). Pour chaque action dans un couloir Système d'un diagramme d'Activité, on vérifie l'existence de la méthode correspondante dans le Class Diagram. Inversement, chaque méthode publique non-getter du Class Diagram doit avoir au moins une action d'Activité qui la déclenche.

### 8.1 Actions Système → Méthodes (couverture descendante)

#### UC-01

| Action couloir Système | Méthode correspondante | Classe | Statut |
|---|---|---|---|
| Créer la commande Draft | `Order.createDraft()` | `Order` | ✓ |
| Marquer Submitted | `Order.submit()` | `Order` | ✓ |
| Vérifier statut Client (BR-01) | `Customer.canPlaceOrder()` | `Customer` | ✓ |
| Vérifier le stock via ERP | `Product.isAvailable()` | `Product` | ✓ |
| Réserver le stock | `StockReservation.reserve()` | `StockReservation` | ✓ |
| Marquer Confirmed | `Order.confirm()` | `Order` | ✓ |
| Marquer prioritaire (BR-04) | `Order.markPriority()` | `Order` | ✓ |
| Envoyer notification | `NotificationService.sendOrderConfirmation()` | `NotificationService` | ✓ |
| Marquer OnHold | `Order.markOnHold()` | `Order` | ✓ |
| Notifier Représentant commercial | `NotificationService.notifySalesRep()` | `NotificationService` | ✓ |

#### UC-02

| Action couloir Système | Méthode correspondante | Classe | Statut |
|---|---|---|---|
| Créer Shipment Preparing | `Shipment.createShipment()` | `Shipment` | ✓ |
| Marquer Order InFulfilment | `Order.markInFulfilment()` | `Order` | ✓ |
| Générer liste de prélèvement | `Shipment.generatePickList()` | `Shipment` | ✓ |
| Générer étiquette de transport | `Shipment.generateShippingLabel()` | `Shipment` | ✓ |
| Notifier Transporteur | `CarrierService.requestPickup()` | `CarrierService` | ✓ |
| Enregistrer n° de suivi | `Shipment.recordTrackingNumber()` | `Shipment` | ✓ |
| Marquer Shipment Dispatched | `Shipment.markDispatched()` | `Shipment` | ✓ |
| Marquer Order Shipped (BR-09) | `Order.markShipped()` | `Order` | ✓ |
| Notifier Client (suivi) | `NotificationService.sendShippingNotification()` | `NotificationService` | ✓ |
| Mettre à jour stock | `Product.deductInventory()` | `Product` | ✓ |
| Marquer Shipment Delivered (BR-10) | `Shipment.markDelivered()` | `Shipment` | ✓ |
| Marquer Order Delivered | `Order.markDelivered()` | `Order` | ✓ |
| Marquer Order Closed | `Order.markClosed()` | `Order` | ✓ |

#### UC-03

| Action couloir Système | Méthode correspondante | Classe | Statut |
|---|---|---|---|
| Générer la facture (BR-11) | `Invoice.generateInvoice()` | `Invoice` | ✓ |
| Calculer date d'échéance | `Invoice.setDueDate()` | `Invoice` | ✓ |
| Marquer Invoice Sent | `Invoice.markSent()` | `Invoice` | ✓ |
| Envoyer facture au Client | `NotificationService.sendInvoice()` | `NotificationService` | ✓ |
| Publier créance dans ERP | `ErpService.postReceivable()` | `ErpService` | ✓ |
| Appeler Passerelle (autoriser) | `PaymentGatewayService.authorisePayment()` | `PaymentGatewayService` | ✓ |
| Capturer le paiement | `PaymentGatewayService.capturePayment()` | `PaymentGatewayService` | ✓ |
| Marquer Payment Captured (BR-14) | `Payment.markCaptured()` | `Payment` | ✓ |
| Marquer Invoice Paid | `Invoice.markPaid()` | `Invoice` | ✓ |
| Marquer Invoice PartiallyPaid | `Invoice.markPartiallyPaid()` | `Invoice` | ✓ |
| Envoyer rapprochement ERP (BR-15) | `ErpService.postReconciliation()` | `ErpService` | ✓ |
| Notifier Client (paiement OK) | `NotificationService.sendPaymentConfirmation()` | `NotificationService` | ✓ |
| Journaliser échec + notifier | `Payment.markFailed()` + `NotificationService.sendPaymentFailureNotice()` | `Payment` + `NotificationService` | ✓ |
| Marquer Overdue (BR-12) | `Invoice.markOverdue()` | `Invoice` | ✓ |
| Alerter Contrôleur financier | `NotificationService.alertFinanceController()` | `NotificationService` | ✓ |
| Incrémenter compteur de relances | `Invoice.incrementDunningCount()` | `Invoice` | ✓ |
| Envoyer relance | `NotificationService.sendDunningReminder()` | `NotificationService` | ✓ |
| Marquer Escalated | `Invoice.escalate()` | `Invoice` | ✓ |
| Notifier escalade | `NotificationService.notifyEscalation()` | `NotificationService` | ✓ |
| Suspendre compte Client | `Customer.suspend()` | `Customer` | ✓ |

### 8.2 Méthodes du Class Diagram → Actions d'Activité (couverture ascendante)

L'inverse est également vérifié : on parcourt chaque méthode publique du Class Diagram (hors getters/setters basiques) et on identifie l'action d'Activité qui la déclenche.

| Méthode | UC déclencheur | Statut |
|---|---|---|
| `Customer.isActive()` / `canPlaceOrder()` | UC-01 (S3-S4) | ✓ |
| `Customer.suspend()` | UC-03 (BR-13 escalade) | ✓ |
| `Order.createDraft()` | UC-01 (S1) | ✓ |
| `Order.submit()` | UC-01 (S2) | ✓ |
| `Order.validate()` | UC-01 (validation implicite avant `S8`) | ✓ |
| `Order.confirm()` | UC-01 (S8) | ✓ |
| `Order.markOnHold()` | UC-01 (S12) | ✓ |
| `Order.markPriority()` | UC-01 (S10) | ✓ |
| `Order.cancel()` | UC-01 (chemin annulation) | ✓ |
| `Order.markInFulfilment()`, `markShipped()`, `markDelivered()`, `markClosed()` | UC-02 (S2, S7, après S10, S12) | ✓ |
| `Order.calculateTotal()` | UC-01 (implicite — récapitulatif C4) | ✓ |
| `OrderLine.calculateLineTotal()` | UC-01 (implicite — récapitulatif C4) | ✓ |
| `Product.isAvailable()` | UC-01 (S5) | ✓ |
| `Product.deductInventory()` | UC-02 (S9 dans Séquence) | ✓ |
| `StockReservation.reserve()` | UC-01 (S7) | ✓ |
| `StockReservation.release()` | UC-02 (à la déduction effective) | ✓ |
| `StockReservation.isActive()` | UC-02 (préconditions) | ✓ |
| `Shipment.*` (createShipment, generatePickList, generateShippingLabel, markReady, recordTrackingNumber, markDispatched, markDelivered, markException) | UC-02 (intégralement) | ✓ |
| `Invoice.*` (generateInvoice, setDueDate, markSent, markPaid, markPartiallyPaid, markOverdue, incrementDunningCount, triggerDunningReminder, escalate, getRemainingBalance, isFullyPaid) | UC-03 (intégralement) | ✓ |
| `Payment.*` + redéfinitions Stripe/BankTransfer | UC-03 (intégralement) | ✓ |
| `NotificationService.*` (10 méthodes) | UC-01/02/03 (chacune mappée) | ✓ |
| `CarrierService.*` (2 méthodes) | UC-02 (séquence) | ✓ |
| `PaymentGatewayService.*` (2 méthodes) | UC-03 (séquence) | ✓ |
| `ErpService.*` (2 méthodes) | UC-03 (S5, S10) | ✓ |

**Aucune méthode orpheline détectée.** La règle de dérivation Activité → Classes est respectée intégralement.

### 8.3 Vérification des couloirs d'Acteurs (écrans)

Conformément à la règle de dérivation, chaque action d'un couloir d'Acteur doit correspondre à un **écran** de l'application — point d'interaction de cet acteur avec le système. L'audit ne valide pas les écrans (ils relèvent du livrable IHM, hors Study Case) mais s'assure que les actions des couloirs Acteur sont **identifiables** et constituent une liste cohérente.

| UC | Couloir | Actions ⇒ Écrans candidats | Cohérence avec Use Case Diagram |
|---|---|---|---|
| UC-01 | Client | Parcourir catalogue, Sélectionner produits/quantités, Fournir adresse, Examiner récapitulatif, Soumettre commande | ✓ — recouvre `Browse Catalogue`, `Place Order` |
| UC-02 | Opérateur d'entrepôt | Récupérer liste de prélèvement, Prélever et emballer, Marquer Ready | ✓ — recouvre `Prepare Shipment` |
| UC-02 | Transporteur | Récupérer colis, Confirmer livraison | ✓ — interface externe (pas un écran OMS, mais un point d'API) |
| UC-03 | Client | Examiner facture, Choisir mode de paiement, Soumettre paiement | ✓ — recouvre `View Invoice`, `Pay Invoice` |
| UC-03 | Contrôleur financier | Examiner facture en retard, Déclencher relance, Escalader | ✓ — recouvre `Issue Dunning Reminder`, `Manage Overdue Accounts` |

**Verdict :** ✓ — les actions des couloirs Acteur projettent une liste d'écrans cohérente avec les UC du livrable 02.

---

## 9. Passe 7 — Audit de la Table de Correspondance Transversale (Section 5.2)

Vérification ligne par ligne de la table de correspondance par concept (Section 5.2 du `Study Case.md`).

| Concept | BPMN | UC | Classe / Attribut | Libellé d'État | Message Séquence | Action d'Activité | Statut |
|---|---|---|---|---|---|---|---|
| Le Client soumet une commande | Événement de début message ✓ | `Place Order` ✓ | `Order` (entité) ; `Customer` ✓ | `Order: Draft→Submitted` ✓ | `submitOrder()` ⇒ trace dans UC-01 Activité ✓ | *Submit Order* ✓ | ✓ |
| L'OMS valide la commande | Tâche de service ✓ | n/a | `Order.validate()` ✓ | `Order: Submitted→Confirmed` ✓ | `validateOrder()` ✓ | *Validate Order* ✓ | ✓ |
| Réservation de stock | Tâche de service ✓ | n/a | `StockReservation` ✓ | `StockReservation: Active` ✓ | `reserveStock()` ✓ | *Reserve Stock* ✓ | ✓ |
| Commande mise en attente | Événement intermédiaire ✓ | n/a | `Order.status = OnHold` ✓ | `Order: OnHold` ✓ | `notifyHold()` (≈`notifySalesRep`) ✓ | *Notify Sales Rep* ✓ | ✓ |
| Expédition créée | Tâche de service ✓ | `Prepare Shipment` ✓ | `Shipment` ✓ | `Shipment: Preparing` ✓ | `createShipment()` ✓ | *Create Shipment* ✓ | ✓ |
| Collecte par le transporteur | Événement intermédiaire de réception message ✓ | `Confirm Dispatch` ✓ | `Shipment.trackingNumber` ✓ | `Shipment: Dispatched` ✓ | `confirmPickup()` (≈`pickupConfirmed`) ✓ | *Record Tracking* ✓ | ✓ |
| Facture générée | Tâche de service ✓ | `Pay Invoice` (déclencheur) ✓ | `Invoice` ✓ | `Invoice: Draft→Sent` ✓ | `generateInvoice()` ✓ | *Generate Invoice* ✓ | ✓ |
| Autorisation de paiement | Tâche de service ✓ | `Pay Invoice` ✓ | `Payment.process()` (≈`authorisePayment`) ✓ | `Payment: Pending→Authorised` ✓ | `authorisePayment()` ✓ | *Authorise Payment* ✓ | ✓ |
| Capture de paiement | Tâche de service ✓ | `Pay Invoice` ✓ | `Payment.capture()` ✓ | `Payment: Authorised→Captured` ✓ | `capturePayment()` ✓ | *Capture Payment* ✓ | ✓ |
| Facture en retard | Événement de bordure temporel ✓ | n/a | `Invoice.status = Overdue` ✓ | `Invoice: Overdue` ✓ | `markOverdue()` ✓ | *Transition to Overdue* ✓ | ✓ |
| Rapprochement ERP | Tâche de service ✓ | n/a | `ErpService.postReconciliation()` ✓ | (transition implicite Invoice → Paid) | `postReconciliation()` ✓ | *Post to ERP* ✓ | ✓ |

**Verdict global Passe 7 :** ✓ — la table de correspondance transversale est intégralement vérifiée.

---

## 10. Synthèse des Constats et Anomalies

### 10.1 Anomalies recensées

| # | Anomalie | Sévérité | Recommandation |
|---|---|---|---|
| A1 | Représentant commercial sans couloir d'Activité | ⚠ mineure | Documenter le pattern d'écrans partagés Client/SalesRep dans la spécification IHM ; produire le diagramme d'Activité de l'extension `Override Stock Hold` |
| A2 | Attributs `Order.isPriority`, `Invoice.paidAmount`, `Invoice.dunningCount` non listés en Section 2 | ⚠ mineure | Enrichir la Section 2 du `Study Case.md` avec ces trois attributs et leur règle d'origine |
| A3 | Attributs `Customer.creditLimit`, `Product.reorderLevel` listés mais jamais consultés | ⚠ mineure | Mentionner comme « réserve d'évolution » dans la spécification |
| A4 | Transition `StockReservation: Active → Expired` non tracée (minuteur implicite) | ⚠ mineure | Ajouter un Bordure Minuteur BPMN dans une évolution future ; ou créer une BR-XX explicite |
| A5 | État `Invoice.Cancelled` listé mais non tracé (retours hors périmètre) | ⚠ documentée | Ajouter une note dans le diagramme d'États `Invoice` |
| A6 | États `Customer.Blocked`, `Product.Discontinued`, `StockReservation.Expired` listés mais non tracés | ⚠ documentée | Annoter la Section 2 pour distinguer les états du périmètre Study Case et les états en réserve |
| A7 | BR-05 — pas de garde explicite dans le diagramme d'Activité (logique portée par la machine à états) | ⚠ acceptable | Documenter la précondition de `Order.cancel()` dans la spécification d'implémentation |
| A8 | BR-06 — extension `Override Stock Hold` non développée | ⚠ documentée | Produire un diagramme d'Activité d'extension dans une seconde itération |

### 10.2 Points forts du modèle consolidé

L'audit fait également ressortir plusieurs choix de conception **particulièrement réussis** qui méritent d'être mis en avant dans le rapport de remise à l'équipe de développement :

- **BR-14 (autorisation vs. capture)** est tracée de manière exemplaire à travers trois diagrammes : deux étapes distinctes dans l'Activité UC-03, deux transitions distinctes dans le diagramme d'États `Payment`, et deux appels distincts dans le diagramme de Séquence. C'est un cas d'école de cohérence inter-diagrammes.
- **BR-13 (boucle de relance bornée)** est portée de manière cohérente par : la transition réflexive `Overdue → Overdue` du diagramme d'États `Invoice`, le fragment `loop` du diagramme de Séquence UC-03, et l'attribut `Invoice.dunningCount` du Class Diagram.
- **Synchronisation `Invoice ↔ Payment`** : la double horloge (cycle propre `Invoice` + cycle propre `Payment`) reflète fidèlement la réalité métier où plusieurs paiements peuvent régler une seule facture, sans fusionner artificiellement les deux cycles.
- **Pattern « interface » uniformément appliqué** aux quatre services externes (`NotificationService`, `CarrierService`, `PaymentGatewayService`, `ErpService`) — facilite le découplage et les tests, et respecte le principe d'inversion de dépendance.
- **Polymorphisme `Payment` ◁— `StripePayment` / `BankTransfer`** : un cas exemplaire de hiérarchie d'héritage motivée par des attributs spécifiques distincts (token Stripe vs. IBAN) et des opérations à redéfinir (`process()`, `capture()`).
- **`Customer.canPlaceOrder()`** comme méthode existante dès UC-01 : elle bénéficie sans modification de l'évolution UC-03 (suspension après escalade BR-13). C'est un cas d'école de **dividende d'un découpage initial bien fait**.

### 10.3 Couverture globale

| Dimension auditée | Couverture | Anomalies |
|---|---|---|
| Acteurs (8) | 100 % | 1 mineure (A1) |
| Entités (10) | 100 % | 0 |
| Attributs (≈40) | 100 % | 3 mineures (A2, A3) |
| États (29) | 86 % tracés ; 14 % en réserve documentée | 4 documentées (A4, A5, A6) |
| Règles métier (15) | 100 % implémentées | 2 mineures (A7, A8) |
| Frontière BPMN ↔ UML (9 règles) | 100 % | 0 |
| Dérivation Activité → Classes | 100 % (aucune méthode orpheline) | 0 |
| Table de correspondance transversale (11 lignes) | 100 % | 0 |

**Verdict global de l'audit :** ✓ **modèle prêt pour la phase d'implémentation**, sous réserve de documenter les huit anomalies mineures dans le rapport de remise à l'équipe de développement.

---

## 11. Recommandations pour la Suite

1. **Produire un rapport de remise à l'équipe de développement** synthétisant les choix architecturaux et les huit points d'attention listés en Section 10.1. Ce rapport doit accompagner la spécification multi-diagrammes en entrée du sprint d'implémentation.

2. **Compléter le périmètre dans une seconde itération** :
   - Diagramme d'Activité d'extension `Override Stock Hold` (BR-06)
   - Modélisation explicite de l'expiration des réservations de stock (`StockReservation: Active → Expired`)
   - Si les retours rentrent dans le périmètre : tracer la transition `Invoice: Sent/Paid → Cancelled` et l'UC `Process Return` associé

3. **Enrichir la Section 2 du `Study Case.md`** avec les attributs dérivés (`Order.isPriority`, `Invoice.paidAmount`, `Invoice.dunningCount`) pour fermer la boucle de traçabilité vocabulaire ⇒ Class Diagram.

4. **Documenter dans la spécification d'implémentation** les préconditions des méthodes critiques (`Order.cancel()`, `Invoice.escalate()`, `Customer.suspend()`) pour que l'implémentation lève les exceptions appropriées en cas d'invocation hors séquence.

5. **Maintenir l'audit dans la durée** : reproduire la grille des sept passes à chaque évolution majeure du modèle. C'est un coût récurrent mais le seul moyen d'éviter la dérive — les six livrables précédents constituent un système couplé, où une modification d'un diagramme peut propager une incohérence.

---

## 12. Conclusion

Le Study Case NovaTrade est **complet et cohérent**. Les sept passes d'audit confirment que :

- Le **vocabulaire** du domaine (Section 2 du `Study Case.md`) est uniformément utilisé sur les six livrables.
- Les **15 règles métier** sont toutes implémentées par au moins un élément concret de diagramme.
- La **frontière BPMN ↔ UML** est respectée — aucune dérive entre la modélisation métier (livrable 01) et la modélisation système (livrables 02 à 06).
- La **dérivation Activité → Classes** — l'objectif pédagogique central de la formation — est intégralement vérifiée : chaque action Système possède sa méthode dans le Class Diagram, et chaque méthode publique du Class Diagram a au moins une action d'Activité qui la déclenche.

Les **huit anomalies mineures** détectées sont toutes documentées et acceptables dans le périmètre du Study Case. Aucune anomalie bloquante n'a été identifiée ; le modèle est prêt à servir de spécification de référence à l'équipe de développement.

Cet audit clôt formellement le Study Case. Les apprenants ont parcouru, livrable après livrable, l'intégralité de la chaîne d'analyse BPMN → Use Case → Activité → États → Séquence → Classes, et ont vu de leurs yeux pourquoi cette chaîne est imposée dans cet ordre : chaque maillon prépare le suivant, et l'audit final ne peut produire un verdict positif que si chaque maillon a été forgé proprement.

---

*Fin du Study Case NovaTrade. Livrable suivant : aucun — la spécification multi-diagrammes est complète et prête pour la phase d'implémentation.*
