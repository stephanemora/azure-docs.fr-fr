---
title: Suivre votre Engagement de consommation Microsoft Azure (MACC)
description: Découvrez comment suivre votre Engagement de consommation Microsoft Azure (MACC) pour un Contrat client Microsoft.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/06/2021
ms.author: banders
ms.openlocfilehash: 41a4f22e669300e24ddce0248e8ab1744b773202
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301921"
---
# <a name="track-your-microsoft-azure-consumption-commitment-macc"></a>Suivre votre Engagement de consommation Microsoft Azure (MACC)

L’offre Engagement de consommation Microsoft Azure (MACC) est un engagement contractuel que votre organisation peut avoir souscrit pour des dépenses relatives à Microsoft Azure au fil du temps. Si votre organisation a un MACC pour un compte de facturation Contrat client Microsoft (MCA), vous pouvez vérifier des aspects importants de votre engagement, notamment les dates de début et de fin, l’engagement restant et les dépenses admissibles dans le portail Azure ou via les API REST. Les comptes de facturation MACC ou CTC pour Contrat Entreprise (EA) ne sont pas encore disponibles sur le portail Azure ou via les API REST.

## <a name="track-your-macc-commitment"></a>Suivre votre engagement MACC

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Recherchez **Gestion des coûts + facturation**.  
    :::image type="content" source="./media/track-consumption-commitment/billing-search-cost-management-billing.png" alt-text="Capture d’écran montrant la recherche de Cost Management and Billing dans le portail." lightbox="./media/track-consumption-commitment/billing-search-cost-management-billing.png" :::
