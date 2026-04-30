# Solution — Diagramme BPMN du Processus Order-to-Cash NovaTrade

**Livrable du Study Case :** 01 (premier livrable de la feuille de route)
**Énoncé :** voir le document maître `Study Case.md`
**Périmètre couvert :** processus métier de bout en bout, du passage de commande par le Client jusqu'au rapprochement comptable du paiement

Ce document est la solution attendue pour le diagramme BPMN du Study Case NovaTrade. Il présente le mémo de périmètre, les trois niveaux d'abstraction du modèle (contexte, vue d'ensemble, détails par sous-processus), la justification des choix de modélisation, et la transition vers les livrables UML qui suivront.

---

## 1. Mémo de Périmètre

Avant de tracer le moindre élément, on fixe les frontières du modèle. Le mémo ci-dessous résume ce qui sera modélisé et ce qui ne le sera pas — il s'aligne sur la Section 7 (Limite de Périmètre) du document `Study Case.md`.

> **📝 Mémo de périmètre — Processus Order-to-Cash NovaTrade**
>
> - **Déclencheur (*trigger*) :** le Client transmet une commande à NovaTrade.
> - **Événements de fin :** (a) Commande clôturée après paiement rapproché (chemin nominal) ; (b) Commande rejetée pour Client suspendu ; (c) Commande mise en attente pour stock insuffisant ; (d) Escalade pour recouvrement formel après 3 relances sans paiement.
> - **Participants externes :** Client (organisation cliente), Transporteur (prestataire logistique).
> - **Participants internes (couloirs NovaTrade) :** Vente (Représentant commercial), Exécution (Opérateur d'entrepôt), Finance (Contrôleur financier).
> - **Hors périmètre :** gestion fournisseurs, catalogue produits, enregistrement client / KYC, retours et remboursements, calcul de TVA (cf. Section 7 du document `Study Case.md`).

Ce mémo doit tenir sur un post-it. Si l'on n'arrive pas à le résumer aussi succinctement, le périmètre n'est pas suffisamment compris pour qu'on puisse modéliser quoi que ce soit.

---

## 2. Niveau 0 — Diagramme de Contexte

Le diagramme de contexte (*context diagram*) montre l'OMS NovaTrade comme une boîte unique, avec ses participants externes et les messages échangés. C'est la vue la plus haute, validable par le Directeur des Opérations en moins d'une minute.

![[Study Case - BPMN - Niveau 0.png]]

> **📎 Diagramme :** `Study Case - BPMN - Niveau 0.bpmn`
>
> Fichier BPMN 2.0 à ouvrir dans Camunda Modeler, bpmn.io, Bizagi ou tout outil compatible. Le diagramme contient trois piscines (Client en boîte noire, OMS NovaTrade, Transporteur en boîte noire) et huit flux de messages.

> **Lecture du diagramme :** trois participants seulement, huit messages au total. Tous les messages traversent la frontière de l'OMS — il n'y a aucun flux interne visible à ce niveau, ce qui est précisément l'objectif. Le contexte est compact, validable par toute partie prenante non technique.

---

## 3. Niveau 1 — Vue d'Ensemble du Processus O2C

Au Niveau 1, on déplie l'OMS en sa **piscine NovaTrade unique** avec ses trois couloirs métier. Trois sous-processus repliés (*collapsed sub-processes*) couvrent l'enchaînement complet, un par couloir. Les participants externes restent en piscines fermées.

![[Study Case - BPMN - Niveau 1.png]]

> **📎 Diagramme :** `Study Case - BPMN - Niveau 1.bpmn`
>
> Fichier BPMN 2.0. Contient : trois piscines (Client en boîte noire, NovaTrade avec les trois couloirs Vente / Exécution / Finance, Transporteur en boîte noire) ; trois sous-processus repliés (un par couloir) ; deux passerelles exclusives pour les bifurcations ; cinq événements de fin nommés ; huit flux de messages externes.

> **Lecture du diagramme :** le chemin nominal traverse les trois couloirs de gauche à droite (Vente → Exécution → Finance) et s'achève sur « Commande clôturée ». Trois branches d'exception émergent à des points distincts et se terminent chacune par un événement de fin nommé : rejet pour Client suspendu, mise en attente pour stock insuffisant, escalade pour défaut de paiement. La règle « une organisation = une piscine » est respectée — NovaTrade est une seule piscine, ses départements sont des couloirs ; Client et Transporteur, organisations externes, ont chacune leur piscine en boîte noire.

---

## 4. Niveau 2 — Détail des Sous-Processus

Chaque sous-processus replié du Niveau 1 est ici développé en éléments individuels. On respecte la règle d'un seul niveau d'abstraction par diagramme — chaque diagramme ci-dessous correspond à **un seul** sous-processus déplié.

### 4.1 Recevoir et confirmer la commande (couloir Vente)

Ce sous-processus traite UC-01 (Passer une Commande) côté métier. Les contrôles de validation (statut Client, stock) s'exécutent en parallèle pour optimiser le temps de traitement avant la décision de confirmation.

![[Study Case - BPMN - Niveau 2.1 Vente.png]]

> **📎 Diagramme :** `Study Case - BPMN - Niveau 2.1 Vente.bpmn`
>
> Fichier BPMN 2.0. Contient : Événement de Début Message ; Passerelle Parallèle de division ; Tâche de Règle Métier (vérification statut Client) et Tâche de Service (vérification stock) en parallèle ; Fusion Parallèle ; Passerelle Exclusive avec trois branches (confirmation, rejet, mise en attente) ; trois événements de fin distincts ; deux Tâches d'Envoi ; trois flux de messages avec le Client.

> **Lecture du diagramme :** les deux vérifications (statut Client, stock) s'exécutent en parallèle via une Passerelle Parallèle (*Parallel Gateway*) en division. Une Fusion Parallèle attend les deux résultats avant la décision exclusive. Trois issues nommées : confirmation, rejet (Client suspendu), mise en attente (stock insuffisant). Chaque chemin se termine par son propre événement de fin — aucune flèche dans le vide.

### 4.2 Préparer et expédier la commande (couloir Exécution)

Ce sous-processus traite UC-02 (Exécuter la Commande). Le **prélèvement physique** (*pick & pack*) et la **génération de l'étiquette de transport** peuvent se faire en parallèle pour gagner du temps. La fin du sous-processus est marquée par la prise en charge effective par le Transporteur.

![[Study Case - BPMN - Niveau 2.2 Execution.png]]

> **📎 Diagramme :** `Study Case - BPMN - Niveau 2.2 Execution.bpmn`
>
> Fichier BPMN 2.0. Contient : trois piscines (Client, NovaTrade-Exécution, Transporteur) ; Tâche de Service initiale (génération liste de prélèvement) ; Passerelle Parallèle activant en parallèle une Tâche Utilisateur (prélèvement physique) et une Tâche de Service (étiquette de transport) ; Fusion Parallèle ; Tâche d'Envoi vers le Transporteur ; Événement Intermédiaire de Réception Message attendant la collecte ; Tâche de Service d'enregistrement ; Tâche d'Envoi vers le Client ; trois flux de messages externes.

> **Lecture du diagramme :** la Passerelle Parallèle après « Générer la liste de prélèvement » lance simultanément l'opération physique (Tâche Utilisateur exécutée par l'Opérateur d'entrepôt) et la préparation logistique (Tâche de Service générant l'étiquette). La Fusion Parallèle synchronise les deux avant la notification au Transporteur. L'**Événement Intermédiaire de Réception Message** (*Message Catch Event*) « Collecte par le Transporteur » modélise l'attente du message du Transporteur — c'est un point d'attente passif, pas une tâche.

