# UML — Diagramme d'Activité — Énoncés des Exercices

Tous les exercices se déroulent au sein de la même entreprise fictive — **NovaTrade**, société de négoce B2B. Vous serez chaque fois reçu par un membre de l'équipe NovaTrade qui vous décrira le comportement à modéliser ; à vous d'écouter, d'identifier les acteurs frontend, les opérations backend et les structures de contrôle (décision, parallélisme, sous-activités, nœuds d'objet) à mettre en œuvre.

> [!warning] Cadre de modélisation : Acteur(s) frontend + SI backend
> En UML, le diagramme d'activité sert à spécifier le comportement d'un **Système d'Information** (SI). Chaque exercice utilise donc systématiquement deux types de partitions (couloirs — *swimlanes*) :
> - une partition par **acteur humain** : capture les actions effectuées via l'**interface frontend** (saisir, cliquer, consulter à l'écran)
> - une partition unique **`Système d'Information`** : capture toutes les opérations **backend automatisées** (valider, calculer, persister, appeler une API, envoyer un courriel)
>
> Les actions du couloir SI deviennent directement des **opérations sur les classes** ([[UML Class]]) et les traversées de partition entre Acteur et SI deviennent les **messages** ([[UML Sequence]]). C'est ce qui assure la cohérence inter-diagrammes UML.
>
> Cette approche distingue fortement le diagramme d'activité UML d'un diagramme de processus métier [[BPMN Exercices|BPMN]] qui, lui, modélise les rôles organisationnels sans nécessairement isoler le SI.

---

## 🟢 Niveau Débutant

### Exercice 01 — Soumission d'une Note de Frais

Léa, responsable Finance chez NovaTrade — déjà rencontrée sur plusieurs autres modules — vous demande de modéliser le tout premier flux de notre portail interne, en partant simple.

> « C'est le flux le plus basique qu'on ait : un collaborateur soumet sa note de frais via le portail. Voilà ce qui se passe, étape par étape. »
>
> « Le collaborateur ouvre le portail. Il saisit le formulaire de note de frais — la date, le motif, les montants ligne par ligne. Ensuite il joint les justificatifs : factures, tickets de restaurant, peu importe. Et il clique sur "Soumettre". »
>
> « À partir de là, c'est notre Système d'Information qui prend la main, et il enchaîne quatre traitements automatisés sans intervention humaine. D'abord, il valide les champs obligatoires — pas de date manquante, pas de montant à zéro, ce genre de choses. Ensuite, il calcule le montant total à rembourser à partir des lignes saisies. Puis il enregistre la note dans notre base avec le statut `Soumise`. Et enfin, il envoie un accusé de réception. »
>
> « Le portail affiche cet accusé à l'écran, et le collaborateur le consulte. Voilà, c'est tout — pour cette V1 on s'arrête à la soumission, pas d'approbation manager, pas de virement, aucun cas d'erreur. On veut juste un schéma propre du flux nominal. »
>
> « Le truc important pour mes développeurs : qu'on voie clairement ce qui est fait par le collaborateur via l'interface (saisir, joindre, cliquer, voir) et ce qui est fait par le SI tout seul (valider, calculer, enregistrer, envoyer). C'est cette distinction frontend/backend qui leur permet de savoir où placer chaque méthode dans le code. »

