# Exercices — Diagramme de Cas d'Utilisation UML

Cette page contient neuf exercices progressifs, tous situés dans la même entreprise fictive — **NovaTrade**, une société de négoce B2B de taille moyenne qui achète des marchandises auprès de fournisseurs et les revend à des clients professionnels. Les exercices se concentrent sur le diagramme de cas d'utilisation (*Use Case Diagram*) et la description textuelle de cas d'utilisation (*Textual Use Case Description*), partant d'un acteur unique et d'une poignée de cas d'utilisation pour aboutir à un système multi-acteurs avec des relations complexes et une spécification textuelle complète.

Chaque exercice est autonome mais s'appuie sur les concepts introduits dans le précédent. Les débutants devraient compléter les trois exercices de niveau débutant avant de passer au niveau intermédiaire.

## 🟢 Niveau Débutant

*Les exercices débutants se concentrent sur les briques de base : une seule frontière de système (*System Boundary*), un petit ensemble de cas d'utilisation, un ou deux acteurs (*actors*), et la relation d'Association (*Association*). Pas encore d'Inclusion (*Include*), d'Extension (*Extend*) ni de Généralisation (*Generalization*).*

### Exercice 01 — Portail Libre-Service Client

NovaTrade lance un portail web en libre-service pour ses clients professionnels. Au lancement, le portail doit permettre aux clients de faire trois choses : parcourir le catalogue de produits, passer une nouvelle commande et consulter leur historique de commandes. Un Client doit être connecté avant de pouvoir effectuer la moindre action — l'étape de connexion fait partie de chaque interaction. L'équipe IT a besoin d'un diagramme de cas d'utilisation qui montre ce que le système offre avant de commencer à écrire du code.

Votre mission est de dessiner le diagramme de cas d'utilisation pour le Portail Client NovaTrade.

### Exercice 02 — Ajouter un Second Acteur

L'équipe du portail NovaTrade a appris que celui-ci sera également utilisé par des représentants de **Fournisseurs** — pas seulement par des Clients. Les Fournisseurs doivent soumettre des factures électroniquement via le portail et vérifier le statut de paiement de leurs factures soumises. Un `Fournisseur` ne peut pas parcourir le catalogue de produits ni passer de commandes — ces fonctionnalités sont réservées aux Clients. Les deux acteurs partagent le cas d'utilisation `Se connecter`. L'équipe décide aussi d'ajouter un acteur `Administrateur` qui peut gérer les comptes utilisateurs mais n'interagit pas directement avec les commandes ou les factures.

Votre mission est d'étendre le diagramme de l'Exercice 01 pour couvrir ces deux nouveaux acteurs.

### Exercice 03 — Rédiger une Description Textuelle de Cas d'Utilisation

L'équipe de développement de NovaTrade convient que le diagramme de l'Exercice 02 est une carte utile, mais elle a besoin de plus de détails avant de pouvoir construire quoi que ce soit. Le *product owner* vous demande d'écrire une **description textuelle de cas d'utilisation** (*Textual Use Case Description*) complète pour le cas d'utilisation `Passer une commande`. Le processus de commande chez NovaTrade fonctionne comme suit : un Client connecté sélectionne des produits et des quantités dans un panier, confirme son adresse de livraison et soumet la commande. Le système vérifie que tous les articles sont en stock et que la limite de crédit du client n'est pas dépassée. Si les vérifications passent, le système crée la commande, réserve le stock et envoie un courriel de confirmation. Si un article est en rupture de stock, le système affiche un message de disponibilité au client et s'arrête. Si la limite de crédit est dépassée, le système affiche un avertissement de crédit et s'arrête.

Votre mission est de rédiger la description textuelle complète du cas d'utilisation `Passer une commande`, couvrant le flux principal, les flux alternatifs et les règles métier.

## 🟡 Niveau Intermédiaire

*Les exercices intermédiaires introduisent les relations d'Inclusion (*Include*), d'Extension (*Extend*) et de Généralisation (*Generalization*), plusieurs frontières de système, et le défi de cadrer correctement des cas d'utilisation partagés entre acteurs.*

### Exercice 04 — Refactorisation avec Include

Après une revue de conception, l'équipe NovaTrade s'aperçoit que la logique d'authentification est dupliquée à travers le diagramme : `Client`, `Fournisseur` et `Administrateur` s'associent chacun à `Se connecter`, et les descriptions textuelles de chaque cas d'utilisation commencent par « l'acteur doit être authentifié ». Un architecte senior propose d'extraire le comportement de connexion dans un cas d'utilisation partagé qui est *inclus* par tous les autres, plutôt que montré comme une association directe avec l'acteur. De même, chaque cas d'utilisation qui modifie des données — `Passer une commande`, `Soumettre une facture`, `Gérer les comptes utilisateurs` — doit écrire une entrée dans le **Journal d'Audit** (*Audit Log*) du système, qui est un cas d'utilisation technique séparé. L'architecte veut que cela soit modélisé explicitement avec des relations d'Inclusion.

Votre mission est de refactoriser le diagramme de l'Exercice 02 pour utiliser des relations d'Inclusion.

### Exercice 05 — Modéliser un Comportement Optionnel avec Extend