### 4.3 Facturer et encaisser (couloir Finance)

Ce sous-processus traite UC-03 (Traiter la Facture et le Paiement). Il introduit deux mécanismes BPMN avancés : la **Passerelle Basée sur Événements** (*Event-Based Gateway*) pour modéliser l'attente concurrente entre paiement et échéance, et la **boucle de relance** pour la procédure de relance (*dunning*) — encapsulée dans un sous-processus replié au Niveau 3.

![[Study Case - BPMN - Niveau 2.3 Finance.png]]

> **📎 Diagramme :** `Study Case - BPMN - Niveau 2.3 Finance.bpmn`
>
> Fichier BPMN 2.0. Contient : deux piscines (Client en boîte noire et NovaTrade-Finance) ; Tâche de Service de génération de facture ; Tâche d'Envoi vers le Client ; Tâche de Service de publication ERP ; Passerelle Basée sur Événements avec deux issues concurrentes (Événement Intermédiaire Message « Paiement reçu » et Événement Intermédiaire Minuteur « Date d'échéance ») ; chemin de retard avec sous-processus replié de relances et Passerelle Exclusive ; chemin de capture commun à T4 ; Tâche de rapprochement ; deux événements de fin (Facture payée, Escalade) ; trois flux de messages avec le Client.

> **Lecture du diagramme :** la Passerelle Basée sur Événements après l'envoi de la facture matérialise une **course** (*race*) entre deux événements : le paiement du Client (Événement Intermédiaire Message) et l'expiration du délai (Événement Intermédiaire Minuteur, calé sur 30 ou 45 jours selon BR-11). Les deux chemins se rejoignent sur la capture du paiement (T4), ou bifurquent vers l'escalade après trois relances infructueuses. Le sous-processus replié « Relancer le client » contient lui-même une boucle interne (Niveau 3, non développée ici).

