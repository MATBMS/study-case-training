# UML — Diagramme de Séquence — Énoncés des Exercices

Tous les exercices se déroulent au sein de la même entreprise fictive — **NovaTrade**, société de négoce B2B. Vous serez chaque fois reçu par un membre de l'équipe NovaTrade qui vous décrira l'interaction qu'il faut modéliser ; à vous d'écouter, d'identifier les lignes de vie (*lifelines*) en jeu, l'ordre des messages, leur synchronisme et les éventuels fragments combinés (*Combined Fragments*) à utiliser.

> [!important] Le Diagramme de Séquence dans cette formation
> Comme le Diagramme d'État, le Diagramme de Séquence est un diagramme **spécialisé** — on l'utilise lorsqu'on a besoin de spécifier *l'ordre temporel et le synchronisme* d'une interaction entre plusieurs participants (typiquement à travers une frontière de système ou pour une orchestration architecturale délicate). On n'en produit pas pour tous les cas d'utilisation ; on en produit pour les cas où l'ordre, la concurrence ou la création/destruction d'objets sont des sujets en eux-mêmes.

---

## 🟢 Niveau Débutant

### Exercice 01 — Valider une Facture (Trois Lignes de Vie)

Léa, responsable Finance chez NovaTrade, vous reçoit avec Hugo, le nouveau développeur fraîchement arrivé dans l'équipe. Hugo doit comprendre comment fonctionne la validation des factures fournisseurs — qui parle à qui dans le code — pour pouvoir intervenir dessus.

> « C'est très simple. Quand un agent finance reçoit une facture, il l'ouvre dans notre application interne, et il clique sur "valider". Côté code, ça part vers notre service de gestion des factures (`ServiceFacture`), qui a besoin de comparer la facture au bon de commande qu'on a émis chez nous — pour vérifier que les montants, les quantités, les références correspondent. Pour récupérer ce bon de commande, le service va chercher dans notre base de données (`BaseDeDonnees`). La base lui renvoie le bon de commande, le service compare, et il renvoie à l'agent un résultat de validation. »
>
> « Donc trois acteurs côté technique : l'agent finance qui déclenche tout (`AgentFinance`), le service métier qui orchestre, et la base qui stocke. C'est une interaction synchrone classique — chacun attend la réponse de celui qu'il a appelé avant de continuer. Côté méthodes, l'agent appelle `validerFacture(idFacture)`, le service appelle ensuite `recupererBonCommande(idBC)` sur la base, qui retourne `bonCommande`, et le service finit par retourner `resultatValidation` à l'agent. »

Votre mission est de modéliser cette interaction sous forme d'un diagramme de séquence (*Sequence Diagram*) UML, en représentant les trois lignes de vie (*lifelines*), les messages échangés dans l'ordre, les retours étiquetés, et les barres d'activation (*activation bars*) sur les lignes de vie système.

---

### Exercice 02 — Passer une Commande (Quatre Lignes de Vie)

Bruno, lead développeur dans l'équipe e-commerce de NovaTrade, vous demande de documenter le flux complet de prise de commande côté code, pour que ses devs aient une référence visuelle.

> « Quand un client passe une commande sur notre site, voilà ce qui se passe en arrière-plan. Le client clique sur "valider la commande" depuis son panier dans l'application web (`AppWeb`). L'AppWeb prend le panier et appelle notre service de gestion des commandes (`ServiceCommande`) avec une méthode `creerCommande(panier)`. Le ServiceCommande, lui, crée un enregistrement dans notre base de données via `insererCommande(donneesCommande)`. La base lui renvoie l'identifiant de la commande nouvellement créée (`idCommande`). Le ServiceCommande renvoie alors la commande complète à l'AppWeb. Et enfin, l'AppWeb affiche au client une confirmation. »
>
> « Tous les appels côté serveur sont synchrones — chacun attend la réponse de l'étape précédente avant de continuer. Et pour que ça serve vraiment de contrat d'API à mes devs, je veux que chaque message porte ses arguments, et que chaque retour soit étiqueté avec ce qui est retourné. »
>
> « Petite précision : le client n'est pas une ligne de vie système — c'est un acteur. Il déclenche le flux et reçoit la confirmation, mais il ne bloque pas son environnement comme un service le ferait. »

