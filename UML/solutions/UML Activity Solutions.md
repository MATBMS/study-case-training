# Exercices — Diagramme d'Activité UML
## 🟢 Niveau Débutant
### Exercice 01 — Soumission d'une Note de Frais

```mermaid
flowchart TB
    subgraph EMP ["👤 Employé"]
        I(("●")) --> A["Saisir le formulaire<br/>de note de frais"]
        A --> B["Joindre les<br/>justificatifs"]
        B --> C["Cliquer<br/><i>Soumettre</i>"]
        R["Voir l'accusé<br/>de réception à l'écran"] --> END(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        S1["Valider les champs<br/>obligatoires"] --> S2["Calculer le<br/>montant total"]
        S2 --> S3["Enregistrer la note<br/>(statut: Soumise)"]
        S3 --> S4["Envoyer un accusé<br/>de réception"]
    end
    C --> S1
    S4 --> R
```

### Exercice 02 — Validation d'une Facture avec Décision

```mermaid
flowchart TB
    subgraph CPT ["👤 Comptable"]
        I(("●")) --> A["Saisir les données<br/>de la facture"]
        A --> B["Cliquer<br/><i>Soumettre</i>"]
        OK["Voir l'accusé<br/>à l'écran"] --> END(("◉"))
        KO["Voir le message de rejet<br/>et corriger"] --> FF(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        S1["Valider le format<br/>(TVA, IBAN, montant)"] --> D{" "}
        D -- "[valide]" --> S2["Enregistrer la facture<br/>(statut: Reçue)"]
        S2 --> S3["Générer un accusé<br/>de réception"]
        D -- "[invalide]" --> S4["Préparer le message<br/>de rejet"]
    end
    B --> S1
    S3 --> OK
    S4 --> KO
```

### Exercice 03 — Vérification de Stock à Trois Voies

```mermaid
flowchart TB
    subgraph CLT ["👤 Client"]
        I(("●")) --> A["Soumettre<br/>la commande"]
        OK["Voir la confirmation<br/>de commande"] --> END(("◉"))
        KO["Voir le message<br/>d'indisponibilité"] --> END2(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        S1["Vérifier la disponibilité<br/>du stock"] --> D{" "}
        D -- "[entièrement en stock]" --> S2a["Réserver la<br/>quantité totale"]
        D -- "[partiellement en stock]" --> S2b["Réserver la<br/>quantité disponible"]
        S2b --> S2c["Créer une commande<br/>en attente"]
        D -- "[rupture de stock]" --> S3["Marquer comme<br/>indisponible"]
        S2a --> M{" "}
        S2c --> M
        M --> S4["Confirmer la commande"]
    end
    A --> S1
    S4 --> OK
    S3 --> KO
```

## 🟡 Niveau Intermédiaire
### Exercice 04 — Approbation de Facture Multi-Acteurs

```mermaid
flowchart TB
    subgraph CPT ["👤 Comptable"]
        I(("●")) --> A["Saisir<br/>la facture"]
        A --> B["Cliquer<br/><i>Soumettre</i>"]
        REJ["Voir le rejet<br/>et corriger"] --> FF(("⊗"))
        FIN["Voir la confirmation<br/>de paiement"] --> END(("◉"))
    end
    subgraph CTR ["👤 Contrôleur"]
        CTR1["Examiner la facture"] --> CTR2["Cliquer<br/><i>Approuver</i>"]
    end
    subgraph MGR ["👤 Manager"]
        MGR1["Examiner la facture"] --> MGR2["Cliquer<br/><i>Approuver</i>"]
    end
    subgraph SI ["⚙️ Système d'Information"]
        S1["Valider le format"] --> D1{" "}
        D1 -- "[valide]" --> D2{" "}
        D2 -- "[< 5 000 €]" --> S2["Affecter au<br/>Contrôleur"]
        D2 -- "[≥ 5 000 €]" --> S3["Affecter au<br/>Manager"]
        M{" "} --> S4["Enregistrer<br/>l'approbation"]
        S4 --> S5["Exécuter le paiement<br/>via l'ERP"]
        S5 --> S6["Notifier<br/>le Comptable"]
    end
    B --> S1
    D1 -- "[invalide]" --> REJ
    S2 --> CTR1
    S3 --> MGR1
    CTR2 --> M
    MGR2 --> M
    S6 --> FIN
```