Votre mission est de modéliser ce flux linéaire sous forme d'un diagramme d'activité (*Activity Diagram*) UML avec deux partitions, en respectant strictement la séparation entre actions utilisateur (couloir Employé) et opérations automatisées (couloir Système d'Information).

---

### Exercice 02 — Validation d'une Facture avec Décision

Karim, responsable du pôle Comptes Fournisseurs chez NovaTrade — déjà rencontré sur les exercices BPMN — vous demande de modéliser maintenant le flux de saisie d'une facture, qui comporte cette fois un point de décision.

> « Le Comptable saisit les factures fournisseurs dans notre portail. Le flux nominal est simple : il saisit les données de la facture, puis il clique sur "Soumettre". Mais il y a une subtilité que j'aimerais qu'on capture proprement. »
>
> « Quand une saisie a été rejetée par le système (on en parle juste après), le Comptable a la possibilité de corriger sa saisie au lieu de tout recommencer depuis zéro. Donc côté Comptable, deux chemins peuvent mener à "Cliquer Soumettre" : soit il vient juste de saisir une facture neuve, soit il vient de corriger une saisie précédemment rejetée. Les deux flux convergent vers la même action de soumission. »
>
> « Maintenant, ce que fait le SI à la réception. Il valide automatiquement le format de la facture — numéro de TVA, IBAN, cohérence des montants. Cette vérification produit un verdict : `valide` ou `invalide`. C'est le SI qui tranche, ce n'est pas le Comptable. »
>
> « Si le verdict est `valide`, le SI enregistre la facture avec le statut `Reçue`, génère un accusé de réception, et le Comptable voit l'accusé à l'écran — fin nominale. »
>
> « Si le verdict est `invalide`, le SI affiche un message de rejet. À ce moment-là, le Comptable a un choix purement humain à faire : soit il décide de corriger sa saisie — et son flux remonte vers la convergence dont je parlais avant, juste avant "Cliquer Soumettre" — soit il décide d'abandonner la facture. S'il abandonne, je veux qu'il y ait une action explicite "Abandonner" dans son couloir avant la fin d'activité, pas juste une flèche dans le vide. C'est important pour la traçabilité — on saura que c'est le Comptable qui a renoncé, pas un bug du système. »
>
> « Donc on a en réalité **deux décisions** dans ce flux : une décision automatique côté SI (`valide` / `invalide`), et une décision humaine côté Comptable (`corriger` / `abandonner`). Il faut bien les distinguer dans la modélisation. »

Votre mission est de modéliser ce flux avec : (a) une décision automatique dans le couloir SI ; (b) une décision humaine dans le couloir Comptable, déclenchée directement par l'action SI d'affichage du rejet ; (c) un nœud de fusion dans le couloir Comptable où vient se greffer la branche de correction ; (d) une action terminale explicite `Abandonner` avant le Nœud Final d'Activité de la branche d'abandon.

---

### Exercice 03 — Vérification de Stock à Trois Voies

Antoine, responsable Order Management chez NovaTrade — déjà rencontré sur le diagramme d'État — vous demande de modéliser le flux de soumission de commande côté SI, en se concentrant sur la vérification de stock.

> « Quand un Client soumet une commande via notre portail web, voilà ce qui se passe côté Système d'Information. Le SI reçoit la commande, puis il vérifie la disponibilité du stock pour les articles demandés. C'est cette vérification qui est intéressante — elle peut produire **trois résultats**, pas juste deux. »
>
> « **Premier résultat : entièrement en stock.** Tous les articles demandés sont disponibles en quantité suffisante. Le SI réserve la quantité totale. »
>
> « **Deuxième résultat : partiellement en stock.** Certains articles sont disponibles, d'autres pas. Dans ce cas, le SI fait deux choses dans la foulée : il réserve la quantité disponible, et il crée immédiatement une commande en attente — ce qu'on appelle un *backorder* — pour le reste qui sera livré plus tard. »
>
> « **Troisième résultat : rupture totale.** Rien n'est disponible. Le SI marque la commande comme indisponible. »
>
> « Pour la suite : dans les deux premiers cas — succès complet ou succès partiel — le flux converge vers une action commune, le SI confirme la commande, et le Client voit la confirmation de commande à l'écran. C'est la même conclusion pour les deux, ce qui fait qu'on n'écrit l'action de confirmation qu'**une seule fois** dans le diagramme — sinon on duplique inutilement. Dans le troisième cas, le Client voit un message d'indisponibilité. »
>
> « Les deux issues — confirmation ou indisponibilité — sont des fins normales de l'activité. Pas d'erreur, pas d'exception, juste deux scénarios de fin légitimes. »

Votre mission est de modéliser cette vérification avec un Nœud de Décision à trois sorties dans le couloir SI, et de faire converger les deux chemins de succès via un Nœud de Fusion (*Merge Node*) avant l'action commune de confirmation, pour éviter toute duplication.

---

## 🟡 Niveau Intermédiaire

### Exercice 04 — Approbation de Facture Multi-Acteurs

Patricia, Directrice Financière chez NovaTrade — déjà rencontrée sur les exercices BPMN et Séquence — vous demande maintenant la version complète du processus d'approbation de facture, avec le routage par montant et plusieurs approbateurs.

> « Le flux complet implique trois acteurs humains côté Finance, plus le SI. Voilà comment ça doit se dérouler. »
>
> « Le **Comptable** saisit la facture dans le portail puis clique "Soumettre". Le SI valide d'abord le format. Si le format est invalide, un message de rejet s'affiche au Comptable, qui peut corriger sa saisie — cette branche d'erreur s'arrête là **sans terminer toute l'activité**, c'est important : c'est une fin de flux locale, pas une fin de l'activité. »
>
> « Si le format est valide, le SI **route automatiquement la facture selon son montant**. Sous 5 000 €, c'est le Contrôleur qui approuve. À partir de 5 000 €, c'est le Manager qui approuve. C'est un seuil métier strict, et c'est le SI qui prend la décision de routage — pas un humain. »
>
> « Le **Contrôleur** ou le **Manager**, selon le routage, consulte la facture à l'écran et clique "Approuver". Pour cet exercice, on ne couvre **pas** le rejet d'approbation — on suppose qu'arrivés là, ils approuvent toujours, on cadre simple. »
>
> « Une fois l'approbation cliquée, les deux chemins (Contrôleur et Manager) convergent côté SI. Le SI enregistre l'approbation, exécute le paiement via l'ERP — qu'on considère ici comme partie du SI, pas un acteur séparé — et notifie le Comptable. Le Comptable voit la confirmation de paiement à l'écran ; l'activité se termine avec succès. »
>
> « Le truc à bien capter : toutes les **décisions de routage** et toutes les **opérations automatisées** (validation, routage par montant, enregistrement, paiement, notification) sont dans le couloir SI. Les couloirs humains ne contiennent que des actions effectuées via l'interface — saisir, examiner, cliquer, voir. Si ton apprenant met "Affecter au Contrôleur" dans le couloir Contrôleur, c'est faux : c'est une action automatique du SI qui pousse la facture *vers* le Contrôleur. »

Votre mission est de modéliser ce processus avec quatre couloirs (Comptable, Contrôleur, Manager, Système d'Information), en respectant rigoureusement le placement des décisions et des opérations automatisées dans le couloir SI, et en utilisant un Nœud Final de Flux (et non d'activité) pour la branche d'erreur de format.

---

### Exercice 05 — Préparation de Commande Parallèle (Fourche/Jointure dans le SI)

Pauline, Directrice des Opérations chez NovaTrade — déjà rencontrée sur les diagrammes BPMN, Séquence et Cas d'Utilisation — vous demande de modéliser la phase de préparation côté SI, qui implique du parallélisme.

> « Le flux est déclenché par un Représentant Commercial qui ouvre le portail, sélectionne une commande à préparer et clique "Confirmer". Le SI reçoit cette confirmation, et c'est là que ça devient intéressant. »
>
> « À partir de là, le SI lance **trois opérations backend en parallèle** — sans condition entre elles, elles partent simultanément. Trois branches qui s'exécutent en concurrence. »
>
> « **Branche 1 — Entrepôt :** créer l'ordre de prélèvement à destination de notre système d'entrepôt. C'est une seule action, simple. »
>
> « **Branche 2 — Facturation :** générer le PDF de facture, puis envoyer le courriel client. Ces deux étapes sont séquentielles à l'intérieur de la branche — on ne peut pas envoyer le courriel avant d'avoir généré le PDF. Donc dans cette branche, on a deux actions enchaînées. »
>
> « **Branche 3 — Logistique :** appeler l'API logistique externe, puis réserver un transporteur. Là aussi, deux actions séquentielles dans la branche — il faut d'abord la réponse de l'API pour pouvoir réserver. »
>
> « Le point critique : ces **trois branches s'exécutent vraiment en parallèle**. Ce n'est pas "soit l'une soit l'autre", c'est "toutes les trois en même temps". Et le SI doit attendre que les trois soient terminées avant de poursuivre. »
>
> « Une fois les trois branches arrivées au point de synchronisation, le SI marque la commande avec le statut `Préparée` puis notifie le Représentant. Le Représentant voit la confirmation de préparation à l'écran ; l'activité se termine. »

Votre mission est de modéliser cette exécution parallèle dans le couloir SI avec une paire **Fourche/Jointure** (*Fork/Join*) ; les transitions sortant de la Fourche sont inconditionnelles (toutes déclenchées simultanément), et la Jointure attend que les trois branches soient terminées avant de poursuivre.

---

### Exercice 06 — Cycle de Vie d'une Facture avec Nœuds d'Objet

Sophie, responsable conformité chez NovaTrade — déjà rencontrée sur l'exercice de collaboration BPMN — vous demande de modéliser le processus d'approbation de facture en faisant **explicitement apparaître les états successifs de l'objet `Facture`**.

> « Pour notre prochain audit, j'ai besoin de pouvoir tracer visuellement comment l'objet `Facture` change d'état au fur et à mesure du processus. Le diagramme d'activité doit montrer ces transitions d'état directement, pas juste les actions. En UML il y a un mécanisme exprès pour ça — les Nœuds d'Objet (*Object Nodes*) — qui se placent entre les actions et qui matérialisent l'état de l'entité à ce moment-là. »
>
> « Voilà le flux. Le Comptable saisit la facture puis clique "Soumettre" ; à ce moment, l'objet `Facture` entre dans le SI à l'état `Reçue`. Le SI valide le format : la facture passe à l'état `Validée`. Le SI affecte ensuite la facture au Contrôleur — c'est une action automatique de routage côté SI — et le Contrôleur la voit apparaître dans son interface. »
>
> « Le Contrôleur examine la facture à l'écran puis prend une décision **humaine** entre deux options : il clique soit "Approuver", soit "Rejeter". Important : **ce n'est pas une décision système** — ce sont deux boutons distincts dans son interface, donc deux actions distinctes dans son couloir. Pas un Nœud de Décision SI, vraiment deux clics. »
>
> « Sur "Approuver", l'objet passe à l'état `Approuvée`, le SI exécute le paiement via l'ERP, l'objet passe à l'état `Payée` et l'activité se termine avec succès. Sur "Rejeter", l'objet passe à l'état `Rejetée` et la branche s'arrête — mais comme à l'Exercice 04, c'est une fin de **flux** local, pas une fin d'activité globale. »
>
> « Et un point absolument critique pour mon audit : les états doivent **strictement correspondre** à l'énumération `StatutFacture` qu'on a définie dans le Diagramme de Classes. La séquence canonique est `Reçue` → `Validée` → `Approuvée` (ou `Rejetée`) → `Payée`. Pas de variante d'orthographe, pas de synonyme. Si un développeur lit le diagramme d'activité et le Diagramme de Classes en parallèle, les deux doivent dire exactement la même chose. »

Votre mission est de modéliser ce processus en ajoutant des **Nœuds d'Objet** (rectangles simples avec étiquettes d'état) entre les actions clés du couloir SI, en plaçant la décision Contrôleur comme **deux clics distincts** dans son couloir (et non comme un Nœud de Décision SI), et en utilisant deux types de nœuds finaux pour distinguer la fin nominale (`Payée`, fin d'activité) du rejet (`Rejetée`, fin de flux local).

---

## 🔴 Niveau Avancé

### Exercice 07 — Décomposition Order-to-Cash avec Sous-Activités

Vincent, architecte principal chez NovaTrade — déjà rencontré sur les autres séries UML — vous reçoit pour spécifier le processus complet **Order-to-Cash** au niveau analyse.

> « Le flux complet d'Order-to-Cash, c'est trop dense pour un seul diagramme. On va l'aborder à **deux niveaux** : une vue d'ensemble qui synthétise les grandes étapes, et un zoom détaillé sur l'une de ces étapes. »
>
> « **Niveau 1 — Vue d'ensemble.** Le Client soumet sa commande, et tout en bas du flux, il voit la confirmation finale. Entre les deux, côté SI, le traitement enchaîne **cinq sous-activités** dans cet ordre : valider la commande, réserver l'inventaire, traiter le paiement, préparer la commande, et réconcilier le paiement. Chacune de ces sous-activités est en réalité un mini-processus à part entière, qu'on ne déplie pas ici. En UML, on les modélise comme des **Actions d'Appel de Comportement** (*Call Behavior Actions*), avec le marqueur râteau ⌬ qui indique "ceci est appelé, le détail est ailleurs". »
>
> « Pour cet exercice, on va déplier en détail seulement une de ces sous-activités : `Traiter le paiement`. Une note attachée à cette action sur le diagramme Niveau 1 doit indiquer qu'elle se décompose dans le diagramme Niveau 2. »
>
> « **Niveau 2 — Sous-activité Traiter le paiement.** Trois couloirs : le Client, le Système d'Information, et la Passerelle de Paiement (acteur secondaire de type système, comme dans le Use Case). Voilà le flux : le Client saisit ses informations de paiement et clique "Payer". Le SI vérifie le crédit du client, ce qui produit un verdict `crédit OK` ou `crédit rejeté`. »
>
> « Si **crédit rejeté**, le SI notifie l'échec, le Client voit le message d'échec, l'activité se termine. »
>
> « Si **crédit OK**, le SI prépare la requête de paiement — signature, chiffrement, idempotence — et l'envoie à la Passerelle. La Passerelle autorise la transaction, ce qui produit `approuvé` ou `refusé`. »
>
> « Si **approuvé**, le SI enregistre la transaction et la sous-activité se termine en **rendant le contrôle au Niveau 1** — l'activité parente continue. C'est important : on n'utilise **pas** un Nœud Final d'Activité ici, parce que ça arrêterait toute l'activité parente. On utilise un **Nœud Final de Flux** (*Flow Final*), qui termine juste cette branche en rendant la main à l'appelant. »
>
> « Si **refusé**, le SI journalise l'échec, le Client voit le message de refus, l'activité se termine — là par contre c'est un Nœud Final d'Activité côté Client, parce que pour le Client, c'est terminé. »

Votre mission est de produire le diagramme Niveau 1 avec ses cinq Actions d'Appel de Comportement, et l'expansion Niveau 2 de `Traiter le paiement` avec ses trois couloirs, en respectant la distinction entre **nœud final de flux** (succès Niveau 2 — rend le contrôle au parent) et **nœuds finaux d'activité** (échecs côté Client — terminent toute l'activité).