Votre mission est de modéliser cette interaction avec quatre lignes de vie (un acteur et trois participants), les six messages dans l'ordre, et les barres d'activation correctement empilées pour montrer la profondeur d'appel.

---

### Exercice 03 — Notification Asynchrone et Auto-Message

Bruno revient avec une nouvelle interaction à modéliser, cette fois sur ce qui se passe **après** qu'une commande a été confirmée.

> « Là où ça se complique un peu, c'est sur les actions post-confirmation. Quand l'AppWeb appelle `confirmerCommande(idCommande)` sur le ServiceCommande, le service doit faire trois choses, et ces trois choses ne se comportent pas pareil. »
>
> « Premièrement, le service doit logger l'événement de confirmation en interne — c'est un appel à sa propre méthode `journaliserConfirmation(idCommande)`. Pas un appel externe, juste une méthode interne du service. C'est ce qu'on appelle un auto-message (*self-message*) en UML. »
>
> « Deuxièmement, on doit envoyer un courriel de confirmation au client via notre service email (`ServiceEmail`). Et là, attention : on ne veut **surtout pas** que le ServiceCommande attende la réponse du ServiceEmail. L'envoi de courriel est lent, parfois plusieurs secondes, et on ne va pas bloquer toute la chaîne pour ça. C'est ce qu'on appelle un envoi *fire-and-forget* — sans suivi. Le ServiceCommande balance le message et continue immédiatement. Du coup, pas de retour, pas de barre d'activation côté ServiceEmail dans ce diagramme — le ServiceEmail traite ailleurs, dans son coin. »
>
> « Troisièmement, on met à jour le statut de la commande en base à "Confirmee" via `mettreAJourStatut(idCommande, "Confirmee")`. Là par contre c'est synchrone — on attend la confirmation de la base, qui nous répond `ok`. Et finalement, le ServiceCommande retourne `ok` à l'AppWeb. »
>
> « Pour aider Hugo et les autres juniors qui vont relire le diagramme, j'aimerais bien qu'on ajoute en dessous une note expliquant en deux-trois phrases la différence entre un appel synchrone et un appel asynchrone du point de vue de l'appelant. »

Votre mission est de modéliser cette interaction en distinguant correctement les trois types de messages (auto-message, asynchrone fire-and-forget, synchrone), avec les barres d'activation au bon endroit, et d'accompagner le diagramme d'une note synthétique sur sync vs. async.

---

## 🟡 Niveau Intermédiaire

### Exercice 04 — Flux de Paiement avec Embranchements

Léna, product manager du module Paiement chez NovaTrade, vous décrit le flux complet de traitement d'un paiement, qui comporte plusieurs branches selon le résultat.

> « Quand l'AppWeb a fini de monter le panier, elle appelle notre passerelle de paiement (`PasserellePaiement`) avec `traiterPaiement(montant)`. À partir de là, deux scénarios mutuellement exclusifs : soit le paiement réussit, soit il échoue. C'est un vrai branchement — on emprunte forcément l'un ou l'autre, jamais les deux. »
>
> « **Si le paiement réussit**, la passerelle renvoie une confirmation de paiement à l'AppWeb. Ensuite, et c'est là le morceau délicat : *si* le client est dans notre programme premium (`client.estPremium`), on doit lui ajouter des points de fidélité — l'AppWeb appelle alors `ajouterPointsFidelite(idClient, montant)` sur le `ServiceFidelite`, qui renvoie le nouveau solde de points. Pour les clients non-premium, on saute simplement cette étape — pas de branche alternative à modéliser, on ne fait juste rien. C'est un cas optionnel, pas une alternative à deux faces. Que le client soit premium ou pas, on enchaîne ensuite avec `confirmerCommande(idCommande)` sur le ServiceCommande, et on renvoie la confirmation au client. »
>
> « **Si le paiement échoue**, la passerelle renvoie une erreur de paiement à l'AppWeb. Du coup l'AppWeb appelle `annulerCommande(idCommande)` sur le ServiceCommande, et renvoie un message d'échec au client. »
>
> « Le truc important : la branche premium est *imbriquée* dans le scénario "paiement réussi". On ne peut pas se retrouver à donner des points de fidélité sur un paiement qui a échoué, ce serait absurde. »