### Exercice 05 — Préparation de Commande Parallèle (Fourche/Jointure dans le SI)

```mermaid
flowchart TB
    subgraph REP ["👤 Représentant Commercial"]
        I(("●")) --> A["Sélectionner<br/>la commande"]
        A --> B["Cliquer<br/><i>Confirmer</i>"]
        R["Voir la confirmation<br/>de préparation"] --> END(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        S0["Recevoir la<br/>confirmation"] --> FORK["══ Fourche ══"]
        FORK --> S1a["Créer l'ordre<br/>de prélèvement"]
        FORK --> S2a["Générer le PDF<br/>de facture"]
        S2a --> S2b["Envoyer le<br/>courriel client"]
        FORK --> S3a["Appeler l'API<br/>logistique"]
        S3a --> S3b["Réserver un<br/>transporteur"]
        S1a --> JOIN["══ Jointure ══"]
        S2b --> JOIN
        S3b --> JOIN
        JOIN --> S4["Marquer la commande<br/>(statut: Préparée)"]
        S4 --> S5["Notifier le<br/>Représentant"]
    end
    B --> S0
    S5 --> R
```

### Exercice 06 — Cycle de Vie d'une Facture avec Nœuds d'Objet

```mermaid
flowchart TB
    subgraph CPT ["👤 Comptable"]
        I(("●")) --> A["Saisir<br/>la facture"]
        A --> B["Cliquer<br/><i>Soumettre</i>"]
    end
    subgraph CTR ["👤 Contrôleur"]
        CTR1["Examiner la facture"]
        CTR1 --> D{" "}
        D -- "[approuver]" --> CTR2["Cliquer<br/><i>Approuver</i>"]
        D -- "[rejeter]" --> CTR3["Cliquer<br/><i>Rejeter</i>"]
    end
    subgraph SI ["⚙️ Système d'Information"]
        O1["Facture<br/>[Reçue]"] --> S1["Valider le<br/>format"]
        S1 --> O2["Facture<br/>[Validée]"]
        O2 --> S2["Affecter au<br/>Contrôleur"]
        O3["Facture<br/>[Approuvée]"] --> S3["Exécuter le paiement<br/>via l'ERP"]
        S3 --> O5["Facture<br/>[Payée]"]
        O5 --> END(("◉"))
        O4["Facture<br/>[Rejetée]"] --> FF(("⊗"))
    end
    B --> O1
    S2 --> CTR1
    CTR2 --> O3
    CTR3 --> O4
```

## 🔴 Niveau Avancé
### Exercice 07 — Décomposition Order-to-Cash avec Sous-Activités

**Niveau 1 — Vue d'ensemble**

```mermaid
flowchart TB
    subgraph CLT ["👤 Client"]
        I(("●")) --> A["Soumettre la commande"]
        R["Voir la confirmation finale"] --> END(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        P1["Valider la commande ⌬"] --> P2["Réserver l'inventaire ⌬"]
        P2 --> P3["Traiter le paiement ⌬"]
        P3 --> P4["Préparer la commande ⌬"]
        P4 --> P5["Réconcilier le paiement ⌬"]
        NOTE["📝 Traiter le paiement ⌬ se décompose<br/>en sous-activité Niveau 2"] -.- P3
    end
    A --> P1
    P5 --> R
```

**Niveau 2 — Sous-activité Traiter le paiement**