---

### Exercice 08 — Préparation de Commande Complète Multi-Acteurs

Pauline revient pour détailler la sous-activité `Préparer la commande` du Niveau 1 de l'Exercice 07. Cette sous-activité est plus complexe que la préparation parallèle de l'Exercice 05 parce qu'elle implique des **interactions humaines** dans chaque branche.

> « On entre dans cette sous-activité avec une commande à l'état `Confirmée`, et on doit en sortir avec la commande à l'état `Expédiée` — c'est le cas nominal. »
>
> « Le SI reçoit l'instruction de préparation, puis lance une Fourche vers **trois branches parallèles**. Chacune des trois branches mélange des actions automatiques côté SI et des **validations humaines via interface** sur l'un des trois acteurs métier. C'est ça qui change par rapport à l'Exercice 05 : ce n'est plus juste du parallélisme dans le SI, c'est du parallélisme qui implique des humains. »
>
> « **Branche Entrepôt.** Le SI crée l'ordre de prélèvement. L'**Opérateur d'Entrepôt** consulte cet ordre dans son interface, prélève physiquement les articles, puis prend une décision **humaine** sur l'état du stock physique : `disponibles` ou `indisponibles`. C'est l'humain qui constate, pas le système. »
>
> « Si **disponibles**, l'Opérateur emballe puis clique "Confirmer prêt" — l'objet `Expédition` passe à l'état `Emballée` côté SI et la branche poursuit vers la Jointure. Si **indisponibles**, l'Opérateur clique "Signaler en attente" — le SI marque la commande en attente et la branche s'arrête. »
>
> « **Cas particulier à anticiper :** si la branche entrepôt s'arrête avec "indisponibles", la Jointure attend toujours trois jetons par sa sémantique standard — donc en théorie elle bloque pour toujours dans ce cas. C'est une question pédagogique légitime à creuser, mais pour cet exercice on accepte cette limitation et on la signale ; on ne va pas modéliser de mécanisme complexe d'annulation des autres branches. »
>
> « **Branche Facture.** Le SI génère le PDF de facture. Le **Comptable** consulte la facture, puis clique "Envoyer". Le SI envoie alors la facture au client — l'objet `Facture` passe à l'état `Envoyée` et la branche poursuit vers la Jointure. »
>
> « **Branche Logistique.** Le SI réserve un transporteur via une API logistique externe. Le **Service Logistique** consulte le créneau proposé, puis clique "Confirmer". Le SI planifie la collecte ; la branche poursuit vers la Jointure. »
>
> « Une fois les trois branches arrivées à la Jointure, le SI met à jour le statut de la commande à `Expédiée`, puis envoie la notification d'expédition au client. L'activité se termine alors avec succès. »
>
> « Et bien sûr, comme à l'Exercice 06, je veux les **Nœuds d'Objet** : `Commande [Confirmée]` à l'entrée, `Commande [Expédiée]` à la sortie, plus les états intermédiaires `Expédition [Emballée]` et `Facture [Envoyée]`. »

