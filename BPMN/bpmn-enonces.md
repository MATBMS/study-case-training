# BPMN énoncés

## 🟢 Niveau Débutant

### Exercice 01 — La Note de Frais

Chez NovaTrade, les employés soumettent des notes de frais à la fin de chaque déplacement professionnel. Le processus est aujourd'hui entièrement manuel et géré au sein du service Finance. Un employé remplit un formulaire papier, le remet à son manager pour examen, et le manager le transmet à l'équipe Finance pour remboursement. La Finance vérifie les montants, exécute le virement bancaire et notifie l'employé par courriel. Le processus se termine lorsque l'employé reçoit la confirmation de remboursement.

Votre mission est de modéliser ce processus en diagramme BPMN pour la première fois.

### Exercice 02 — Décision d'Approbation de Facture

L'équipe Finance de NovaTrade reçoit chaque jour des factures de fournisseurs. Toutes les factures ne sont pas équivalentes : les factures inférieures à 5 000 € peuvent être approuvées directement par le Contrôleur, mais celles d'un montant égal ou supérieur à 5 000 € exigent la signature du CFO avant paiement. Si une facture échoue au contrôle de format initial — par exemple, numéro de TVA manquant ou IBAN incorrect — elle est immédiatement rejetée et un avis de rejet est renvoyé au fournisseur. Le processus se termine soit par l'exécution du paiement, soit par l'envoi de l'avis de rejet.

Votre mission est de modéliser ce processus de routage d'approbation au sein du service Finance de NovaTrade.

### Exercice 3 — Collaboration Fournisseur–Acheteur

NovaTrade souhaite documenter son échange complet de factures avec un fournisseur — pas uniquement son processus interne, mais l'ensemble de la collaboration. Quand un fournisseur achève une livraison, il génère et envoie une facture. NovaTrade la reçoit, la valide, puis la paie ou la rejette. Dans les deux cas, le fournisseur est notifié : confirmation de paiement ou avis de rejet. Le fournisseur archive le résultat et clôt son enregistrement de facturation. NovaTrade ne sait pas comment le fournisseur génère sa facture en interne.

Votre mission est de modéliser cela sous forme de diagramme de Collaboration (*Collaboration diagram*) avec deux Piscines.

## 🟡 Niveau Intermédiaire

### Exercice 4 — Préparation de Commande Parallèle

Lorsque NovaTrade reçoit un bon de commande confirmé d'un client, trois opérations indépendantes doivent se produire simultanément : l'équipe Entrepôt réserve et emballe les marchandises, l'équipe Finance génère la facture et l'envoie au client, et l'équipe Logistique réserve un transporteur et planifie l'expédition. Une fois les trois opérations terminées, l'équipe Gestion des Commandes marque la commande comme préparée et envoie une confirmation de préparation au client. Si les marchandises sont en rupture de stock pendant l'étape entrepôt, la commande est placée sur une liste de réapprovisionnement (*backorder*) et le client est notifié du retard — le flux de préparation se termine à ce stade.

### Exercice 5 — Gestion d'Exceptions avec Événements de Bordure

Le processus d'approbation des factures de NovaTrade rencontre deux problèmes opérationnels récurrents. D'abord, les managers laissent fréquemment des tâches d'approbation traîner dans leur boîte de réception pendant des jours — NovaTrade a un SLA interne exigeant que toutes les approbations soient achevées sous 48 heures. Si le délai est dépassé, le Directeur Financier doit être automatiquement notifié pendant que la tâche d'approbation reste ouverte. Ensuite, l'étape d'exécution du paiement se connecte à une API bancaire externe qui renvoie occasionnellement une erreur. Lorsque cela se produit, le paiement échoué doit être consigné, l'équipe Finance notifiée manuellement et la facture remise dans la file « paiement en attente » pour une nouvelle tentative.

Votre mission est d'ajouter la gestion d'exceptions au modèle d'approbation de factures de l'Exercice 02.

### Exercice 6 — Modélisation du Flux de Données

