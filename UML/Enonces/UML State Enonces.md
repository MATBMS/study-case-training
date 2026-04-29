# UML — Diagramme d'État — Énoncés des Exercices

Tous les exercices se déroulent au sein de la même entreprise fictive — **NovaTrade**, société de négoce B2B. Vous serez chaque fois reçu par un membre de l'équipe NovaTrade qui vous décrira son besoin avec ses propres mots ; à vous d'écouter, d'extraire les éléments pertinents (états, événements, gardes, actions internes, états composites, pseudo-états) et de mobiliser les pages théoriques pour produire un diagramme d'état (*State Machine Diagram*) UML correct.

---

## 🟢 Niveau Débutant

### Exercice 01 — Cycle de Vie de la Facture (Linéaire)

Camille vient d'intégrer le service Finance de NovaTrade en tant que comptable junior. Elle revient de sa première semaine en immersion et a besoin de poser un cadre clair pour suivre.

> « Quand mes collègues me parlent d'une facture, ils utilisent toujours des étiquettes différentes — "celle-là est encore au brouillon", "celle-ci, elle est partie chez le client", "celle-là on attend encore le règlement", "ah celle-là elle est en retard"… Au début je ne suivais plus. Je me suis dit que ce qui me manquait, c'était un schéma simple qui me dirait *par quels états passe une facture, dans quel ordre, et où elle finit*. Pas besoin pour l'instant des détails techniques — pas d'événements précis, pas de conditions, juste les grandes étapes. »
>
> « Si je résume ce que j'ai compris : une facture commence sa vie chez nous comme un brouillon. Une fois qu'elle est prête, on l'envoie au client. À partir de là, on attend qu'elle soit payée. Quand le paiement arrive à temps, la facture passe au statut payée. Si la date d'échéance passe sans qu'on ait reçu d'argent, la facture est en retard — mais elle peut très bien finir par être payée plus tard, et dans ce cas-là aussi elle passe au statut payée. Une facture payée, c'est terminé pour elle, on ne la touche plus. »

Votre mission est de modéliser ce cycle de vie sous forme d'un diagramme d'état (*State Machine Diagram*) UML, en se contentant pour le moment des états et de leurs transitions, sans étiqueter les événements.

---

### Exercice 02 — Ajouter les Événements de Transition

Bruno, lead développeur dans l'équipe de Camille, regarde le diagramme qu'elle a produit à l'Exercice 01 et constate qu'il faut aller plus loin pour pouvoir l'implémenter.

> « C'est très bien comme synthèse, mais à partir du moment où on veut coder ce cycle, on a besoin d'aller plus loin : il faut savoir *ce qui* fait passer une facture d'un état à un autre. Une transition non étiquetée, je ne sais pas quoi en faire — un développeur derrière son écran ne peut pas implémenter "il y a une flèche", il a besoin du nom de l'événement qui la déclenche. »
>
> « Donc reprenons le diagramme et collons un événement sur chaque flèche. Quand on passe le brouillon en envoi au client, ça correspond à l'événement `factureEnvoyee` chez nous. La transition de envoyée vers en-attente-de-paiement, c'est `livraisonConfirmee` — parce qu'en interne, on attend la confirmation logistique avant de réclamer le paiement. Quand on reçoit le paiement à temps, c'est `paiementRecu`. Si la date d'échéance passe sans rien, le système déclenche `dateEcheanceDepassee` — c'est un événement généré par notre minuteur de fond. Si on finit par être payés en retard, c'est `paiementTardifRecu`. Et la fin du cycle, quand on archive la facture définitivement, c'est `factureCloturee`. »
>
> « Tant qu'on y est, j'ai aussi besoin d'un état que tu n'as pas modélisé : `Annulée`. Une facture peut être annulée tant qu'elle est encore au brouillon, ou même après envoi si le client se rétracte. L'événement qui déclenche tout ça s'appelle `annulationDemandee`. Une fois annulée, elle est archivée par un événement `archiverAnnulee` et c'est terminé. »

Votre mission est d'enrichir le diagramme de l'Exercice 01 avec les étiquettes d'événements fournies par Bruno, et d'y intégrer le nouvel état `Annulée` ainsi que ses transitions entrantes et sortantes.

---

### Exercice 03 — Actions d'Entrée, de Sortie et d'Activité

Bruno revient avec une nouvelle préoccupation, après que ses développeurs ont commencé à implémenter le code à partir du diagramme.