```mermaid
flowchart TB
    subgraph CLT ["👤 Client"]
        I(("●")) --> A["Saisir les informations<br/>de paiement"]
        A --> B["Cliquer<br/><i>Payer</i>"]
        REJ["Voir le message<br/>d'échec"] --> END1(("◉"))
        REF["Voir le message<br/>de refus"] --> END2(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        S1["Vérifier le crédit<br/>du client"] --> D1{" "}
        D1 -- "[crédit rejeté]" --> S2a["Notifier l'échec"]
        D1 -- "[crédit OK]" --> S2b["Préparer la requête<br/>de paiement"]
        S3["Enregistrer la<br/>transaction"] --> FF(("⊗"))
        S4["Journaliser<br/>l'échec"]
    end
    subgraph PG ["⚙️ Passerelle de Paiement"]
        PG1["Autoriser la transaction"] --> D2{" "}
    end
    B --> S1
    S2a --> REJ
    S2b --> PG1
    D2 -- "[approuvé]" --> S3
    D2 -- "[refusé]" --> S4
    S4 --> REF
```

### Exercice 08 — Préparation de Commande Complète Multi-Acteurs

```mermaid
flowchart TB
    subgraph WH ["👤 Opérateur d'Entrepôt"]
        W1["Consulter<br/>l'ordre"] --> W2["Prélever<br/>physiquement"]
        W2 --> WD{" "}
        WD -- "[disponibles]" --> W3["Emballer"]
        W3 --> W4["Cliquer<br/><i>Confirmer prêt</i>"]
        WD -- "[indisponibles]" --> W5["Cliquer<br/><i>Signaler en attente</i>"]
    end
    subgraph CPT ["👤 Comptable"]
        C1["Consulter la facture"] --> C2["Cliquer<br/><i>Envoyer</i>"]
    end
    subgraph LOG ["👤 Service Logistique"]
        L1["Consulter le<br/>créneau proposé"] --> L2["Cliquer<br/><i>Confirmer</i>"]
    end
    subgraph SI ["⚙️ Système d'Information"]
        O0["Commande<br/>[Confirmée]"] --> S0["Recevoir l'instruction<br/>de préparation"]
        S0 --> FORK["══ Fourche ══"]
        FORK --> S1["Créer l'ordre<br/>de prélèvement"]
        FORK --> S2["Générer le PDF<br/>de facture"]
        FORK --> S3["Réserver un<br/>transporteur (API)"]
        S4["Marquer la commande<br/>en attente"] --> WFF(("⊗"))
        O_PKD["Expédition<br/>[Emballée]"] --> JOIN["══ Jointure ══"]
        S5["Envoyer la facture<br/>au client"] --> O_INV["Facture<br/>[Envoyée]"]
        O_INV --> JOIN
        S6["Planifier<br/>la collecte"] --> JOIN
        JOIN --> S7["Mettre à jour<br/>le statut"]
        S7 --> O_SHIP["Commande<br/>[Expédiée]"]
        O_SHIP --> S8["Envoyer la notification<br/>d'expédition"]
        S8 --> END(("◉"))
    end
    S1 --> W1
    W4 --> O_PKD
    W5 --> S4
    S2 --> C1
    C2 --> S5
    S3 --> L1
    L2 --> S6
```

### Exercice 09 — Activité Order-to-Cash Complète + Cohérence Inter-Diagrammes

**Niveau 1 enrichi avec Nœuds d'Objet**

