# Exercices — Diagramme de Classes UML

Cette page contient neuf exercices progressifs, tous situés dans **NovaTrade**, une société de négoce B2B de taille moyenne. Les exercices se concentrent sur le diagramme de classes (*Class Diagram*) UML, partant d'une seule classe bien formée pour aboutir à un modèle de domaine complet avec les six types de relations, une classe abstraite (*Abstract Class*) et une interface (*Interface*) — cohérent avec le vocabulaire établi dans [[UML Use Case Exercices]] et [[UML Activity Exercices]].

Chaque exercice est autonome mais s'appuie sur le précédent. Les débutants devraient compléter les trois exercices de niveau débutant avant de passer au niveau intermédiaire.

## 🟢 Niveau Débutant

*Les exercices débutants se concentrent sur les briques de base : une seule classe avec les compartiments corrects, les types d'attributs, les marqueurs de visibilité et les opérations. Pas encore de relations.*

### Exercice 01 — Modéliser une Classe Unique

L'équipe de développement de NovaTrade démarre la conception du Système de Gestion des Commandes. Avant que des relations ne soient dessinées, chaque classe doit être correctement spécifiée isolément. L'équipe vous demande de modéliser la classe `Produit` (*Product*) — la classe la plus référencée du système. Un Produit possède : un identifiant unique, un nom, une description, un prix unitaire, une quantité en stock et une catégorie. Un Produit doit pouvoir : indiquer s'il est en stock, appliquer une remise sur le prix et se réapprovisionner avec une quantité donnée.

Votre mission est de dessiner la classe `Produit` avec la structure correcte à trois compartiments, des attributs typés, des marqueurs de visibilité et des opérations typées.

### Exercice 02 — Attributs, Visibilité et Valeurs par Défaut

L'équipe NovaTrade doit maintenant modéliser la classe `Client` (*Customer*). Les règles sont : chaque client est par défaut un client Standard (pas Premium), les clients sont actifs par défaut (pas suspendus), et la limite de crédit pour les nouveaux clients est par défaut 0 € (signifiant qu'aucun crédit n'est accordé jusqu'à ce qu'une vérification de crédit soit effectuée). L'équipe Finance veut aussi distinguer les attributs lisibles publiquement par toute partie du système des attributs qui ne peuvent être modifiés qu'en interne (la limite de crédit et le statut de suspension ne devraient pas être modifiables publiquement). L'équipe décide de modéliser cela avec des attributs privés et des accesseurs (*getters*) publics.

Votre mission est de dessiner la classe `Client` avec une visibilité correcte (privée/protégée/publique), des valeurs par défaut explicites et des opérations publiques bien typées.

### Exercice 03 — Modéliser un Fragment à Trois Classes

L'équipe NovaTrade doit modéliser le fragment transactionnel central du domaine : une `Commande` qui contient une ou plusieurs `LigneCommande`, où chaque `LigneCommande` référence un `Produit`. Les règles métier sont : une Commande doit avoir au moins une LigneCommande (elle ne peut pas être vide) ; une LigneCommande ne peut pas exister sans une Commande (supprimer une Commande supprime ses lignes) ; chaque LigneCommande référence exactement un Produit ; un Produit peut être référencé par plusieurs LigneCommande. Vous devez aussi ajouter des relations d'Association entre les trois classes avec la multiplicité correcte aux deux extrémités.

Votre mission est de dessiner ces trois classes connectées par les bons types de relations et les bonnes multiplicités.

## 🟡 Niveau Intermédiaire

*Les exercices intermédiaires introduisent les six types de relations, les décisions Agrégation vs. Composition, et des diagrammes multi-classes nécessitant une multiplicité correcte partout. Chaque scénario présente un choix métier qui détermine quel type de relation est correct.*

### Exercice 04 — Choisir le Bon Type de Relation

L'architecte de NovaTrade vous a donné cinq descriptions de relations et vous a demandé de dessiner la relation UML correcte pour chacune. C'est un exercice ciblé sur le choix de relation — la source la plus courante d'erreurs de conception dans les diagrammes de classes. Pour chaque description, décider d'utiliser Association, Agrégation, Composition, Dépendance, Généralisation ou Réalisation, et justifier votre choix en une phrase.

Les cinq relations :

1. Un `Entrepot` (*Warehouse*) détient de nombreux articles `Produit` dans son inventaire. Si l'Entrepôt est désaffecté, les Produits sont déplacés vers un autre Entrepôt — ils ne sont pas détruits.
2. Une `Facture` est composée d'une ou plusieurs `LigneFacture`. Si la Facture est supprimée, toutes ses lignes sont supprimées.
3. La classe `GenerateurRapport` utilise un objet `IntervalleDate` (*DateRange*) comme paramètre de la méthode `genererRapport()`, mais elle ne stocke pas d'attribut `IntervalleDate`.
4. Un `ClientPremium` est un type de `Client` et hérite de tous les attributs et opérations de Client.
5. À la fois `PaiementStripe` et `VirementBancaire` s'engagent à implémenter les opérations déclarées par l'interface `ProcesseurPaiement`.

Votre mission est de dessiner les cinq relations avec le bon type, la bonne direction et les bonnes multiplicités, et de justifier chaque choix en une phrase.

### Exercice 05 — Le Fragment du Domaine Facture et Paiement