3. Dans la page des étendues de facturation, sélectionnez le compte de facturation pour lequel vous souhaitez suivre l’engagement. Le compte de facturation doit être de type **Contrat client Microsoft**.  
    :::image type="content" source="./media/track-consumption-commitment/list-of-scopes.png" alt-text="Capture d’écran montrant les étendues de facturation." lightbox="./media/track-consumption-commitment/list-of-scopes.png" :::
    > [!NOTE]
     > Le portail Azure mémorise la dernière étendue de facturation à laquelle vous accédez et affiche l’étendue la prochaine fois que vous revenez à la page Gestion des coûts + facturation. Vous ne verrez pas la page des étendues de facturation si vous avez déjà consulté Gestion des coûts + facturation. Si c’est le cas, vérifiez que vous êtes dans l’[étendue appropriée](#check-access-to-a-microsoft-customer-agreement). Si ce n’est pas le cas, [changez d’étendue](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) pour sélectionner le compte de facturation d’un Contrat client Microsoft.
4. Sélectionnez **Propriétés** dans la partie gauche, puis sélectionnez **Engagement de consommation Microsoft Azure (MACC)** .  
    :::image type="content" source="./media/track-consumption-commitment/select-macc-tab.png" alt-text="Capture d’écran qui montre la sélection de l’onglet MACC." lightbox="./media/track-consumption-commitment/select-macc-tab.png" :::
5. L’onglet Engagement de consommation Microsoft Azure (MACC) comporte les sections suivantes.

#### <a name="remaining-commitment"></a>Engagement restant 

L’engagement restant affiche le montant de l’engagement restant depuis votre dernière facture.

:::image type="content" source="./media/track-consumption-commitment/macc-remaining-commitment.png" alt-text="Capture d’écran de l’engagement restant pour un MACC." lightbox="./media/track-consumption-commitment/macc-remaining-commitment.png" :::

#### <a name="details"></a>Détails

La section Détails affiche d’autres aspects importants de votre engagement.

:::image type="content" source="./media/track-consumption-commitment/macc-details.png" alt-text="Capture d’écran des détails de MACC." lightbox="./media/track-consumption-commitment/macc-details.png" :::

| Terme | Définition |
|---|---|
| id | Identificateur qui identifie votre MACC de façon unique. Cet identificateur est utilisé pour récupérer vos informations MACC via les API REST. |
| Date d’achat | Date à laquelle vous avez pris l’engagement. |
| Date de début | Date à laquelle l’engagement a pris effet. |
| Date de fin | Date à laquelle l’engagement a expiré. |
| Montant de l’engagement | Montant que vous vous êtes engagé à dépenser pour des produits ou services qualifiés pour le MACC. |
| Statut | État de votre engagement. |

Votre MACC peut avoir l’un des états suivants :

- Actif : Le MACC est actif. Toute dépense admissible contribuera à votre engagement MACC.
- Terminé : Vous avez terminé votre engagement MACC. 
- Expiré : Le MACC a expiré. Pour plus d’informations, contactez l’équipe Compte Microsoft. 
- Annulé : Le MACC est annulé. Les nouvelles dépenses Azure ne contribueront pas à votre engagement MACC.

#### <a name="events"></a>Événements

La section Événements affiche les événements (dépenses facturées) qui ont décrémenté votre engagement MACC.

:::image type="content" source="./media/track-consumption-commitment/macc-events.png" alt-text="Capture d’écran des événements MACC." lightbox="./media/track-consumption-commitment/macc-events.png" :::

| Terme | Définition |
|---|---|
| Date | Date à laquelle l’événement s’est produit |
| Description | Description de l’événement |
| Profil de facturation | Profil de facturation pour lequel l’événement s’est produit |
| Décrémentation MACC | Montant de la décrémentation MACC à partir de l’événement |
| Engagement restant | Engagement MACC restant après l’événement |

### <a name="rest-api"></a>[REST API](#tab/rest)

Vous pouvez utiliser les API [Facturation Azure](/rest/api/billing/) et [Consommation](/rest/api/consumption/) pour obtenir programmatiquement Engagement de consommation Microsoft Azure (MACC) de votre compte de facturation.

Les exemples ci-dessous utilisent les API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-billing-accounts-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2020-05-01
```
La réponse de l’API renvoie une liste des comptes de facturation.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Contoso",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "hasReadAccess": true,
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Kayla Lewis",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Individual",
        "hasReadAccess": true,
      }
    }
  ]
}
```

Utilisez la propriété `displayName` du compte de facturation pour identifier le compte de facturation pour lequel vous voulez suivre le MACC. Copiez le `name` du compte de facturation. Par exemple, si vous voulez suivre le MACC pour le compte de facturation **Contoso**, vous devez copier `9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, car vous en aurez besoin à l’étape suivante.

### <a name="get-a-list-of-microsoft-azure-consumption-commitments"></a>Obtenir la liste des Engagements consommation Microsoft Azure

Effectuez la requête suivante, en remplaçant `<billingAccountName>` par l’élément `name` copié lors de la première étape (`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`).

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/lots?api-version=2021-05-01&$filter=source%20eq%20%27ConsumptionCommitment%27
```
La réponse de l’API renvoie des listes de MACC pour votre compte de facturation.

```json
    {
    "value": [
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "name": "G2021032459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Active",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 9899.42
          },
          "source": "ConsumptionCommitment",
          "startDate": "2021-03-01T00:00:00.0000000Z",
          "expirationDate": "2024-02-28T00:00:00.0000000Z"
        }
      },
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G1011082459206000XXXX",
        "name": "G1011082459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Complete",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 0.00
          },
          "source": "ConsumptionCommitment",
          "startDate": "2020-03-01T00:00:00.0000000Z",
          "expirationDate": "2021-02-28T00:00:00.0000000Z"
        }
      }
    ]
  }
```

| Nom de l'élément  | Description                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `purchasedDate`  | Date à laquelle le MACC a été acheté.   |
| `status`  | État de votre engagement. |
| `originalAmount` | Montant de l’engagement initial. |
| `closedBalance`   | Engagement restant depuis la dernière facture.    |
| `source`      | Pour MACC, la source est toujours ConsumptionCommitment. |
| `startDate`      |  Date d’activation de MACC.  |
| `expirationDate`  | Date d’expiration de MACC.   |

Votre MACC peut avoir l’un des états suivants : 

- Actif : Le MACC est actif. Toute dépense admissible contribuera à votre engagement MACC.
- Terminé : Vous avez terminé votre engagement MACC. 
- Expiré : Le MACC a expiré. Pour plus d’informations, contactez l’équipe Compte Microsoft. 
- Annulé : Le MACC est annulé. Les nouvelles dépenses Azure ne contribueront pas à votre engagement MACC. 

### <a name="get-events-that-affected-macc-commitment"></a>Obtenir les événements qui ont eu un impact sur l’engagement MACC

Effectuez la requête suivante, en remplaçant `<billingAccountName>` par l’élément `name` copié lors de la première étape (`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`). Vous devez transmettre une valeur **startDate**  et une valeur **endDate** pour obtenir les événements relevant de la période qui vous intéresse.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/events?api-version=2021-05-01&startDate=<startDate>&endDate=<endDate>&$filter=lotsource%20eq%20%27ConsumptionCommitment%27
```

La réponse de l’API renvoie tous les événements qui ont eu un impact sur votre engagement MACC.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Finance",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-05-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00075XXXX",
        "charges": {
          "currency": "USD",
          "value": -100.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9899.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00075XXXX"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Engineering",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-04-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00074XXXX",
        "charges": {
          "currency": "USD",
          "value": -0.29
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9999.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00074XXXX"
      }
    }
  ]
}

```
| Nom de l'élément  | Description                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `billingProfileId` | Identificateur unique du profil de facturation pour lequel l’événement s’est produit. |
| `billingProfileDisplayName` | Nom d’affichage du profil de facturation pour lequel l’événement s’est produit. |
| `lotId`   | Identificateur unique du MACC.    |
| `lotSource`      | ConsumptionCommitment pour MACC. |
| `transactionDate`      |  Date à laquelle l’événement s’est produit.  |
| `description`  | Description de l'événement.   |
| `charges`  | Montant de décrémentation MACC.   |
| `closedBalance`  | Solde après l’événement.   |
| `eventType`  | Seuls les événements SettledCharges sont pris en charge pour MACC.   |
| `invoiceNumber`  | ID unique de la facture dont les frais ont décrémenté le MACC.   |

---

## <a name="azure-services-and-marketplace-offers-that-are-eligible-for-macc"></a>Services Azure et offres Place de marché qualifiés pour MACC

Vous pouvez déterminer quels services Azure et quelles offres Place de marché sont qualifiés pour la décrémentation MACC dans le portail Azure. Pour plus d’informations, consultez [Déterminer les offres éligibles aux engagements de consommation Azure (MACC/CTC)](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc).

## <a name="azure-credits-and-macc"></a>Crédits Azure et MACC

Si votre organisation a reçu des crédits Azure de la part de Microsoft, la consommation ou les achats couverts par ces crédits ne contribueront pas à votre engagement MACC.

Si votre organisation a acheté un prépaiement Azure, la consommation ou les achats couverts par ces crédits ne contribueront pas à votre engagement MACC.  Toutefois, l’achat du prépaiement proprement dit décrémente votre engagement MACC.

Par exemple, Contoso a pris un engagement MACC de 50 000 USD en mai. En juin, elle a acheté un prépaiement Azure de 10 000 USD. L’achat décrémente son engagement MACC et l’engagement restant est de 40 000 USD. En juin, Contoso a consommé 10 000 USD de services admissibles au prépaiement Azure. Les frais de service seront couverts par son prépaiement Azure, mais ils ne décrémenteront pas son engagement MACC. Une fois le prépaiement Azure entièrement utilisé, toute consommation de services Azure et tout autre achat admissible décrémenteront l’engagement MACC.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Déterminer les offres éligibles aux engagements de consommation Azure (MACC/CTC)](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)
- [Suivre votre solde de crédits Azure](mca-check-azure-credits-balance.md)