> « Le diagramme me dit *ce qui* déclenche les transitions, mais il ne me dit pas *ce qui se passe automatiquement* quand on entre ou quand on sort d'un état. Or il y a tout un tas d'actions qu'on doit déclencher sans qu'aucun événement externe ne soit nécessaire — juste parce que la facture vient de basculer dans un nouvel état. »
>
> « Concrètement : dès qu'une facture passe en envoyée, deux choses doivent partir automatiquement — la génération du PDF (`genererPDF`) et l'envoi du courriel au client (`envoyerCourrielClient`). C'est immédiat, dès l'entrée dans l'état. Pendant que la facture vit dans l'état d'attente de paiement, il y a un job de fond qui tourne en continu et qui surveille la date d'échéance (`surveillerDateEcheance`) — pas une action ponctuelle, c'est *en continu*, tant qu'on est dans cet état. Quand une facture bascule en retard, on envoie immédiatement un premier rappel de relance (`envoyerRappelRelance`) — encore une fois, dès l'entrée. Et quand la facture sort de l'état en retard — peu importe pourquoi, qu'elle soit payée ou autrement — on doit journaliser combien de temps elle est restée en retard (`journaliserDureeRetard`) ; c'est important pour les statistiques de recouvrement. Enfin, dès qu'une facture passe au statut payée, peu importe par quel chemin, on enregistre l'horodatage du paiement (`enregistrerHorodatagePaiement`). »
>
> « En UML, je crois que ces choses-là s'appellent des actions d'entrée, d'activité et de sortie — `entry /`, `do /`, `exit /`. C'est ce qu'on doit ajouter sur le diagramme pour que mon équipe sache quoi coder dans les hooks de chaque état. »

Votre mission est d'enrichir le diagramme de l'Exercice 02 en y ajoutant les actions internes aux états concernés, en respectant la sémantique propre à chaque type d'action (sur entrée, pendant le séjour, sur sortie), et en accompagnant le diagramme d'une courte note expliquant la différence entre une action attachée à une transition et une action interne d'état.

---

## 🟡 Niveau Intermédiaire

### Exercice 04 — Conditions de Garde sur le Cycle de Vie d'une Commande

Antoine, responsable Order Management chez NovaTrade, vous fait venir pour formaliser le cycle de vie de l'objet `Commande` dans l'application interne.

> « Le cycle d'une commande, à première vue ça paraît linéaire — brouillon, confirmée, préparation, expédiée, livrée, clôturée. Mais en réalité il y a partout des conditions qui changent la suite. Je vais te lister les règles que mes équipes appliquent. »
>
> « Quand un client confirme sa commande (événement `clientConfirme`), ce qui se passe ensuite dépend de son paiement. Si le paiement est complet et validé, la commande passe directement à confirmée et on envoie un courriel de confirmation au client. Si le paiement n'est que partiel, le même événement de confirmation client envoie la commande dans un état d'attente de paiement — on attend le complément avant de la confirmer. Depuis cet état d'attente, deux issues : si le paiement est complété (`paiementComplete`), la commande passe à confirmée (et là aussi on envoie le courriel) ; si le délai de paiement est dépassé sans que le complément arrive (`delaiPaiementDepasse`), la commande est annulée. »
>
> « Une fois la commande confirmée, le client peut encore l'annuler (`clientAnnule`) — mais uniquement dans les premières 24 heures. Passé ce délai, l'annulation n'est plus possible côté client, c'est dans nos CGV. Sinon, dès que l'entrepôt accepte la commande (`entrepotAccepte`), elle bascule en préparation. La préparation peut mener à deux issues : soit le transporteur vient collecter la marchandise (`transporteurCollecte`) et on passe à expédiée — auquel cas on génère automatiquement l'étiquette d'expédition — soit on découvre que le stock n'est pas disponible (`stockIndisponible`), et la commande est annulée en notifiant le client. »
>
> « Enfin, expédiée mène à livrée quand le transporteur confirme la livraison (`transporteurConfirme`), et livrée mène à clôturée quand la facture associée est payée (`facturePayee`). Tous les chemins terminaux — annulée et clôturée — terminent le cycle de vie de la commande. »

Votre mission est de modéliser ce cycle de vie en UML en faisant figurer les conditions qui rendent certaines transitions valides (ou non), et les actions automatiques qui accompagnent certaines transitions — en veillant à ce que les transitions partageant un même événement source soient mutuellement exclusives.

---

### Exercice 05 — Auto-Transitions et Cycle de Vie d'une Notification

Yasmine, responsable de la plateforme Notifications chez NovaTrade, vous reçoit pour modéliser le cycle de vie d'un objet `Notification` qui circule dans tous les canaux (courriel, SMS, push).