L'équipe Finance de NovaTrade a besoin du fragment Facture–Paiement du modèle de domaine. Les règles sont : une Commande génère zéro ou une Facture ; une Facture est réglée par exactement un Paiement ; un Paiement est abstrait — c'est toujours soit un `PaiementStripe`, soit un `VirementBancaire` ; les deux types de paiement implémentent l'interface `ProcesseurPaiement`. De plus, une Facture est composée d'une ou plusieurs `LigneFacture` (chaque ligne correspond à une LigneCommande). La `LigneFacture` référence la `LigneCommande` à partir de laquelle elle a été générée — c'est une association dirigée (et non une composition, puisque supprimer une LigneFacture ne supprime pas la LigneCommande).

Votre mission est de dessiner ce fragment avec les six classes, l'interface, la classe abstraite et toutes les relations correctement typées.

### Exercice 06 — Hiérarchie de Généralisation et Classes Abstraites

NovaTrade gère trois types de notifications : `NotificationEmail`, `NotificationSMS` et `NotificationPush`. Toutes trois partagent un comportement commun : chaque notification a un destinataire, un sujet, un corps, un horodatage d'envoi et un statut de livraison. Toutes trois peuvent être envoyées, annulées et avoir leur statut consulté. Cependant, chaque type a ses attributs spécifiques et un mécanisme de livraison spécifique : Email a une liste de destinataires en copie ; SMS a un numéro de téléphone et une limite de caractères ; Push a un jeton d'appareil et un niveau de priorité. L'architecte décide de modéliser cela comme une hiérarchie de Généralisation avec une classe de base `Notification` abstraite. De plus, les trois types concrets doivent implémenter une interface `CanalNotification` (*NotificationChannel*) qui déclare une seule opération `livrer(): Boolean`.

Votre mission est de modéliser cette hiérarchie de Généralisation avec une classe abstraite, trois sous-classes concrètes et une interface de réalisation.

## 🔴 Niveau Avancé

*Les exercices avancés exigent le modèle de domaine NovaTrade complet, le diagnostic et la correction d'un diagramme défectueux, et la production d'un Diagramme de Classes entièrement spécifié servant de référence vocabulaire pour les diagrammes de Séquence et de Machine à États.*

### Exercice 07 — Construire le Modèle de Domaine NovaTrade Complet

Le comité de pilotage projet de NovaTrade a approuvé les exigences du Système de Gestion des Commandes. Avant le démarrage du développement, l'architecte doit produire le diagramme de classes complet au niveau du domaine couvrant tout le périmètre Order-to-Cash. Le diagramme doit inclure : la hiérarchie Client (Standard et Premium), la chaîne Commande–LigneCommande–Produit, la hiérarchie Facture–LigneFacture–Paiement, l'interface ProcesseurPaiement et la hiérarchie Notification. Toutes les classes des Exercices 01 à 06 sont dans le périmètre. Le diagramme doit être divisé en deux vues ciblées : une vue **Client & Commande** et une vue **Facture & Paiement**, avec une note indiquant comment les deux vues se connectent à la frontière `Commande` → `Facture`.

Votre mission est de produire ces deux vues du modèle de domaine, ainsi qu'une note expliquant les principales décisions de modélisation.

### Exercice 08 — Diagnostiquer et Corriger un Diagramme de Classes Défectueux

Un analyste junior a produit un premier brouillon du fragment de domaine Gestion des Fournisseurs de NovaTrade. Vous avez reçu la description suivante de son diagramme :

> Le diagramme a quatre classes : `Fournisseur`, `contactFournisseur`, `BonCommande` et `traiteurCommande`. `Fournisseur` a les attributs `nom`, `emailContact` et `commandes`. `contactFournisseur` a les attributs `nom` et `telephone`. `BonCommande` a les attributs `id`, `Date`, `articles` et `approuve`. `traiteurCommande` a les opérations `traiterCommande()`, `valider()` et `envoyerAuFournisseur()`. Il y a une Composition de `Fournisseur` vers `contactFournisseur` (losange creux côté Fournisseur). Il y a une Généralisation de `BonCommande` vers `Fournisseur`. `traiteurCommande` est connecté à `BonCommande` par une Dépendance dessinée en flèche pleine. Aucune relation ne porte de multiplicité.

Votre mission est d'identifier chaque erreur de modélisation, produire un diagramme corrigé et rédiger une analyse des écarts numérotée.

### Exercice 09 — Spécification de Classes Complète comme Référence Vocabulaire

L'architecte principal de NovaTrade doit produire le diagramme de classes final et faisant autorité pour le Système de Gestion des Commandes — le document que tous les autres diagrammes (Séquence, Machine à États, schéma de Base de données) référenceront. Les exigences sont :
- Toutes les classes du modèle de domaine NovaTrade sont dans le périmètre
- Chaque classe doit avoir des attributs complets avec types et visibilité, et des opérations complètes avec signatures
- La classe `Commande` doit avoir un attribut explicite `statut` typé `StatutCommande` (une énumération avec les valeurs : `BROUILLON`, `CONFIRMEE`, `EXPEDIEE`, `LIVREE`, `ANNULEE`, `CLOTUREE`)
- La classe `Facture` doit avoir un attribut explicite `statut` typé `StatutFacture` (énumération : `BROUILLON`, `ENVOYEE`, `PAYEE`, `EN_RETARD`, `ANNULEE`)
- Le diagramme doit inclure une note de référence croisée mappant chaque classe vers le diagramme de Machine à États qui spécifie son cycle de vie (pour `Commande` et `Facture`)
- L'architecte doit aussi produire un **registre des classes** — un tableau Markdown listant chaque classe, son type (concrète/abstraite/interface), ses attributs clés et les autres diagrammes qui la référencent

Votre mission est de produire le diagramme de classes complet, les énumérations, le registre des classes et la liste des divergences avec les autres diagrammes.
