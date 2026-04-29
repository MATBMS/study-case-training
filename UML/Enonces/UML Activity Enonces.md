# Exercices — Diagramme d'Activité UML

Cette page contient neuf exercices progressifs, tous situés dans **NovaTrade**, une société de négoce B2B de taille moyenne. Les exercices se concentrent sur le diagramme d'activité (*Activity Diagram*) UML appliqué à la **spécification d'un Système d'Information**, partant d'un flux linéaire à deux couloirs (`Acteur` + `SI`) pour aboutir à une activité multi-acteurs avec exécution parallèle, nœuds d'objet (*Object Nodes*) et actions d'appel de comportement (*Call Behavior Actions*).

> [!warning] Cadre de modélisation : Acteur(s) frontend + SI backend
> En UML, le diagramme d'activité sert à spécifier le comportement d'un **Système d'Information** (SI). Chaque exercice utilise donc systématiquement deux types de partitions (couloirs — *swimlanes*) :
> - une partition par **acteur humain** : capture les actions effectuées via l'**interface frontend** (saisir, cliquer, consulter à l'écran)
> - une partition unique **`Système d'Information`** : capture toutes les opérations **backend automatisées** (valider, calculer, persister, appeler une API, envoyer un courriel)
>
> Les actions du couloir SI deviennent directement des **opérations sur les classes** ([[UML Class]]) et les traversées de partition entre Acteur et SI deviennent les **messages** ([[UML Sequence]]). C'est ce qui assure la cohérence inter-diagrammes UML.

Chaque exercice est autonome mais s'appuie sur le précédent. Les débutants devraient compléter les trois exercices de niveau débutant avant de passer au niveau intermédiaire.

## 🟢 Niveau Débutant

*Les exercices débutants introduisent le pattern Acteur + SI avec les briques de base : Nœud Initial (*Initial Node*), Actions, Flux de Contrôle (*Control Flows*), nœuds de Décision/Fusion (*Decision/Merge*) et un seul Nœud Final d'Activité (*Activity Final Node*). Pas encore de Fourche/Jointure ni de Nœuds d'Objet.*

### Exercice 01 — Soumission d'une Note de Frais

Chez NovaTrade, un employé soumet une note de frais via le portail interne. L'**interface frontend** (le portail web) capte les actions de l'utilisateur (saisie, clics) ; le **Système d'Information** (SI) prend ensuite en charge tous les traitements automatisés : validation, calcul, persistance, notification. Pour cette première itération, on se concentre uniquement sur la phase de soumission — pas encore d'approbation manager ni de virement.

Votre mission est de modéliser ce flux linéaire en distinguant clairement les actions **utilisateur** des actions du **SI**.

### Exercice 02 — Validation d'une Facture avec Décision

Le Comptable de NovaTrade saisit les factures fournisseurs dans le portail. Le SI doit valider le format (numéro de TVA, IBAN, montant cohérent) avant tout enregistrement. **Si la validation échoue**, un message de rejet s'affiche au Comptable, qui peut corriger ou abandonner. **Si elle réussit**, la facture est enregistrée, un accusé est généré et affiché.

Votre mission est de modéliser ce flux avec une **décision dans le couloir SI** et un retour conditionnel à l'utilisateur.

### Exercice 03 — Vérification de Stock à Trois Voies

Quand un Client de NovaTrade soumet une commande via le portail web, le SI vérifie la disponibilité du stock pour chaque article. **Trois résultats** sont possibles : entièrement en stock (réserver la quantité totale), partiellement en stock (réserver le disponible **et** créer une commande en attente — *backorder* — pour le reste), ou rupture totale (marquer comme indisponible). Le Client voit le résultat à l'écran : confirmation de commande dans les deux premiers cas, message d'indisponibilité dans le troisième.

Votre mission est de modéliser un **Nœud de Décision à trois voies** dans le couloir SI, avec convergence des deux chemins de succès via un Nœud de Fusion.

## 🟡 Niveau Intermédiaire

*Les exercices intermédiaires introduisent **plusieurs acteurs humains** en plus du SI, les barres de Fourche/Jointure pour l'exécution parallèle d'opérations système, et les Nœuds d'Objet (*Object Nodes*) montrant les transitions d'état des entités du domaine.*

### Exercice 04 — Approbation de Facture Multi-Acteurs

NovaTrade affine son processus d'approbation de facture. Le **Comptable** saisit la facture dans le portail. Le SI valide le format puis route automatiquement selon le montant : factures < 5 000 € → **Contrôleur**, factures ≥ 5 000 € → **Manager**. L'approbateur consulte la facture à l'écran et clique « Approuver » (ou rejette — non couvert dans cet exercice). Le SI enregistre l'approbation, exécute le paiement via l'**ERP** (*Enterprise Resource Planning*, considéré comme partie du SI ici) et notifie le Comptable.

Votre mission est de modéliser ce processus avec **trois acteurs humains** + un **couloir SI** unique. Toutes les décisions de routage et toutes les opérations automatisées sont dans le couloir SI ; les couloirs humains ne contiennent que des actions effectuées via l'interface.