> « C'est un objet techniquement simple mais qui a un comportement particulier sur les échecs de livraison, et c'est ça qu'il faut bien capturer. Donc reprenons depuis le début. Quand on crée une notification, elle est en attente. Au moment où on tente de l'envoyer — événement `envoyer` — deux issues : soit la livraison réussit (`livraisonReussie`) et la notification passe en livrée, soit elle échoue (`livraisonEchouee`). »
>
> « En cas d'échec, c'est là que ça se complique : on garde la notification dans un état "échouée". Dès l'entrée dans cet état, deux actions partent automatiquement — on log l'échec (`journaliserEchec`) et on incrémente un compteur de tentatives (`incrementerCompteurTentatives`). Ensuite on ré-essaie via un événement `reessayer`. Trois cas possibles. Si le réessai réussit (`reessaiReussi`), la notification passe en livrée. Si le réessai échoue mais qu'il nous reste des tentatives autorisées (`reessaiEchoue ET tentativesRestantes`), la notification reste dans l'état "échouée" — et c'est important : elle y *retourne* en quelque sorte, parce qu'on veut que le compteur de tentatives s'incrémente à nouveau et qu'on planifie le prochain réessai (`planifierReessai` comme action sur la transition de retour). Si le réessai échoue et qu'on a épuisé toutes nos tentatives (`reessaiEchoue ET plusDeTentatives`), la notification bascule en échec permanent. »
>
> « Côté chemin nominal : une fois livrée, on attend que le destinataire l'acquitte — événement `acquitter` — et on passe à acquittée. Les deux états terminaux possibles, acquittée et échec permanent, mènent tous les deux à l'archivage (`archiver`) et à la fin du cycle de vie. »

Votre mission est de modéliser ce cycle de vie en respectant la sémantique du retour-sur-soi de l'état "échouée" : on doit pouvoir voir que ré-entrer dans l'état déclenche de nouveau le log et l'incrément du compteur. Veiller également à ce que les trois transitions sortantes de "échouée" sur l'événement `reessayer` soient mutuellement exclusives.

---

### Exercice 06 — États Composites pour le Bon de Commande

Hélène, Directrice des Achats chez NovaTrade — vous l'avez peut-être déjà rencontrée sur le module BPMN d'approvisionnement — vous demande maintenant un diagramme d'état pour l'objet `BonCommande` côté application.

> « Tu te souviens de notre processus d'approvisionnement ? On va se concentrer sur l'objet bon de commande lui-même. Au départ, c'est simple : il est créé en brouillon, et dès qu'il est soumis (événement `soumis`), il passe en revue. Là où ça se complique, c'est que la "revue" elle-même est un mini-processus à part entière, avec trois étapes successives : d'abord l'équipe Finance vérifie le BC sous l'angle budgétaire (`VerificationFinance`), ensuite l'équipe Conformité vérifie sous l'angle KYC et conformité réglementaire (`VerificationConformite`), enfin un dernier étage d'approbation finale tranche (`ApprobationFinale`). Chacune de ces étapes a son propre événement de complétion qui fait avancer à la suivante (`financeApprouvee`, `conformitePassee`, `approbationAccordee`). »
>
> « Le truc important, c'est que je ne veux pas un diagramme avec sept états plats au même niveau qui mélange revue et après-revue. Je veux qu'on voie clairement que la revue est *une seule chose vue de l'extérieur*, mais qu'elle a sa propre dynamique interne — un peu comme une boîte qu'on peut ouvrir si on veut voir le détail. »
>
> « Maintenant les transitions qui sortent de cette boîte. Si la revue se conclut par une approbation (`approuve`), le BC passe à actif, et à ce moment-là on notifie le fournisseur (`notifierFournisseur`) — c'est automatique, dès qu'on entre dans l'état actif. Si en revanche le BC est rejeté (`rejete`) — et le rejet peut arriver à *n'importe quelle* étape interne de la revue, pas seulement à la dernière — alors le BC bascule en rejeté. Au passage, peu importe pourquoi on quitte l'état de revue (approbation ou rejet), on doit journaliser le temps total que la revue a pris (`journaliserDureeRevue`), pour nos statistiques. »
>
> « Une fois actif, deux suites possibles. Soit on reçoit les marchandises (`marchandisesRecues`), et le BC passe en marchandises-reçues. Soit on annule (`annule`), mais uniquement si aucune livraison n'est en attente (`aucuneLivraisonEnAttente`) — c'est une garde métier importante, on ne peut pas annuler un BC dont des marchandises sont déjà parties chez nous. Depuis marchandises-reçues, quand la facture associée est rapprochée (`factureRapprochee`), le BC passe à clôture. Tous les états terminaux — rejeté, annulé, clôture — mettent fin au cycle de vie du BC. »