L'équipe Conformité de NovaTrade a demandé une version du processus d'approbation de factures qui montre explicitement toutes les entrées de données, les sorties et les écritures système — elle en a besoin pour un audit à venir. Plus précisément : le processus utilise un Contrat Fournisseur (stocké en externe dans le système de gestion des contrats) pour valider les conditions de la facture ; le document de facture change d'état au fil de sa progression ; la facture approuvée est écrite dans le système ERP pour la comptabilité ; et une annotation textuelle (*Text Annotation*) doit documenter la règle SOX de double contrôle qui exige que le CFO contresigne toute facture supérieure à 10 000 €.

Votre mission est d'enrichir le modèle de l'Exercice 02 d'une couche complète de données.

## 🔴 Niveau Avancé

### Exercice 7 — Processus d'Approvisionnement Top-Down

Le processus d'approvisionnement (*procurement*) de NovaTrade commence lorsqu'un service soumet une demande d'achat. L'équipe Achats valide la demande par rapport au budget annuel, sélectionne un fournisseur dans la liste des fournisseurs approuvés, négocie les conditions et émet un bon de commande. Une fois le bon de commande envoyé, un sous-processus distinct gère l'intégration (*onboarding*) du fournisseur s'il est nouveau (contrôle KYC, création de contrat, approbation). À l'arrivée des marchandises, un rapprochement à trois voies (*three-way match*) est effectué : le bon de réception est comparé au bon de commande et à la facture du fournisseur. Si les trois correspondent, le paiement est autorisé et exécuté. En cas de divergence, le problème est escaladé au Directeur des Achats.

Votre mission est de modéliser ce processus **à deux niveaux** : une vue d'ensemble Niveau 1 utilisant des Sous-processus repliés, et une expansion complète Niveau 2 du seul sous-processus « Rapprochement à Trois Voies ».

### Exercice 8 — Confirmation de Bon de Commande Basée sur Événements

Lorsque NovaTrade émet un bon de commande à un fournisseur, elle attend que le fournisseur renvoie une confirmation de commande. En pratique, trois choses peuvent se produire : (1) le fournisseur confirme la commande dans les 3 jours ouvrés — NovaTrade enchaîne en planifiant la livraison ; (2) le fournisseur rejette la commande — NovaTrade relance le processus de sélection de fournisseur ; (3) le fournisseur ne répond pas dans les 3 jours — NovaTrade envoie un rappel automatisé et redémarre l'attente pour 2 jours supplémentaires. Si aucune réponse n'arrive après la seconde attente non plus, la commande est automatiquement annulée et un nouveau fournisseur est recherché. Par ailleurs, le système financier de NovaTrade surveille tous les bons de commande ouverts — si un BC reste non confirmé pendant plus de 10 jours ouvrés au total, le Contrôleur Financier est automatiquement notifié sans que le BC soit annulé.

### Exercice 9

NovaTrade souhaite documenter son processus complet **Order-to-Cash** (commande-vers-encaissement) — du moment où un client professionnel passe une commande au moment où le paiement est reçu et rapproché. Le processus implique quatre participants : le **Client** (externe, Boîte Noire), **NovaTrade Ventes** (prend la commande, confirme la disponibilité, envoie la confirmation de commande), **NovaTrade Opérations** (prélève, emballe et expédie les marchandises) et **NovaTrade Finance** (facture le client, suit le paiement, gère les retards de paiement).

Le flux complet est : le Client passe une commande → Ventes la valide (contrôle de crédit via une Tâche de Règle Métier, contrôle de stock via une Tâche de Service) → en cas d'échec de validation, un rejet est renvoyé ; en cas de succès, Opérations et Finance travaillent en parallèle (Opérations expédie les marchandises ; Finance génère et envoie la facture) → les deux doivent se terminer avant que la commande soit marquée comme expédiée-et-facturée → la Finance attend ensuite le paiement via une Passerelle Basée sur Événements (paiement reçu OU minuteur de 30 jours) → si le paiement arrive, la Finance rapproche et clôt la commande → si 30 jours s'écoulent, un processus de relance (*dunning*) est déclenché (3 rappels à intervalles de 7 jours via une boucle, puis escalade juridique si toujours impayé).

Votre mission est de modéliser cela de bout en bout selon l'approche descendante (*top-down*) : produire d'abord une **vue d'ensemble Niveau 1** (Sous-processus repliés), puis développer entièrement chaque Sous-processus.