---

## 5. Justification des Choix de Modélisation

### Une seule piscine pour NovaTrade

Vente, Exécution et Finance sont des départements de la **même organisation** NovaTrade. La règle structurelle BPMN est intransigeante : **une organisation = une piscine, un département = un couloir**. Modéliser Vente, Exécution et Finance comme trois piscines distinctes constituerait l'erreur structurelle la plus fréquente sur ce type de processus, et obligerait à utiliser des Flux de Messages (*Message Flows*) entre départements internes — sémantiquement incorrect et opérationnellement absurde.

### Boîte noire (*black-box*) pour Client et Transporteur

Le Client et le Transporteur sont des organisations externes dont on **ne modélise pas** le détail interne. C'est la sémantique BPMN de la **piscine fermée** (*black-box pool*) : on connaît les messages échangés, pas les processus internes du participant. Si NovaTrade décidait un jour d'intégrer plus profondément avec un Transporteur partenaire (intégration EDI complète), on pourrait déplier sa piscine en boîte blanche — mais c'est hors périmètre ici.

### ERP, Passerelle de paiement et Service de notification : pas de piscines

Ces trois systèmes sont **invoqués** par l'OMS comme des services techniques. À ce niveau d'abstraction métier (BPMN), ils apparaissent sous forme de **Tâches de Service** (*Service Task*) — la facture est « publiée dans l'ERP » par une Tâche de Service ; le paiement est « capturé via la Passerelle » par une Tâche de Service ; le Client est notifié par une **Tâche d'Envoi** (*Send Task*). Le détail technique des appels et des protocoles relève des diagrammes de séquence UML (livrables 03 à 05), pas du BPMN métier.

### Passerelles Parallèles vs Exclusives

Au Niveau 2.1 (Vente), les contrôles statut Client + stock sont mis en parallèle parce qu'ils sont **indépendants** et que l'on veut le résultat des deux avant de décider. C'est exactement le rôle d'une Passerelle Parallèle. À l'inverse, la décision « Tous les contrôles passés ? » est une Passerelle Exclusive : un seul chemin sortant est emprunté à chaque exécution. Ces deux passerelles ne sont **pas interchangeables** — un Fusion Parallèle après une Division Exclusive bloquerait le processus pour toujours, et inversement.

### Passerelle Basée sur Événements pour le paiement

Au Niveau 2.3 (Finance), on attend l'un des deux événements suivants : le paiement du Client (qui peut arriver à tout moment) ou l'échéance de la facture (qui se déclenche automatiquement à 30 ou 45 jours). C'est précisément le cas d'usage de la Passerelle Basée sur Événements (*Event-Based Gateway*) — le **premier** événement qui arrive **gagne** la course. Une simple passerelle exclusive avec deux conditions ne fonctionnerait pas, car les conditions ne sont pas évaluables au moment du choix : on ne sait pas, à l'instant où l'on arrive sur la passerelle, si le paiement va arriver avant ou après l'échéance.