Votre mission est de modéliser cette préparation parallèle avec quatre couloirs (Opérateur d'Entrepôt, Comptable, Service Logistique, Système d'Information), en faisant apparaître explicitement les Nœuds d'Objet, en plaçant la décision « disponibles / indisponibles » dans le couloir Opérateur (décision humaine et non SI), et en utilisant un Nœud Final de Flux pour la branche d'arrêt en attente.

---

### Exercice 09 — Activité Order-to-Cash Complète + Cohérence Inter-Diagrammes

Vincent vous reçoit pour la **dernière itération** : produire la spécification d'activité finale du processus Order-to-Cash, avec **cohérence inter-diagrammes complète** vis-à-vis des Diagrammes de Classes ([[UML Class]]) et de Séquence ([[UML Sequence]]) déjà produits.

> « C'est le diagramme qui va être lu en parallèle du diagramme de classes et du diagramme de séquence pour valider la conception complète de l'OMS. Il doit être canonique — tout doit s'aligner. »
>
> « **Premier livrable : Niveau 1 enrichi.** On reprend la chaîne des cinq sous-activités de l'Exercice 07 (`Valider la commande ⌬`, `Réserver l'inventaire ⌬`, `Traiter le paiement ⌬`, `Préparer la commande ⌬`, `Réconcilier le paiement ⌬`), et on **insère un Nœud d'Objet `Commande [<État>]` entre chaque sous-activité** pour matérialiser explicitement la transition d'état. La séquence canonique des états est : `Reçue` (entrée, juste après la soumission Client) → `Validée` → `Confirmée` → `PaiementAutorisé` → `Expédiée` → `Clôturée` (sortie, juste avant l'affichage de la confirmation finale au Client). Ces étiquettes doivent strictement correspondre à l'énumération `Commande.statut` du Diagramme de Classes — pas de variante. »
>
> « **Deuxième livrable : Niveau 2 — Valider la commande.** Trois couloirs : Client, Système d'Information, Service Inventaire (acteur secondaire système, sous-système interne distinct du SI principal). Le Client a soumis la commande ; le SI vérifie la session, vérifie le catalogue produit, interroge le Service Inventaire pour la disponibilité du stock, puis applique les règles de validation. La décision finale (commande valide ou non) est prise dans le couloir SI. »
>
> « **Troisième livrable : Niveau 2 — Préparer la commande.** Reprendre et raffiner le diagramme de l'Exercice 08. Tu peux ajouter quelques Nœuds d'Objet supplémentaires si tu veux affiner la traçabilité. »
>
> « **Quatrième livrable : Niveau 2 — Réconcilier le paiement.** Trois couloirs : Client, Système d'Information, Système ERP. Cette sous-activité est différente des autres : le SI **n'exécute pas activement** une opération, il **attend un événement externe** — la réception du paiement client. En UML, on modélise ça avec une **Action d'Acceptation d'Événement** (*Accept Event Action*), pictogramme pentagone concave ou ⌚, intitulée "Accepter le paiement". »
>
> « Une fois l'événement reçu, le SI évalue le délai : `[reçu à temps]` ou `[en retard]`. Si reçu à temps, le SI réconcilie le paiement (et le Système ERP comptabilise l'écriture de paiement de son côté), puis met à jour la commande à l'état `Clôturée` ; l'activité se termine. Si en retard, le SI envoie une notification de relance au client puis **boucle** vers l'Action d'Acceptation d'Événement pour attendre à nouveau. Le Client, de son côté, finit par soumettre le paiement, ce qui déclenche l'événement attendu par le SI. »
>
> « **Cinquième livrable, et c'est le plus important : la liste de cohérence inter-diagrammes.** Pour chaque action du couloir SI sur l'ensemble des diagrammes Niveau 1 + Niveau 2, vérifier qu'elle correspond bien à : (a) une opération du Diagramme de Classes ; (b) un message du Diagramme de Séquence ; (c) un état du Nœud d'Objet quand applicable. Le tableau doit comporter au moins **dix lignes** et signaler explicitement les divergences trouvées — opération manquante, message absent, nom incohérent. »
>
> « Et un callout `[!warning]` final qui liste chaque divergence avec sa recommandation de résolution en une phrase. C'est ce tableau qui va déclencher les corrections nécessaires avant qu'on parte en implémentation. »

Votre mission est de produire la spécification complète : Niveau 1 enrichi avec Nœuds d'Objet, trois Niveau 2 (Valider, Préparer, Réconcilier), le tableau de cohérence inter-diagrammes (≥ 10 lignes), et le callout `[!warning]` listant chaque divergence avec sa recommandation de résolution.

---

*Page suivante : [[UML Activity Solutions]] — solutions modèles avec justifications.*