### Exercice 05 — Préparation de Commande Parallèle (Fourche/Jointure dans le SI)

Quand le **Représentant Commercial** de NovaTrade confirme une commande, le SI lance **trois opérations backend en parallèle** : création d'un ordre de prélèvement à destination de l'entrepôt (système interne), génération du PDF de facture et envoi par courriel au client, réservation d'un transporteur via une API logistique externe. Une fois les trois opérations terminées, le SI marque la commande comme « Préparée » et notifie le Représentant à l'écran.

Votre mission est de modéliser cette **exécution parallèle dans le couloir SI** avec une paire **Fourche/Jointure** (*Fork/Join*).

### Exercice 06 — Cycle de Vie d'une Facture avec Nœuds d'Objet

L'équipe conformité de NovaTrade veut visualiser le **cycle de vie de l'objet `Facture`** au fur et à mesure qu'il change d'état durant le processus d'approbation. Le Comptable saisit la facture, le SI la valide, le Contrôleur l'examine et l'approuve ou la rejette, le SI exécute le paiement. À chaque étape clé, l'attribut `statut` de la classe `Facture` (cf. [[UML Class]]) change : `Reçue` → `Validée` → `Approuvée` (ou `Rejetée`) → `Payée`. Le diagramme doit faire apparaître ces états comme **Nœuds d'Objet** entre les actions.

Votre mission est d'ajouter des Nœuds d'Objet (rectangles avec étiquettes d'état) entre les actions clés et de vérifier la cohérence avec les valeurs de l'attribut `Facture.statut` du Diagramme de Classes.

## 🔴 Niveau Avancé

*Les exercices avancés exigent une **décomposition multi-niveaux** avec des Actions d'Appel de Comportement (*Call Behavior Actions*), des activités multi-acteurs avec orchestration SI complexe, et une **vérification complète de cohérence inter-diagrammes** avec [[UML Class]] et [[UML Sequence]].*

### Exercice 07 — Décomposition Order-to-Cash avec Sous-Activités

NovaTrade veut spécifier le processus complet **Order-to-Cash** à un niveau d'analyse. Le flux entier est trop détaillé pour un seul diagramme : on adopte une approche à **deux niveaux**. **Niveau 1** : vue d'ensemble avec cinq sous-activités modélisées comme **Actions d'Appel de Comportement** (marqueur râteau ⌬). **Niveau 2** : détail de la sous-activité « Traiter le paiement », qui implique le **Client** (saisie carte au frontend), le **SI** (vérification de crédit interne) et la **Passerelle de Paiement** externe (acteur secondaire de type système, cf. [[UML Use Case Exercices]]).

Votre mission est de produire le diagramme Niveau 1 et l'expansion Niveau 2 de « Traiter le paiement », en respectant le pattern Acteur+SI à chaque niveau.

### Exercice 08 — Préparation de Commande Complète Multi-Acteurs

Détailler la sous-activité « Préparer la commande » du Niveau 1 de l'Exercice 07. Le SI orchestre **trois opérations en parallèle** mais **chaque branche implique une validation humaine via interface** : l'**Opérateur d'Entrepôt** consulte l'ordre de prélèvement, prélève physiquement, confirme à l'écran (ou signale une rupture) ; le **Comptable** consulte la facture générée, vérifie, clique « Envoyer » ; le **Service Logistique** confirme le créneau de transporteur proposé. Le SI gère toute l'orchestration et les changements d'état des objets `Commande` et `Expédition`.

Votre mission est de modéliser cette préparation parallèle avec **trois acteurs humains** + un couloir **SI**, en faisant apparaître les **Nœuds d'Objet** `Commande [Confirmée]` → `Commande [Expédiée]` ainsi que `Expédition [Emballée]` et `Facture [Envoyée]`.

### Exercice 09 — Activité Order-to-Cash Complète + Cohérence Inter-Diagrammes

L'architecte principal de NovaTrade a besoin de la spécification d'activité finale du processus **Order-to-Cash**, avec **cohérence inter-diagrammes complète** vis-à-vis du [[UML Class|Diagramme de Classes]] et du [[UML Sequence|Diagramme de Séquence]] déjà produits. Le **Niveau 1** doit être enrichi avec des **Nœuds d'Objet** entre les phases pour montrer les transitions d'état de la `Commande`. Le **Niveau 2** doit développer **trois sous-activités prioritaires** : `Valider la commande`, `Préparer la commande` (à raffiner depuis l'Ex 08) et `Réconcilier le paiement`. Une **liste de cohérence inter-diagrammes** doit ensuite vérifier, pour chaque action du couloir SI, qu'elle correspond à une opération du Diagramme de Classes et à un message du Diagramme de Séquence.

Votre mission est de produire la spécification complète : Niveau 1 enrichi, trois Niveau 2, et le tableau de cohérence inter-diagrammes accompagné de ses divergences identifiées.