Votre mission est de modéliser ce cycle de vie en mettant à profit la notion d'état composite (*Composite State*) UML pour représenter la phase de revue, et en faisant ressortir le fait que certaines transitions partent de la frontière du composite plutôt que d'un sous-état particulier.

---

## 🔴 Niveau Avancé

### Exercice 07 — Pseudo-état Historique pour un Workflow Suspendu

Karim, responsable du processus KYC (*Know Your Customer*) et de l'intégration des nouveaux fournisseurs chez NovaTrade, vous expose un casse-tête de modélisation qu'il rencontre en pratique.

> « Notre processus d'intégration fournisseur (`IntegrationFournisseur`) est globalement linéaire : un nouveau fournisseur passe par une phase de vérification où on collecte d'abord ses documents (`CollecteDocuments`), puis on lance la vérification KYC (`VerificationKYC`), puis on signe le contrat (`SignatureContrat`). Une fois la vérification entièrement terminée, le fournisseur devient actif dans notre système. Jusque-là rien d'extraordinaire — c'est trois sous-étapes successives au sein d'une grande étape "vérification". »
>
> « Là où c'est intéressant, c'est qu'à *n'importe quel moment* pendant la vérification, un superviseur peut décider de mettre l'intégration en attente (`superviseurMetEnAttente`) — typiquement parce qu'il manque un document, parce qu'on a un doute juridique, parce qu'il y a une investigation à faire. Quand il met en attente, on bascule dans un état suspendu. Plus tard, quand le superviseur reprend l'intégration (`superviseurReprend`), elle ne doit *pas* repartir depuis la collecte des documents — elle doit reprendre exactement là où elle en était au moment de la suspension. Si on était en pleine vérification KYC, on reprend en KYC. Si on était à la signature de contrat, on reprend là. Recommencer depuis zéro à chaque suspension serait un cauchemar opérationnel — on perdrait à chaque fois plusieurs jours de travail. »
>
> « Bien sûr, à la toute première fois où un fournisseur entre en vérification, il n'y a pas d'historique à reprendre — donc dans ce cas-là, on démarre par la collecte des documents, comme prévu. Et au moment où on entre dans la phase de vérification, peu importe le sous-état, on doit assigner automatiquement un responsable d'intégration interne (`assignerResponsableIntegration`) — chez nous, c'est un junior qui suit le dossier. »
>
> « Une fois actif, le fournisseur peut aussi être désactivé temporairement (`desactiver`, par exemple s'il y a une rupture commerciale momentanée), depuis un état inactif il peut être réactivé (`reactiver`), et à tout moment depuis actif ou inactif on peut le bloquer définitivement (`bloquerDefinitivement`) — c'est notre porte de sortie quand un fournisseur s'avère problématique. Une fois bloqué, c'est définitif, le cycle de vie est terminé. »

Votre mission est de modéliser ce cycle de vie en utilisant le mécanisme UML qui permet à une machine à états de se souvenir du sous-état où elle se trouvait quand on revient dans un composite — sans oublier de gérer le cas de la première entrée, où aucun historique n'existe encore.

---

### Exercice 08 — Pseudo-état Choix et Diagnostic d'une Machine à États Défectueuse

Romain, Responsable du Risque Crédit chez NovaTrade, vous demande de modéliser le cycle de vie d'une demande de crédit (`DemandeCredit`).

> « Une demande de crédit, ça commence en brouillon. Une fois soumise par le commercial (`soumise`), on bascule en évaluation. Pendant cette phase, on lance notre vérification automatique de crédit (`executerVerificationCredit`) — c'est notre service interne qui calcule un score à partir de l'historique du client. C'est une action qui s'exécute pendant la transition vers le résultat, pas pendant un état stable. »
>
> « Une fois la vérification terminée, on a un score, et c'est ce score qui dicte la suite. Si le score est très bon — disons 700 ou plus — la demande est directement approuvée. Si le score est moyen — entre 500 et 700 — on accorde une approbation conditionnelle (avec garanties supplémentaires). Si le score est en dessous de 500, la demande est refusée. C'est un branchement à trois sorties qui n'est pas vraiment un état — c'est juste un point de décision instantané, sans entry, sans exit, sans nom métier. La demande ne reste pas dans un état "en train de décider", elle bascule directement vers l'une des trois suites. »
>
> « Côté approuvée et approbation conditionnelle, le commercial active la demande quand le client signe (`activee`), et on passe à active. Une demande active se clôture en fin de contrat (`cloturee`). Côté refusée, le commercial peut soit la décliner définitivement (`declinee`) — fin du cycle — soit la contester (`contestee`), ce qui la renvoie en évaluation pour une seconde revue (parfois on découvre une donnée qui change la donne, ça arrive). »

#### Contexte & Scénario — Partie B

Khalid, analyste junior dans l'équipe support, vient ensuite vous voir avec un brouillon de diagramme d'état pour l'objet `TicketSupport` qu'il a produit. Il vous le décrit oralement :

> « Bon alors, j'ai quatre états : ouvert, en cours, résolu, clôturé. Quand un agent est assigné, on passe d'ouvert à en-cours. Quand l'agent fournit une solution, on passe en résolu. Si le client confirme, on passe en clôturé. Si le client rouvre le ticket, depuis résolu on revient en en-cours. J'ai aussi mis une transition d'ouvert vers clôturé directement, sans étiquette d'événement — pour le cas où on archive un ticket vide. Dans l'état en-cours j'ai mis une action d'entrée qui envoie un courriel de confirmation, parce qu'on veut que le client soit notifié quand on prend en charge. Ah et j'ai pas mis de pseudo-état initial parce que j'ai supposé qu'on partait toujours d'ouvert. Et après clôturé je n'ai pas mis d'état final non plus. Pas mis de gardes nulle part. Voilà, qu'est-ce que tu en penses ? »

Votre mission est double : (1) produire le diagramme `DemandeCredit` de la Partie A en utilisant le mécanisme UML adapté au branchement à trois sorties que Romain décrit comme « un point de décision instantané, sans nom métier » ; (2) produire pour la Partie B une analyse des écarts (au moins cinq points distincts) sur le brouillon de Khalid avec, pour chacun, l'explication de l'erreur et la correction proposée, puis fournir un diagramme `TicketSupport` corrigé qui résout chaque écart identifié.

---

### Exercice 09 — Cycle de Vie Complet de la Facture avec Cohérence Inter-Diagrammes

Vincent, architecte principal chez NovaTrade, vous reçoit pour la **dernière itération** du cycle de vie de la classe `Facture`. Il a passé en revue les diagrammes des Exercices 02 et 03, et a quelques exigences supplémentaires avant que ce diagramme devienne le document de référence pour l'équipe de développement.

> « Tout ce qu'on a déjà — les six états (brouillon, envoyée, en-attente-de-paiement, en-retard, payée, annulée), tous les événements et toutes les actions internes — ça reste vrai. On va juste affiner deux endroits, et je veux qu'on vérifie la cohérence avec les autres diagrammes UML qu'on a déjà produits. »
>
> « Premier raffinement : l'état d'attente de paiement doit être plus granulaire. Aujourd'hui c'est un seul état, mais en pratique on distingue deux situations — soit on attend encore le tout premier paiement (rien n'a été payé), soit le client a fait un paiement partiel et on attend le complément. Un paiement partiel doit nous faire passer du premier sous-état au second. Un paiement complet, depuis l'un ou l'autre des deux sous-états, doit faire sortir la facture vers payée. C'est typiquement un état composite avec deux sous-états internes. »
>
> « Deuxième raffinement : quand la date d'échéance est dépassée, ce qui se passe ensuite dépend du palier du client. Pour nos clients premium (`client.estPremium`), on accorde un délai de grâce — la facture passe en retard, on envoie une relance, et on espère qu'ils paient. Pour les autres clients, pas de délai de grâce : on bascule directement en mise-en-demeure-immédiate, et de là c'est transmission au juridique (`transmissionJuridique`), fin du cycle de vie côté Finance. C'est un branchement instantané sur la condition `client.estPremium`, sans état tampon. »
>
> « Et puis je veux une vérification systématique : pour chaque événement et chaque action interne du diagramme d'état, est-ce qu'il existe une opération correspondante dans le diagramme de classes UML Class ? Est-ce qu'il existe un message correspondant dans un diagramme de séquence UML Sequence ? Si oui, est-ce que les noms s'alignent ? Si non, c'est qu'on a un trou ou une incohérence à résoudre avant que mes équipes ne se mettent à coder. Je veux un tableau Markdown qui croise tout ça, et un avertissement explicite sur les divergences avec une recommandation de résolution pour chacune. »

Votre mission est de produire ce diagramme d'état final et faisant autorité, en intégrant les deux raffinements demandés par Vincent et en préservant tous les acquis des Exercices 02 et 03, accompagné du tableau de cohérence inter-diagrammes (au moins huit lignes) et des recommandations de résolution dans un callout `[!warning]`.