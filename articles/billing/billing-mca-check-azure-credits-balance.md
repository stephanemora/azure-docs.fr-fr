---
title: Suivre le solde de crédit Azure d’un Contrat client Microsoft
description: Découvrez comment vérifier le solde de crédit Azure d’un Contrat client Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375537"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Suivre le solde de crédit Azure d’un Contrat client Microsoft

Vous pouvez vérifier le solde de crédits Azure sur votre compte de facturation d’un Contrat Client Microsoft dans le portail Azure. 

Vous utilisez des crédits pour payer les frais associés à des produits éligibles aux crédits. Vous êtes facturé lorsque vous utilisez des produits qui ne sont pas éligibles aux crédits ou quand votre utilisation dépasse votre solde de crédits. Pour plus d’informations, consultez [Produits non couverts par les crédits Azure](#products-that-arent-covered-by-azure-credits).

Dans le compte de facturation associé à un Contrat Client Microsoft, les crédits sont attribués à un profil de facturation. Chaque profil de facturation a des crédits qui lui sont propres. Vous devez avoir un rôle de propriétaire, contributeur, lecteur ou gestionnaire de factures sur le profil de facturation ou bien un rôle de propriétaire, contributeur ou lecteur sur le compte de facturation pour voir le solde de crédits Azure d’un profil de facturation. Pour en savoir plus sur les rôles, consultez [Présentation des rôles d’administrateur Azure dans le cadre des Contrats client Microsoft](billing-understand-mca-roles.md).

Cet article s'applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Consulter votre solde de crédits dans le portail Azure

1. Connectez-vous au [Portail Azure]( https://portal.azure.com).

2. Recherchez **Gestion des coûts + facturation**.

    ![Capture d’écran montrant la recherche dans le portail pour gestion des coûts + facturation](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Sélectionnez **Crédits Azure** dans la partie gauche. Selon votre accès, vous devrez peut-être sélectionner un compte ou profil de facturation, puis sélectionner **Crédits Azure**.

4. La page Crédits Azure affiche les informations suivantes :

   ![Capture d’écran montrant le solde de crédits et les transactions pour un profil de facturation](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Terme               | Définition                           |
   |--------------------|--------------------------------------------------------|
   | Solde estimé  | Montant estimé de crédits dont vous disposez après prise en compte de toutes les transactions facturées et en attente |
   | Solde actuel    | Montant des crédits depuis votre dernière facture. Les transactions en attente ne sont pas incluses. |
   | Transactions       | Transactions de facturation qui ont impacté votre solde de crédits Azure |

   Lorsque votre solde estimé atteint zéro (0), vous êtes facturé pour toute votre utilisation, y compris pour les produits éligibles aux crédits.

6. Sélectionnez **Liste des crédits** pour afficher la liste des crédits du profil de facturation. La liste des crédits fournit les informations suivantes :

   ![Capture d’écran des listes de crédits d'un profil de facturation](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Terme | Définition |
   |---|---|
   | Source | Source d’acquisition du crédit |
   | Date de début | Date d'acquisition du crédit |
   | Date d'expiration | Date d’expiration du crédit |
   | Solde actuel | Solde depuis votre dernière facture |
   | Montant d’origine | Montant d'origine du crédit |
   | Statut | État actuel du crédit. Cet état peut être actif, utilisé, expiré ou en cours d'expiration |

## <a name="check-your-credit-balance-programmatically"></a>Vérifier votre solde de crédits programmatiquement

Vous pouvez utiliser les API [Consommation](https://docs.microsoft.com/rest/api/billing/) et [Facturation Azure](https://docs.microsoft.com/rest/api/consumption/) pour obtenir programmatiquement le solde de crédits de votre compte de facturation.

Les exemples ci-dessous utilisent les API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-billing-profiles-you-have-access-to"></a>Rechercher les profils de facturation auxquels vous avez accès

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
L’API retourne en réponse une liste de comptes de facturation et des profils de facturation associés.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Utilisez la propriété `displayName` du profil de facturation pour identifier le profil de facturation pour lequel vous voulez vérifier le solde de crédits. Copiez la propriété `id` du profil de facturation. Par exemple, si vous souhaitez vérifier le solde de crédits du profil de facturation **Development**, vous copiez ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Collez cette valeur quelque part, car vous en aurez besoin à l’étape suivante.

### <a name="get-azure-credit-balance"></a>Obtenir le solde de crédits Azure 

Envoyez la requête suivante, en remplaçant `<billingProfileId>` par l’élément `id` copié lors de la première étape (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

L’API retourne en réponse le solde estimé et le solde actuel pour le profil de facturation.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nom de l'élément  | Description                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Montant estimé de vos crédits après prise en compte de toutes les transactions facturées et en attente. |
| `currentBalance`   | Montant des crédits depuis votre dernière facture. Les transactions en attente ne sont pas prises en compte.    |
| `pendingCreditAdjustments`      | Ajustements tels que les remboursements qui ne sont pas encore facturés.  |
| `expiredCredit`      |  Crédits ayant expiré depuis votre dernière facture.  |
| `pendingEligibleCharges`  | Frais éligibles aux crédits qui ne sont pas encore facturés.   |

### <a name="get-list-of-credits"></a>Obtenir la liste des crédits

Envoyez la requête suivante, en remplaçant `<billingProfileId>` par l’élément `id` copié lors de la première étape (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
L’API retourne en réponse les listes de crédits Azure pour un profil de facturation.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nom de l'élément  | Description                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Montant initial des crédits. |
| `closedBalance`   | Solde à la date de la dernière facture.    |
| `source`      | Source définissant qui a acquis les crédits. |
| `startDate`      |  Date d’activation des crédits.  |
| `expirationDate`  | Date d’expiration des crédits.   |
| `poNumber`  | Numéro de bon de commande de la facture sur laquelle les crédits ont été facturés.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Obtenir les transactions ayant impacté le solde de crédits

Envoyez la requête suivante, en remplaçant `<billingProfileId>` par l’élément `id` copié lors de la première étape (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Vous devez passer une valeur **startDate**  et une valeur **endDate** pour obtenir les transactions relevant de la période qui vous intéresse.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
L’API retourne en réponse toutes les transactions qui ont impacté le solde de crédits de votre profil de facturation.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nom de l'élément  | Description                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Date à laquelle la transaction a eu lieu. |
| `description` | Description de la transaction. |
| `adjustments`   | Ajustements de crédits pour la transaction.    |
| `creditExpired`      | Montant des crédits ayant expiré. |
| `charges`      |  Frais associés à la transaction.  |
| `closedBalance`  | Solde après la transaction.   |
| `eventType`  | Le type de transaction.   |
| `invoiceNumber`  | Numéro de la facture sur laquelle la transaction est facturée. Il reste vide si la transaction est en attente.   |

## <a name="how-credits-are-used"></a>Utilisation des crédits

Dans un compte de facturation pour un Contrat client Microsoft, vous utilisez les profils de facturation pour gérer vos factures et modes de paiement. Une facture mensuelle est générée pour chaque profil de facturation et les modes de paiement permettent de payer la facture.

Vous attribuez vos crédits acquis à un profil de facturation. Quand une facture est générée pour le profil de facturation, les crédits sont automatiquement appliqués au total des frais afin de calculer le montant que vous devez payer. Vous vous acquittez du montant restant en utilisant le mode de paiement choisi (chèque, virement ou carte de crédit).

## <a name="products-that-arent-covered-by-azure-credits"></a>Produits non couverts par les crédits Azure

 Les produits suivants ne sont pas couverts par vos crédits Azure. Ces produits vous sont facturés, quel que soit votre solde de crédit :

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Utilisateur inscrit
- Openlogic
- Utilisateur inscrit de Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (mensuel)
- Visual Studio Enterprise (annuel)
- Visual Studio Professional (mensuel)
- Visual Studio Professional (annuel)
- Produits de la Place de marché Azure
- Plans de support Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l’accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre le compte de facturation associé à un Contrat client Microsoft](billing-mca-overview.md)
- [Comprendre les termes indiqués sur votre facture du Contrat client Microsoft](billing-mca-understand-your-invoice.md)