```mermaid
flowchart TB
    subgraph CLT ["👤 Client"]
        I(("●")) --> A["Soumettre la commande"]
        R["Voir la confirmation finale"] --> END(("◉"))
    end
    subgraph SI ["⚙️ Système d'Information"]
        O1["Commande<br/>[Reçue]"] --> P1["Valider la commande ⌬"]
        P1 --> O2["Commande<br/>[Validée]"]
        O2 --> P2["Réserver l'inventaire ⌬"]
        P2 --> O3["Commande<br/>[Confirmée]"]
        O3 --> P3["Traiter le paiement ⌬"]
        P3 --> O4["Commande<br/>[PaiementAutorisé]"]
        O4 --> P4["Préparer la commande ⌬"]
        P4 --> O5["Commande<br/>[Expédiée]"]
        O5 --> P5["Réconcilier le paiement ⌬"]
        P5 --> O6["Commande<br/>[Clôturée]"]
    end
    A --> O1
    O6 --> R
```

**Niveau 2 — Réconcilier le paiement**

```mermaid
flowchart TB
    subgraph CLT ["👤 Client"]
        PAY["Soumettre<br/>le paiement"]
    end
    subgraph SI ["⚙️ Système d'Information"]
        WAIT["⌚ Accepter le paiement<br/>(Accept Event Action)"] --> D1{" "}
        D1 -- "[reçu à temps]" --> S1["Réconcilier<br/>le paiement"]
        S1 --> S2["Mettre à jour la commande<br/>(Clôturée)"]
        S2 --> O_CL["Commande<br/>[Clôturée]"]
        O_CL --> END(("◉"))
        D1 -- "[en retard]" --> S3["Envoyer une notification<br/>de relance au client"]
        S3 --> WAIT
    end
    subgraph ERP ["⚙️ Système ERP"]
        POST["Comptabiliser l'écriture<br/>de paiement"]
    end
    PAY --> WAIT
    S1 --> POST
```

**Tableau de Cohérence Inter-Diagrammes**

| Action SI | Opération de Classe | Message de Séquence | État du Nœud d'Objet | Cohérent ? |
|---|---|---|---|---|
| Valider la commande | `Commande.valider()` | `creerCommande(panier)` | `Commande [Validée]` | ✅ |
| Vérifier le catalogue produit | `Produit.estDisponible()` | — | — | ⚠️ Pas de message Séquence |
| Réserver l'inventaire | `Inventaire.reserver(article)` | `reserverArticle(idArticle, qte)` | `Commande [Confirmée]` | ✅ |
| Vérifier le crédit | `Client.obtenirLimiteCredit()` | — | — | ⚠️ Manquant en Séquence |
| Autoriser le paiement (Passerelle) | `ProcesseurPaiement.autoriser()` | `traiterPaiement(montant)` | `Commande [PaiementAutorisé]` | ✅ |
| Enregistrer la transaction | `Paiement.enregistrer()` | — | — | ⚠️ Op. de Classe à confirmer |
| Mettre à jour le statut Préparée | `Commande.preparer()` | — | `Expédition [Emballée]` | ❌ Op. absente du Diag. de Classes |
| Réconcilier le paiement | `Facture.marquerCommePayee()` | — | `Facture [Payée]` | ⚠️ Manquant en Séquence |
| Mettre à jour le statut Clôturée | `Commande.cloturer()` | — | `Commande [Clôturée]` | ❌ Op. absente du Diag. de Classes |
| Envoyer notification d'expédition | `ServiceNotification.envoyer()` | `envoyerCourrielConfirmation(commande)` | — | ⚠️ Nom incohérent |

> [!warning] Divergences inter-diagrammes — recommandations
> - **`Commande.preparer()` absente du Diagramme de Classes** : ajouter cette opération à la classe `Commande`, ou réutiliser une opération existante.
> - **`Commande.cloturer()` absente du Diagramme de Classes** : ajouter l'opération ; cohérent avec la transition d'état de la [[UML State|Machine à États]] de la facture.
> - **Action « Vérifier le catalogue produit » sans message Séquence** : ajouter un message `verifierProduit(idProduit)` dans le Diagramme de Séquence avant la réservation d'inventaire.
> - **Nom incohérent `ServiceNotification.envoyer()` vs. `envoyerCourrielConfirmation`** : standardiser sur un nom unique entre tous les diagrammes.