Votre mission est de modéliser ce flux en utilisant deux fragments combinés (*Combined Fragments*) UML — l'un pour le branchement principal réussi/échoué, l'autre pour la condition optionnelle premium imbriquée — en choisissant pour chacun le bon type de fragment (`alt`, `opt`, autre) selon la sémantique décrite par Léna.

---

### Exercice 05 — Réservation d'Inventaire avec Boucle et Référence

Antoine, responsable Order Management chez NovaTrade — déjà rencontré sur le diagramme d'état — revient avec un détail technique sur la création de commande.

> « Quand l'AppWeb appelle `creerCommande(panier)` sur le ServiceCommande, ce qu'on n'avait pas regardé dans le diagramme précédent, c'est que le service doit en réalité réserver le stock pour *chaque* article du panier. Si je commande trois produits différents, le ServiceCommande va parler à notre service d'inventaire (`Inventaire`) trois fois : une fois par article. Pour chaque article, il appelle `reserverArticle(idArticle, qte)` et reçoit en retour un `idReservation`. C'est une boucle, vraiment — un appel par article, et chaque appel attend sa réponse avant de passer au suivant. »
>
> « Une fois tous les articles réservés, le ServiceCommande doit ensuite déclencher tout le traitement du paiement — mais ce traitement, on l'a déjà décrit dans un autre diagramme de séquence à part entière (celui de l'Exercice 04). Je ne veux pas qu'on le redessine entièrement ici, ça polluerait la lecture. Je veux juste qu'on signale au lecteur "à ce moment-là, va voir le diagramme Traiter le Paiement" et qu'on continue. C'est exactement le rôle du fragment `ref` en UML. »
>
> « Après tout ça, le ServiceCommande renvoie la commande complète à l'AppWeb. »
>
> « Et puisque le `ref` est un mécanisme important mais pas évident pour mes juniors, j'aimerais qu'on accompagne le diagramme d'une note expliquant quand utiliser `ref` plutôt que de tout copier en ligne. »

Votre mission est de modéliser cette interaction en utilisant un fragment de boucle pour la réservation des articles, et un fragment de référence pour déléguer le traitement du paiement à un autre diagramme — en veillant à ce que la barre d'activation de l'inventaire couvre bien une seule itération à la fois, et en accompagnant le diagramme d'une note synthétique sur l'usage de `ref`.

---

### Exercice 06 — Création et Destruction d'une Facture

Vincent, architecte principal chez NovaTrade, vous reçoit pour modéliser le cycle de vie complet d'un objet `Facture` à travers une interaction.

> « Là on va sortir un peu du cadre habituel. Dans la plupart des diagrammes de séquence, les participants existent depuis le début du diagramme — ils sont déclarés en haut, et c'est tout. Mais dans cette interaction-ci, l'objet `Facture` est *créé en cours de route*, utilisé, puis détruit avant la fin. C'est important de le montrer parce que ça change la manière dont on lit le diagramme. »
>
> « Voici le scénario. L'AppWeb appelle `confirmerCommande(idCommande)` sur le ServiceCommande. À ce moment-là, le ServiceCommande crée un nouvel objet `Facture` en mémoire — c'est ce qu'on indique avec le stéréotype `<<create>>` en UML. La facture n'existait pas avant cet instant ; elle apparaît en cours de diagramme. Une fois créée, le ServiceCommande lui définit son montant via `definirMontant(montantTotal)`, qui retourne `ok`. Puis il appelle `genererPDF()` qui renvoie le fichier PDF. »
>
> « Le service envoie ensuite ce PDF au ServiceEmail via `envoyerFacture(fichierPDF)` — et là, comme on l'a vu à l'Exercice 03, c'est asynchrone, sans retour. »
>
> « Ensuite — et là on saute en avant dans le temps, le paiement a eu lieu — le ServiceCommande appelle `marquerCommePayee()` sur la facture, qui retourne `ok`. Et juste après, le ServiceCommande détruit l'objet facture en mémoire avec `<<destroy>>`. La ligne de vie de la facture s'arrête là — plus aucun message ne peut lui être envoyé après. »
>
> « Pour finir, le ServiceCommande renvoie une `referenceFacture` à l'AppWeb. Notez bien que c'est une *référence* — un identifiant, peut-être une URL d'archive — pas l'objet lui-même qui vient d'être détruit. »