### Sous-processus repliés au Niveau 1

Le Niveau 1 utilise trois sous-processus repliés (un par phase métier). Ce choix respecte la **loi de Miller** (5 à 9 éléments lisibles d'un coup d'œil) et la règle « un seul niveau d'abstraction par diagramme ». Le diagramme de Niveau 1 doit être validable par le Directeur des Opérations en deux minutes. Les détails techniques sont relégués au Niveau 2, et la boucle de relance (qui forme un sous-processus à elle seule) au Niveau 3.

---

## 6. Variantes Acceptables

### Représenter le Système ERP comme une piscine séparée

Certaines organisations préfèrent rendre l'ERP visible comme une piscine fermée séparée pour matérialiser la dépendance technique. Avantage : on voit explicitement les flux de messages avec l'ERP. Inconvénient : on alourdit le diagramme alors que l'ERP est déjà dans le périmètre fonctionnel de NovaTrade. Ce choix est **acceptable** mais non recommandé pour ce niveau d'abstraction métier.

### Découper le sous-processus « Facturer et encaisser » en deux

Au lieu d'un seul sous-processus Finance couvrant facturation et paiement, on peut le scinder en « Facturer » et « Encaisser et rapprocher ». Avantage : meilleure granularité, séparation claire des deux responsabilités. Inconvénient : on dépasse facilement les cinq éléments au Niveau 1 et il faut synchroniser deux sous-processus au lieu d'un. **Acceptable** si le métier le souhaite explicitement.

### Bordure Minuteur globale de SLA

Pour matérialiser un SLA global « commande clôturée sous 45 jours », on peut attacher une **Bordure Minuteur non-interrompante** (*Non-Interrupting Timer Boundary Event*) au sous-processus Finance au Niveau 1, qui notifierait le Management si l'on dépasse ce délai. Cette variante est pédagogiquement intéressante mais introduit une notation avancée que le Niveau 1 cherche normalement à éviter.

### Service de notification en piscine

Si l'on souhaite tracer explicitement les canaux de notification (courriel, SMS), on peut représenter le Service de notification comme une piscine fermée avec ses propres flux de messages sortants. À éviter au Niveau 1 — c'est un détail d'implémentation qui n'apporte rien à la compréhension du processus métier.

---

## 7. Cohérence avec les Livrables Suivants

Ce diagramme BPMN est le **point de départ** de la chaîne d'analyse. Les livrables suivants en dériveront directement, en suivant les règles de transposition de la Section 5.1 du document `Study Case.md` :

- **Livrable 02 — Diagramme de Cas d'Utilisation.** Chaque tâche métier supportée par le système (les Tâches de Service du diagramme BPMN ci-dessus) devient un cas d'utilisation candidat. Les piscines externes (Client, Transporteur) deviennent des acteurs primaires/secondaires. Les couloirs de NovaTrade (Vente, Exécution, Finance) correspondent aux acteurs métier (Représentant commercial, Opérateur d'entrepôt, Contrôleur financier).

- **Livrables 03 à 05 — Diagrammes d'Activité par UC.** Les sous-processus dépliés au Niveau 2 ci-dessus se traduiront en diagrammes d'Activité UML, avec **un couloir par acteur du Use Case + un couloir Système** (cf. la convention « Couloirs Acteurs / Système » du document `Study Case.md`).

- **Livrable 06 — Diagramme de Classes.** Les actions du couloir Système des activités produiront les méthodes du diagramme de classes ; les Objets de Données BPMN (`Order`, `StockReservation`, `Shipment`, `Invoice`, `Payment`) deviendront les classes principales du modèle de domaine.

Garder ce diagramme propre et accessible — il sera référencé à chaque étape suivante du Study Case.

---

*Livrable suivant : `Study Case - Use Case Diagram.md`*
