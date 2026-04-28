# UML Use Cases - Solutions

## 🟢 Niveau Débutant

### Exercice 01 — Portail Libre-Service Client

```mermaid
flowchart LR
    Client(["👤 Client"])
    subgraph Portail ["Portail Client NovaTrade"]
        UC1(["Se connecter"])
        UC2(["Parcourir le catalogue de produits"])
        UC3(["Passer une commande"])
        UC4(["Consulter l'historique des commandes"])
    end
    Client --- UC2
    UC2 -. "«include»" .-> UC1
    Client --- UC3
    UC3 -. "«include»" .-> UC1
    Client --- UC4
    UC4 -. "«include»" .-> UC1
```

### Exercice 02 — Ajouter un Second Acteur

```mermaid
flowchart LR
    Client(["👤 Client"])
    Fournisseur(["👤 Fournisseur"])
    Admin(["👤 Administrateur"])
    subgraph Portail ["Portail Client NovaTrade"]
        UC1(["Parcourir le catalogue de produits"])
        UC2(["Passer une commande"])
        UC3(["Consulter l'historique des commandes"])
        UC4(["Soumettre une facture"])
        UC5(["Vérifier le statut de paiement"])
        UC6(["Gérer les comptes utilisateurs"])
    end
    Client --- UC1
    Client --- UC2
    Client --- UC3
    Fournisseur --- UC4
    Fournisseur --- UC5
    Admin --- UC6
```

> **Pre-conditions**: Les utilisateurs doivent être connectés.

### Exercice 03 — Rédiger une Description Textuelle de Cas d'Utilisation

| Champ | Contenu |
|---|---|
| **Identifiant** | UC-03 |
| **Nom** | Passer une commande |
| **Acteur Principal** | Client |
| **Préconditions** | Le Client est authentifié. Au moins un produit existe dans le panier du client. Le Client possède une adresse de livraison enregistrée. |
| **Postcondition — Succès** | La commande est créée avec le statut `Confirmée`. Le stock est réservé. Un courriel de confirmation est envoyé au client. |
| **Postcondition — Échec** | Aucune commande n'est créée. Le contenu du panier est préservé pour la prochaine session du client. |

**Flux Principal**
1. Le Client examine le contenu et les quantités du panier.
2. Le Client sélectionne ou confirme l'adresse de livraison.
3. Le Client clique sur « Confirmer la commande ».
4. Le système vérifie que tous les articles du panier disposent d'un stock suffisant.
5. Le système vérifie que le total de la commande ne dépasse pas la limite de crédit du client.
6. Le système crée la commande avec le statut `Confirmée`.
7. Le système réserve le stock pour chaque ligne de commande.
8. Le système envoie un courriel de confirmation au client.
9. Le système affiche la page de confirmation de commande avec le numéro de référence.