Votre mission est de modéliser cette interaction en faisant apparaître la création et la destruction explicites de la ligne de vie `Facture`, sans la pré-déclarer en haut du diagramme — la création et la fin de vie de l'objet sont l'objet même de cet exercice.

---

## 🔴 Niveau Avancé

### Exercice 07 — Préparation Parallèle

Pauline, Directrice des Opérations chez NovaTrade — déjà rencontrée sur le diagramme BPMN Order-to-Cash — vous demande de modéliser la phase de préparation côté code, qui implique plusieurs services en parallèle.

> « Tu te souviens de ce qu'on disait en BPMN : quand une commande est confirmée, trois choses se passent en parallèle. Côté code, c'est le ServiceCommande qui orchestre les trois en concurrence. »
>
> « **Première branche : la préparation d'expédition**. Le ServiceCommande appelle `preparerExpedition(idCommande)` sur notre service entrepôt (`ServiceEntrepot`). C'est un appel synchrone parce qu'on a besoin de l'identifiant d'expédition (`idExpedition`) en retour pour la suite. »
>
> « **Deuxième branche : la génération de facture**. Le ServiceCommande appelle `genererFacture(idCommande)` sur notre service finance (`ServiceFinance`). Synchrone aussi, on attend l'`idFacture`. »
>
> « **Troisième branche : la notification au client**. Le ServiceCommande envoie un message au `ServiceNotification` via `envoyerCommandeConfirmee(idClient)`. Et là, attention, comme l'envoi de courriel : c'est asynchrone, sans suivi. On ne bloque pas la préparation pour attendre que la notification soit délivrée — pas de retour, pas de barre d'activation sur le ServiceNotification. »
>
> « Le point critique : ces trois branches s'exécutent **vraiment en parallèle**. Ce n'est pas "soit l'une soit l'autre" comme un branchement — toutes les trois partent en même temps. Le fragment UML qui modélise ça s'appelle `par` (parallèle). »
>
> « Maintenant, après le bloc parallèle, le ServiceCommande doit attendre que les **deux branches synchrones** (Entrepôt et Finance) soient terminées pour pouvoir continuer — il a besoin des deux retours. La branche notification, asynchrone, n'entre pas dans cette attente. Une fois Entrepôt et Finance revenus, le ServiceCommande appelle sa propre méthode `marquerCommandePreparee(idCommande)` (auto-message), puis renvoie `resumePreparation` à l'AppWeb. »
>
> « Pour aider mon équipe à lire le diagramme, j'aimerais qu'on ajoute une note explicative sur l'absence de barre d'activation côté ServiceNotification — c'est le genre de subtilité qui peut sembler être une erreur d'oubli si on ne l'explique pas. »

Votre mission est de modéliser cette orchestration concurrente avec un fragment combiné à trois opérandes pour les trois branches parallèles, l'auto-message de marquage placé au bon endroit dans la chronologie, et la note d'accompagnement sur le ServiceNotification.

---

### Exercice 08 — Processus de Relance avec Boucle et Sortie Anticipée

Patricia, Directrice Financière de NovaTrade — déjà rencontrée sur le diagramme BPMN de gestion d'exceptions — vous demande de modéliser le processus de relance (*dunning*) côté code.