L'équipe produit de NovaTrade a introduit deux fonctionnalités optionnelles dans le Portail Client. D'abord, lorsqu'un Client passe une commande, il peut optionnellement choisir d'appliquer un **code promotionnel** (*promotional voucher code*) — ceci ne fait pas partie du flux de commande standard, ce n'est déclenché que si le client a un code à saisir. Ensuite, si le total de la commande d'un Client dépasse 10 000 €, le système *peut* suggérer une **remise sur quantité** (*bulk discount*) — cette extension est déclenchée par le système en fonction de la valeur du panier, pas par le choix du client. Les deux sont des comportements optionnels qui étendent le cas d'utilisation `Passer une commande`, et non des étapes obligatoires.

Votre mission est de modéliser ces deux extensions en utilisant la relation `«extend»`.

### Exercice 06 — Généralisation d'Acteurs et Frontières de Système

L'équipe d'architecture de NovaTrade a passé en revue le portail et identifié deux problèmes. Premièrement, il existe maintenant deux types de Clients : un **Client Standard** (limite de crédit, pas d'éligibilité à la remise sur quantité) et un **Client Premium** (limite de crédit plus élevée, éligible aux remises sur quantité et à une fonctionnalité dédiée de gestionnaire de compte). Les deux types partagent tous les cas d'utilisation du rôle `Client` de base mais les Clients Premium ont en plus accès à `Demander une consultation au gestionnaire de compte`. Cela doit être modélisé avec une Généralisation d'Acteurs (*Actor Generalization*). Deuxièmement, l'équipe décide de scinder le portail en deux frontières de système distinctes : **« Portail Client & Fournisseur »** (orienté public) et **« Back-Office Administrateur »** (interne), puisqu'ils seront déployés en tant qu'applications séparées.

Votre mission est de restructurer le diagramme pour refléter ces deux changements.

## 🔴 Niveau Avancé

*Les exercices avancés exigent une spécification système complète sur plusieurs acteurs, un ensemble complet de relations de cas d'utilisation, et une description textuelle pouvant servir d'entrée à un Diagramme de Classes (*Class Diagram*) et un Diagramme de Séquence (*Sequence Diagram*). Prévoir du temps pour le cadrage avant de dessiner.*

### Exercice 07 — Cadrage du Système Complet de Gestion des Commandes

NovaTrade démarre une refonte complète de son Système de Gestion des Commandes (*Order Management System* — OMS). Avant tout travail de conception, l'équipe projet a besoin d'un diagramme de cas d'utilisation qui couvre l'ensemble du périmètre du système. Les acteurs suivants interagissent avec l'OMS : **Client** (passe des commandes, suit les livraisons, gère son compte), **Représentant Commercial** (*Sales Representative*) (crée et modifie des commandes au nom des clients, applique des remises manuelles), **Opérateur d'Entrepôt** (*Warehouse Operator*) (réceptionne les marchandises, met à jour les niveaux de stock, prépare les expéditions), **Contrôleur Financier** (*Finance Controller*) (valide les factures, approuve les paiements, génère des rapports financiers), et **Système ERP** (un système externe qui envoie des mises à jour de stock et reçoit les confirmations de paiement — un acteur secondaire). Il y a aussi un **Administrateur Système** qui gère les comptes utilisateurs et configure les seuils d'approbation.

Votre mission est de produire le diagramme de cas d'utilisation complet et une description textuelle complète pour le cas d'utilisation prioritaire `Créer une commande`.

### Exercice 08 — Identifier les Exigences Manquantes

L'analyste métier de NovaTrade a produit un premier brouillon du diagramme de cas d'utilisation de l'OMS, mais il présente plusieurs problèmes. Vous avez reçu la description suivante :

> L'OMS a trois acteurs : `Client`, `Manager` et `Système`. Le Client peut `Acheter un produit`, `Se connecter`, `Payer` et `Obtenir un reçu`. Le Manager peut `Consulter les rapports`. Le Système peut `Envoyer un courriel`. Les trois acteurs sont connectés à `Se connecter`. Il y a un `«include»` de `Se connecter` vers `Acheter un produit`. Le `Système` est à l'intérieur de la frontière du système.

Votre mission est de critiquer ce brouillon, d'identifier chaque erreur de modélisation et chaque élément manquant, de produire un diagramme corrigé et de rédiger un document d'analyse des écarts (*gap analysis*).

### Exercice 09 — Spécification Complète de Cas d'Utilisation pour le Périmètre Order-to-Cash

Le comité de pilotage projet de NovaTrade a approuvé le Système de Gestion des Commandes. Avant le démarrage du développement, l'analyste principal doit produire une **spécification complète de cas d'utilisation** qui servira de référence d'exigences pour le Diagramme de Classes, les Diagrammes de Séquence et la Machine à États (*State Machine*) à venir. Le périmètre Order-to-Cash complet implique tous les acteurs de l'Exercice 07. Le comité veut un modèle à deux niveaux : un diagramme de haut niveau montrant les principaux domaines de cas d'utilisation (cinq à sept cas d'utilisation, tous repliés en objectifs de haut niveau), et un diagramme détaillé étendant le domaine **Cycle de Vie des Commandes** en ses cas d'utilisation individuels. De plus, un minimum de **trois descriptions textuelles complètes** doit être rédigé, couvrant les trois cas d'utilisation prioritaires, chacun avec toutes les sections, au moins deux flux alternatifs, des exigences non fonctionnelles et des règles métier.

Votre mission est de produire cette spécification complète de cas d'utilisation.