**Flux Alternatif A — Rupture de stock (déviation à l'étape 4)**
4a. Un ou plusieurs articles ne disposent pas d'un stock suffisant.
4b. Le système affiche un message de disponibilité listant les articles concernés et leur stock actuel.
4c. Le cas d'utilisation se termine. Aucune commande n'est créée.

**Flux Alternatif B — Limite de crédit dépassée (déviation à l'étape 5)**
5a. Le total de la commande dépasse la limite de crédit approuvée du client.
5b. Le système affiche un avertissement de crédit montrant la limite et le total de la commande.
5c. Le cas d'utilisation se termine. Aucune commande n'est créée.

**Règles Métier**
- RM-01 : La quantité minimale de commande par ligne est de 1 unité.
- RM-02 : Le seuil de vérification de crédit est la limite de crédit approuvée du client stockée dans l'ERP ; aucune dérogation manuelle n'est permise au moment du paiement.


## 🟡 Niveau Intermédiaire
### Exercice 04 — Refactorisation avec Include

```mermaid
flowchart LR
    Client(["👤 Client"])
    Fournisseur(["👤 Fournisseur"])
    Admin(["👤 Administrateur"])
    subgraph Portail ["Portail Client NovaTrade"]
        LogIn(["Se connecter"])
        Audit(["Écrire dans le journal d'audit"])
        UC2(["Parcourir le catalogue de produits"])
        UC3(["Passer une commande"])
        UC4(["Consulter l'historique des commandes"])
        UC5(["Soumettre une facture"])
        UC6(["Vérifier le statut de paiement"])
        UC7(["Gérer les comptes utilisateurs"])
        UC2 -. "«include»" .-> LogIn
        UC3 -. "«include»" .-> LogIn
        UC4 -. "«include»" .-> LogIn
        UC5 -. "«include»" .-> LogIn
        UC6 -. "«include»" .-> LogIn
        UC7 -. "«include»" .-> LogIn
        UC3 -. "«include»" .-> Audit
        UC5 -. "«include»" .-> Audit
        UC7 -. "«include»" .-> Audit
    end
    Client --- UC2
    Client --- UC3
    Client --- UC4
    Fournisseur --- UC5
    Fournisseur --- UC6
    Admin --- UC7
```

### Exercice 05 — Modéliser un Comportement Optionnel avec Extend

```mermaid
flowchart LR
    Client(["👤 Client"])
    subgraph Portail ["Portail Client NovaTrade"]
        LogIn(["Se connecter"])
        UC3(["Passer une commande<br/>——————<br/>points d'extension :<br/>• le client saisit un code<br/>• total panier ≥ 10 000 €"])
        UC_V(["Appliquer un code promotionnel"])
        UC_B(["Suggérer une remise sur quantité"])
        UC3 -. "«include»" .-> LogIn
        UC_V -. "«extend»<br/>[le client saisit un code]" .-> UC3
        UC_B -. "«extend»<br/>[total panier ≥ 10 000 €]" .-> UC3
    end
    Client ---- UC3
```

### Exercice 06 — Généralisation d'Acteurs et Frontières de Système

```mermaid
flowchart LR
    StdC(["👤 Client<br/>Standard"])
    PremC(["👤 Client<br/>Premium"])
    Fournisseur(["👤 Fournisseur"])
    Admin(["👤 Administrateur"])
    PremC -- "▷ généralise" --> StdC
    subgraph Portail ["Portail Client & Fournisseur"]
        UC2(["Parcourir le catalogue de produits"])
        UC3(["Passer une commande"])
        UC4(["Consulter l'historique des commandes"])
        UC5(["Soumettre une facture"])
        UC6(["Vérifier le statut de paiement"])
        UC_P(["Demander une consultation<br/>au gestionnaire de compte"])
    end
    subgraph BO ["Back-Office Administrateur"]
        UC7(["Gérer les comptes utilisateurs"])
        UC8(["Générer un rapport d'utilisation"])
    end
    StdC --- UC2
    StdC --- UC3
    StdC --- UC4
    PremC --- UC_P
    Fournisseur --- UC5
    Fournisseur --- UC6
    Admin --- UC7
    Admin --- UC8
```

## 🔴 Niveau Avancé
### Exercice 07 — Cadrage du Système Complet de Gestion des Commandes

**Énoncé de Périmètre**
L'OMS NovaTrade permet aux Clients de passer et suivre leurs commandes, aux Représentants Commerciaux de gérer les commandes au nom des clients, aux Opérateurs d'Entrepôt de gérer le stock et les expéditions, aux Contrôleurs Financiers de gérer la facturation et les paiements, et au Système ERP de synchroniser les données de stock et de paiement. L'Administrateur Système gère les comptes utilisateurs et la configuration système. Hors périmètre : la gestion de l'adresse de facturation client (gérée par un CRM séparé) et l'intégration avec les transporteurs (gérée par une plateforme logistique séparée).

```mermaid
flowchart LR
    Client(["👤 Client"])
    RepCom(["👤 Représentant<br/>Commercial"])
    OpEntrepot(["👤 Opérateur<br/>d'Entrepôt"])
    CtrlFin(["👤 Contrôleur<br/>Financier"])
    AdminSys(["👤 Administrateur<br/>Système"])
    ERPSys(["⚙️ Système ERP"])
    subgraph OMS ["Système de Gestion des Commandes NovaTrade"]
        UC1(["Passer une commande"])
        UC2(["Créer une commande<br/>au nom du Client"])
        UC3(["Suivre la livraison"])
        UC4(["Mettre à jour le niveau de stock"])
        UC5(["Préparer l'expédition"])
        UC6(["Valider la facture"])
        UC7(["Approuver le paiement"])
        UC8(["Générer un rapport financier"])
        UC9(["Gérer les comptes utilisateurs"])
        UC10(["S'authentifier"])
        UC11(["Appliquer une remise manuelle"])
        UC1 -. "«include»" .-> UC10
        UC2 -. "«include»" .-> UC10
        UC11 -. "«extend»<br/>[le rep. commercial a l'autorité]" .-> UC2
    end
    Client --- UC1
    Client --- UC3
    RepCom --- UC2
    RepCom --- UC3
    OpEntrepot --- UC4
    OpEntrepot --- UC5
    CtrlFin --- UC6
    CtrlFin --- UC7
    CtrlFin --- UC8
    AdminSys --- UC9
    ERPSys --- UC4
    ERPSys --- UC7
```

### Exercice 08 — Identifier les Exigences Manquantes

**Analyse des Écarts**

1. `Système` est placé *à l'intérieur* de la frontière du système — les acteurs sont toujours externes. La frontière représente le système en cours de construction ; l'acteur `Système` est un participant automatisé externe, pas le système lui-même.
2. La flèche `«include»` va de `Se connecter` vers `Acheter un produit` — la direction est inversée. `Acheter un produit` inclut `Se connecter` (le cas de base appelle le cas inclus), pas l'inverse.
3. `Acheter un produit` et `Payer` sont deux cas d'utilisation séparés, mais le paiement est probablement une étape obligatoire dans l'achat — ils devraient n'en former qu'un seul ou être modélisés avec `«include»`.
4. `Obtenir un reçu` est un événement (le système envoie un reçu), pas un objectif que l'acteur initie. Cela peut appartenir à une postcondition ou à un cas d'utilisation `Envoyer un reçu` associé à l'acteur `Système`.
5. `Consulter les rapports` est trop vague — quels rapports ? Rapports financiers, de livraison, d'activité utilisateur ? Chaque type sert un objectif différent et peut requérir des données et des droits d'accès différents.
6. Acteurs manquants du contexte NovaTrade : `Fournisseur`, `Opérateur d'Entrepôt`, `Contrôleur Financier` — le brouillon ne couvre que le côté client, ignorant les opérations back-office que l'OMS doit prendre en charge.
7. Cas d'utilisation manquants : `Suivre la livraison`, `Soumettre une facture`, `Approuver le paiement`, `Mettre à jour le stock` — le brouillon ne décrit que l'achat, pas le cycle de vie complet de la commande.
8. `Envoyer un courriel` est un détail d'implémentation, pas un cas d'utilisation métier — l'envoi d'un courriel par le système est un mécanisme technique, pas un objectif que poursuit un acteur. Cela appartient à une note ou une annotation, pas à un cas d'utilisation.

### Exercice 09 — Spécification Complète de Cas d'Utilisation pour le Périmètre Order-to-Cash

**Niveau 1 — Diagramme de haut niveau**

```mermaid
flowchart LR
    Client(["👤 Client"])
    RepCom(["👤 Représentant<br/>Commercial"])
    OpEntrepot(["👤 Opérateur<br/>d'Entrepôt"])
    CtrlFin(["👤 Contrôleur<br/>Financier"])
    AdminSys(["👤 Admin Système"])
    ERPSys(["⚙️ Système ERP"])
    subgraph OMS ["OMS NovaTrade — Niveau 1"]
        A(["Gérer les commandes"])
        B(["Gérer l'inventaire"])
        C(["Traiter les paiements"])
        D(["Gérer les utilisateurs"])
        E(["Générer des rapports"])
    end
    Client --- A
    RepCom --- A
    OpEntrepot --- B
    CtrlFin --- C
    CtrlFin --- E
    AdminSys --- D
    ERPSys --- B
    ERPSys --- C
```

**Niveau 2 — Expansion de Gérer les Commandes**

```mermaid
flowchart LR
    Client(["👤 Client"])
    RepCom(["👤 Représentant<br/>Commercial"])
    StdC(["👤 Client<br/>Standard"])
    PremC(["👤 Client<br/>Premium"])
    StdC -- "▷" --> Client
    PremC -- "▷" --> Client
    subgraph Orders ["Gérer les commandes — Détail"]
        Auth(["S'authentifier"])
        UC1(["Passer une commande"])
        UC2(["Modifier une commande"])
        UC3(["Annuler une commande"])
        UC4(["Suivre une commande"])
        UC5(["Clôturer une commande"])
        UC6(["Appliquer une livraison prioritaire"])
        UC1 -. "«include»" .-> Auth
        UC2 -. "«include»" .-> Auth
        UC3 -. "«include»" .-> Auth
        UC6 -. "«extend»<br/>[client.estPremium]" .-> UC1
    end
    Client --- UC1
    Client --- UC3
    Client --- UC4
    RepCom --- UC1
    RepCom --- UC2
    RepCom --- UC3
    PremC --- UC6
    RepCom --- UC5
```

**Notes de Mappage (exemple pour Passer une commande)**
`Passer une commande` introduit les classes `Commande` et `LigneCommande`, les classes `Client` et `Produit`, ainsi que les opérations `confirmer()` et `annuler()` — toutes doivent apparaître dans le Diagramme de Classes. Le flux principal se mappe directement au Diagramme de Séquence `passerCommande`, où les étapes deviennent des messages entre `AppWeb`, `ServiceCommande`, `Inventaire` et `ServicePaiement`.