> « Voilà le scénario. Quand une facture est en retard, on lance un processus de relance qui peut envoyer jusqu'à **trois rappels** au client, espacés de 7 jours. C'est une boucle bornée. À chaque itération, le ServiceFinance fait deux choses : il envoie un rappel via le ServiceNotification (`envoyerRappelRelance(idFacture, tentative)` qui retourne `envoye`), puis il vérifie auprès du ServicePaiement si le paiement a été reçu entre-temps (`verifierPaiementRecu(idFacture)` qui retourne un `statutPaiement`). »
>
> « Maintenant, le truc clé : si à un moment quelconque pendant ces trois cycles le paiement est reçu, on doit **sortir immédiatement de la boucle** — pas finir l'itération courante, pas faire les itérations restantes. Et avant de sortir, on appelle `marquerCommePayee(idFacture)` sur le ServiceFacture pour mettre à jour le statut. C'est une sortie anticipée conditionnelle — en UML il y a un fragment dédié pour ça, qui s'appelle `break`. »
>
> « **Si la boucle se termine naturellement** — c'est-à-dire qu'on a envoyé les trois rappels et qu'aucun n'a abouti à un paiement — alors on escalade le dossier au juridique. Le ServiceFinance appelle `escalaterCompte(idClient)` sur le ServiceJuridique, qui crée un dossier juridique et renvoie l'identifiant. Cet appel d'escalade se trouve **après** la boucle — il ne fait pas partie de la boucle, c'est ce qui se passe quand la boucle est sortie sans break. »
>
> « Et finalement, le ServiceFinance renvoie un `resultatRelance` à l'AppWeb. »

Votre mission est de modéliser ce processus en utilisant un fragment de boucle bornée contenant un fragment de sortie anticipée, et en plaçant correctement l'appel d'escalade en dehors de la boucle pour qu'il ne s'exécute que dans le cas du non-paiement après les trois tentatives.

---

### Exercice 09 — Passer une Commande Complète avec Cohérence Inter-Diagrammes

Vincent, architecte principal chez NovaTrade, vous reçoit pour la **dernière itération** : produire le diagramme de séquence canonique du cas d'utilisation `Passer une commande` qui servira de référence à toute l'équipe d'implémentation, avec une vérification de cohérence avec les autres diagrammes UML déjà produits.

> « C'est le diagramme le plus important de toute notre documentation, parce que c'est lui qui va être lu en parallèle du diagramme de classes (UML Class) et du diagramme d'état (UML State) pour valider la conception complète de l'OMS NovaTrade. Il doit couvrir l'interaction de bout en bout, depuis le moment où le client soumet sa commande jusqu'au démarrage de la préparation, en réutilisant tout ce qu'on a vu dans les exercices précédents. »
>
> « Le déroulé. Le client soumet sa commande à l'AppWeb. L'AppWeb appelle le ServiceCommande pour créer la commande. Le ServiceCommande boucle sur les articles du panier pour réserver le stock auprès de l'Inventaire (comme à l'Exercice 05). Une fois la commande montée, le ServiceCommande la renvoie à l'AppWeb. L'AppWeb lance alors le traitement du paiement via la PasserellePaiement. »
>
> « À partir de là, on retombe sur l'embranchement de l'Exercice 04 : alternative paiement réussi / paiement échoué. Sur la branche réussie, on a en plus l'option des points de fidélité pour les clients premium, l'appel à `confirmerCommande` sur le ServiceCommande, l'envoi asynchrone du courriel de confirmation au client, et — point nouveau — un appel `ref [Préparer la Commande]` qui délègue à un autre diagramme de séquence (celui de l'Exercice 07). Sur la branche échouée, on annule la commande et on renvoie un message d'échec au client. »
>
> « Maintenant l'exigence supplémentaire, et c'est elle qui justifie cet exercice : à la sortie du diagramme, je veux un **tableau de cohérence inter-diagrammes** en Markdown qui croise chaque message du diagramme de séquence avec deux choses — l'opération correspondante dans le diagramme de classes UML Class, et la transition d'état correspondante dans le diagramme d'état UML State. Quatre colonnes : Message, Opération de Classe, Transition d'État, Cohérent ? Couvrir au moins dix messages. Pour chaque ligne où il y a un écart — nom différent, signature différente, élément manquant dans un autre diagramme — je veux une recommandation de résolution dans un callout `[!warning]`. C'est ce tableau qui va déclencher les corrections nécessaires avant qu'on parte en implémentation. »
>
> « Ah, et dernière chose : ajouter une note de deux phrases qui explique comment ce diagramme implémente le cas d'utilisation `Passer une commande` de UML Use Case Exercices — en référençant les numéros d'étape du flux principal pour la traçabilité. »

Votre mission est de produire ce diagramme de séquence canonique, le tableau de cohérence inter-diagrammes (au moins dix lignes), le callout `[!warning]` listant les divergences avec leurs résolutions, et la note de traçabilité vers le cas d'utilisation.