---
title: Migrer à partir de l’accord entreprise aux API de contrat de client de Microsoft - Azure | Microsoft Docs
description: Cet article vous aide à comprendre les conséquences de la migration d’un contrat d’entreprise Microsoft (EA) vers un contrat de client de Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e4c5607089efb247620766fb311b97cae3772770
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279871"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrer à partir de l’accord entreprise aux API de contrat de client de Microsoft

Cet article vous aide à comprendre la structure de données, les API et les autres différences d’intégration système entre les comptes contrat entreprise (EA) et le contrat de client de Microsoft (MCA). Azure Cost Management prend en charge les API pour les deux types de compte. Examinez le [configurer le compte de facturation pour](../billing/billing-mca-setup-account.md) article de contrat de client de Microsoft avant de continuer.

Les organisations avec un compte EA existant doivent vérifier cet article conjointement avec la configuration d’un compte MCA. Auparavant, le renouvellement d’un compte EA devait certains un minimum de travail pour déplacer à partir d’une ancienne inscription vers un autre. Toutefois, la migration vers un compte MCA nécessite des efforts supplémentaires. Effort supplémentaire est en raison de modifications dans le sous-système de facturation sous-jacent, qui affectent toutes les API liées au coût et les offres de service.

## <a name="mca-apis-and-integration"></a>API de MCA et intégration

API de MCA et nouvelle intégration permettent :

- Avoir disponibilité complète des API par le biais des API Azure natives.
- Configurer plusieurs factures dans un seul compte de facturation.
- Accéder à une API combinée avec l’utilisation des services Azure, l’utilisation de la place de marché tierce et achats Marketplace.
- Afficher les coûts sur la facturation des profils (le même que les inscriptions) à l’aide d’Azure Cost Management.
- Accéder aux nouvelles API pour afficher les coûts, soyez averti lorsque les coûts dépassent des seuils prédéfinis et exporter des données brutes automatiquement.

## <a name="migration-checklist"></a>Liste de contrôle de migration

L’aide d’éléments suivante que transition vers les API MCA.

- Familiarisez-vous avec la nouvelle [contrat de client Microsoft compte de facturation](../billing/billing-mca-overview.md).
- Déterminer les API que vous utilisez et voir celles qui est remplacées dans la section suivante.
- Familiarisez-vous avec [API REST Azure Resource Manager](/rest/api/azure).
- Si vous n’utilisez pas déjà des API Azure Resource Manager, [inscrire votre application cliente auprès d’Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Mettre à jour le code de programmation pour [utiliser l’authentification Azure AD](/rest/api/azure/#create-the-request).
- Mettre à jour le code de programmation pour remplacer les appels d’API EA avec les appels d’API de MCA.
- Mettre à jour pour utiliser les nouveaux codes d’erreur de gestion des erreurs.
- Passez en revue les offres d’intégration supplémentaires, telles que Cloudyn et Power BI, pour d’autres nécessaire action.

## <a name="ea-apis-replaced-with-mca-apis"></a>API EA remplacé par les API MCA

API de EA utilisent une clé API pour l’authentification et l’autorisation. API de MCA utilisent l’authentification Azure AD.

| Objectif | API EA | API DE MCA |
| --- | --- | --- |
| Solde et crédits | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Utilisation (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilisation (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilisation de la place de marché (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Billing periods | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Price sheet | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Achats de réservation | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Reservation recommendations | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Utilisation de la réservation | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> service azure et l’utilisation de la place de marché de fournisseurs tiers sont disponibles avec la [API détails d’utilisation](/rest/api/consumption/usagedetails).

Les API suivantes sont disponibles pour les comptes de facturation MCA :

| Objectif | API de contrat (MCA) de client de Microsoft |
| --- | --- |
| Comptes de facturation<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Profils de facturation<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Sections de facture<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Factures | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Abonnements de facturation | {scope}/billingSubscriptions |

<sup>2</sup> API retournent des listes d’objets, qui sont des portées, où les expériences de gestion des coûts dans le portail Azure et API fonctionnent. Pour plus d’informations sur les étendues de gestion des coûts, consultez [comprendre et utiliser des étendues](understand-work-scopes.md).

Si vous utilisez des API EA existante, vous devez les mettre à jour pour prendre en charge les comptes de facturation MCA. Le tableau suivant présente les autres modifications de l’intégration :

| Objectif | Ancien offre | Nouvelle offre |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Informations sur la consommation Microsoft](/power-bi/desktop-connect-azure-consumption-insights) pack et connecteur de contenu | [Application Insights Power BI de Microsoft Azure consommation](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) et [connecteur d’Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API pour obtenir le solde et crédits

Le [obtenir le résumé de solde](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API vous donne un résumé mensuel des :

- Balances
- Nouveaux achats
- Frais de service place de marché Azure
- Ajustements
- Frais de dépassement du service

Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request).

L’API de résumé de solde Get est remplacé par l’API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Pour obtenir des soldes disponibles avec l’API de solde disponible :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API pour obtenir le coût et utilisation

Obtenir une répartition quotidienne des coûts à partir de l’utilisation des services Azure, l’utilisation de la place de marché tierce et autres achats Marketplace avec les API suivantes. Les API distinctes suivantes ont été fusionnées pour les services Azure et de l’utilisation de la place de marché tierce. Les anciennes API sont remplacés par le [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. Il ajoute les achats de la place de marché, qui ont été précédemment uniquement affichées dans le solde résumés jusqu'à ce jour.

- [Obtenir les détails de l’utilisation/téléchargement](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obtenir les détails de l’utilisation/envoyer](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obtenir des détails d’utilisation/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obtenir des détails d’utilisation/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obtenir le magasin de la place de marché frais/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Obtenir le magasin de la place de marché frais/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request).

Toutes les API précédentes sont remplacées par l’API de détails d’utilisation/consommation.

Pour obtenir des détails d’utilisation avec l’API de détails d’utilisation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

L’API de détails d’utilisation, comme avec toutes les API de gestion des coûts, est disponible pour plusieurs étendues. Des coûts facturés, comme vous recevriez traditionnellement au niveau de l’inscription, utilisez l’étendue de profil de facturation.  Pour plus d’informations sur les étendues de gestion des coûts, consultez [comprendre et utiliser des étendues](understand-work-scopes.md).

| Type | Format d’ID |
| --- | --- |
| Compte de facturation | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profil de facturation | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Abonnement | `/subscriptions/{subscriptionId}` |
| Groupe de ressources | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Utiliser les paramètres de chaîne de requête suivant pour mettre à jour le code de programmation.

| Paramètres de l’ancien | Nouveaux paramètres |
| --- | --- |
| `billingPeriod={billingPeriod}` | Non pris en charge |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Le corps de la réponse est également modifié.

Ancien corps de réponse :

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nouveau corps de réponse :

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Le nom de propriété contenant le tableau d’enregistrements d’utilisation a été remplacée par les données à _valeurs_. Chaque enregistrement est utilisé pour avoir une liste plate de propriétés détaillées. Toutefois, chaque enregistrement maintenant tous les détails se trouvent maintenant dans une propriété imbriquée nommée _propriétés_, à l’exception des balises. La nouvelle structure est cohérente avec d’autres API Azure. Certains noms de propriété ont changé. Le tableau suivant présente les propriétés correspondantes.

| Ancienne propriété | Nouvelle propriété | Notes |
| --- | --- | --- |
| ID du compte | S.O. | Le créateur de l’abonnement n’est pas suivi. Utilisez invoiceSectionId (identique à departmentId). |
| AccountNameAccountOwnerId et AccountOwnerEmail | S.O. | Le créateur de l’abonnement n’est pas suivi. Utilisez invoiceSectionName (identique à departmentName). |
| Informations supplémentaires | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Notez que ces propriétés sont contraires. Si isAzureCreditEnabled a la valeur true, ChargesBilledSeparately est false. |
| ConsumedQuantity | quantité | &nbsp; |
| Service consommé | consumedService | Les valeurs de chaîne exacte peuvent différer. |
| ID du service consommé | Aucun | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date et usageStartDate | date | &nbsp;  |
| jour | Aucun | Analyse le jour à partir de la date. |
| ID du service | invoiceSectionId | Les valeurs exactes diffèrent. |
| DepartmentName | invoiceSectionName | Les valeurs de chaîne exacte peuvent différer. Configurer les sections de facture en fonction des départements, si nécessaire. |
| ExtendedCost et coût | costInBillingCurrency | &nbsp;  |
| InstanceId | ResourceId | &nbsp;  |
| Il s'agit de frais récurrents | Aucun | &nbsp;  |
| Lieu | location | &nbsp;  |
| Catégorie du compteur | meterCategory | Les valeurs de chaîne exacte peuvent différer. |
| ID du compteur | meterId | Les valeurs de chaîne exacte diffèrent. |
| Nom du compteur | meterName | Les valeurs de chaîne exacte peuvent différer. |
| Région du compteur | meterRegion | Les valeurs de chaîne exacte peuvent différer. |
| Sous-catégorie du compteur | meterSubCategory | Les valeurs de chaîne exacte peuvent différer. |
| Mois | Aucun | Analyse le mois de date. |
| Nom de l’offre | Aucun | Utilisez publisherName et productOrderName. |
| OfferId | Aucun | &nbsp;  |
| Numéro de commande | Aucun | &nbsp;  |
| PartNumber | Aucun | Utilisez meterId et productOrderName pour identifier de manière unique les prix. |
| Nom du plan | productOrderName | &nbsp;  |
| Produit | Produit |   |
| ProductId | productId | Les valeurs de chaîne exacte diffèrent. |
| Nom de l’éditeur | publisherName | &nbsp;  |
| ResourceGroup | nom_groupe_ressources | &nbsp;  |
| ResourceGuid | meterId | Les valeurs de chaîne exacte diffèrent. |
| Emplacement des ressources | resourceLocation | &nbsp;  |
| ID d'emplacement des ressources | Aucun | &nbsp;  |
| Taux de ressources | effectivePrice | &nbsp;  |
| ID de l'administrateur de service | S.O. | &nbsp;  |
| Informations de service 1 | serviceInfo1 | &nbsp;  |
| Informations de service 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Les valeurs de chaîne exacte peuvent différer. |
| ServiceTier | meterSubCategory | Les valeurs de chaîne exacte peuvent différer. |
| Identificateur du service Store | S.O. | &nbsp;  |
| Guid d'abonnement | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Balises | tags | La propriété tags s’applique à l’objet racine, pas à la propriété de propriétés imbriquées. |
| Unité de mesure | unitOfMeasure | Les valeurs de chaîne exacte diffèrent. |
| usageEndDate | date | &nbsp;  |
| Year | Aucun | Analyse l’année de date. |
| (nouveau) | billingCurrency | Devise utilisée pour les frais. |
| (nouveau) | billingProfileId | ID unique pour le profil de facturation (identique à l’inscription). |
| (nouveau) | billingProfileName | Nom du profil de facturation (identique à l’inscription). |
| (nouveau) | chargeType | Permet de différencier l’utilisation des services Azure, l’utilisation de la place de marché et les achats. |
| (nouveau) | invoiceId | ID unique de la facture. Vide pour le mois en cours, ouvrez. |
| (nouveau) | publisherType | Type de serveur de publication pour les achats. Vide pour l’utilisation. |
| (nouveau) | serviceFamily | Type d’achat. Vide pour l’utilisation. |
| (nouveau) | servicePeriodEndDate | Date de fin pour le service acheté. |
| (nouveau) | servicePeriodStartDate | Date de début pour le service acheté. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API périodes remplacée par les API de factures facturation

N’utilisent pas les comptes de facturation MCA périodes de facturation. Au lieu de cela, ils utilisent des factures pour les coûts d’étendue pour les périodes de facturation spécifiques. Le [API périodes de facturation](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) est remplacé par l’API de factures. Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request).

Pour obtenir les factures avec l’API de factures :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API de feuille de prix

Cette section décrit les API de feuille de prix existantes et fournit des recommandations pour la déplacer vers l’API grille tarifaire pour les contrats de client de Microsoft. Il décrit l’API grille tarifaire pour les contrats de client de Microsoft et explique les champs dans les tables de tarification. Le [Enterprise obtenir la table de tarification](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) et [Enterprise obtenir périodes de facturation](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API sont remplacés par l’API grille tarifaire pour les contrats de client de Microsoft (Microsoft.Billing/billingAccounts/billingProfiles / grille tarifaire). La nouvelle API prend en charge JSON et CSV met en forme, dans des formats REST asynchrones. Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>API d’entreprise de facturation

Vous avez utilisé les API d’entreprise facturation avec les inscriptions d’entreprise pour obtenir les prix et les informations de période de facturation. Authentification et autorisation utilisé des jetons web Azure Active Directory.

Pour obtenir les prix applicables pour l’inscription d’entreprise spécifiée avec la table de tarification et les API de période de facturation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API de grille tarifaire pour les contrats de client de Microsoft

Utiliser l’API de la grille tarifaire pour les contrats de client Microsoft pour afficher les prix pour tous les services de consommation de consommation Azure et à la place de marché. Les prix indiqués pour le profil de facturation s’appliquent à tous les abonnements qui appartiennent au profil de facturation.

Utilisez l’API grille tarifaire pour afficher toutes les données de table de tarification de services de consommation Azure au format CSV :

| Méthode | URI de demande |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Utilisez l’API grille tarifaire pour afficher toutes les données de table de tarification de services de consommation Azure au format JSON :

| Méthode | URI de demande |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

À l’aide de l’API retourne la table de tarification pour l’intégralité du compte. Toutefois, vous pouvez également obtenir une version condensée de la table de tarification au format PDF. Le résumé inclut des services de consommation de consommation Azure et la place de marché qui sont facturés pour une facture spécifique. La facture est identifiée par le n° {facture}, qui est le même que le **numéro de facture** indiqué dans les fichiers PDF de résumé de facture. Voici un exemple.

![Image d’un exemple montrant le numéro de facture qui correspond à la n° de facture](./media/migrate-cost-management-api/invoicesummary.png)

Pour afficher les informations de facturation avec l’API grille tarifaire au format CSV :

| Méthode | URI de demande |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Pour afficher les informations de facturation avec l’API grille tarifaire au Format JSON :

| Méthode | URI de demande |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Vous pouvez également voir les prix estimés pour n’importe quel service de consommation de consommation Azure ou à la place de marché dans le cycle de facturation actuel ouvert ou d’un point de service.

Pour afficher les prix estimés pour les services de la consommation avec l’API grille tarifaire au format CSV :

| Méthode | URI de demande |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Pour afficher les prix estimés pour les services de la consommation avec l’API grille tarifaire au format JSON :

| Méthode | URI de demande |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Les API de feuille de prix Microsoft client accord sont *API REST asynchrones*. Les réponses pour les API modifié à partir des anciennes API synchrones. Le corps de la réponse de l’API est également modifié.

#### <a name="old-response-body"></a>Ancien corps de réponse

Voici un exemple de la réponse de l’API REST synchrone :

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nouveau corps de réponse

Les API prennent en charge la [Azure REST asynchrones](../azure-resource-manager/resource-manager-async-operations.md) format. Appeler l’API à l’aide de GET, et vous recevez la réponse suivante :

```
No Response Body

HTTP Status 202 Accepted
```

Les en-têtes suivants sont envoyés avec l’emplacement de la sortie :

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Appeler une autre fonction GET à l’emplacement. La réponse à l’appel GET est la même jusqu'à ce que l’opération atteigne un état d’achèvement ou l’échec. Une fois terminé, la réponse à l’emplacement d’appel GET retourne l’URL de téléchargement. Comme si l’opération a été exécutée en même temps. Voici un exemple :

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

Le client peut également appeler GET la `Azure-AsyncOperation`. Le point de terminaison retourne l’état de l’opération.

Le tableau suivant affiche les champs dans l’ancienne API feuille de prix obtenir de l’entreprise. Il inclut les champs correspondants dans la nouvelle table de tarification pour les contrats de client de Microsoft :

| Ancienne propriété | Nouvelle propriété | Notes |
| --- | --- | --- |
| billingPeriodId  | _Non applicable_ | Non applicable. Pour les contrats de client de Microsoft, la facture et la table de tarification associé remplacé le concept de billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Les valeurs de chaîne exacte peuvent différer. |
| includedQuantity  | includedQuantity | Non applicable pour les services dans les contrats de client de Microsoft. |
| partNumber  | _Non applicable_ | Au lieu de cela, utilisez une combinaison de productOrderName (identique à offerId) et meterid. |
| unitPrice  | unitPrice | Prix unitaire est applicable pour les services utilisés dans les contrats de client de Microsoft. |
| currencyCode  | pricingCurrency | Accords de clients Microsoft ont des représentations de prix dans les devises tarification et facturation. Le currencyCode correspond à la pricingCurrency dans les contrats de client de Microsoft. |
| offerId | productOrderName | Au lieu de OfferId, vous pouvez utiliser productOrderName, mais n’est pas identique à OfferId. Toutefois, productOrderName et compteur déterminent la tarification contrats client de Microsoft liées à meterId et l’Offerid dans les inscriptions héritées. |

## <a name="consumption-price-sheet-api-operations"></a>Opérations d’API grille tarifaire consommation

Pour les contrats d’entreprise, vous avez utilisé l’API de grille tarifaire consommation [obtenir](/rest/api/consumption/pricesheet/get) et [obtenir par période de facturation](/rest/api/consumption/pricesheet/getbybillingperiod) opérations pour une étendue par ID d’abonnement ou d’une période de facturation. L’API utilise l’authentification de gestion des ressources Azure.

Pour obtenir les informations de tarification pour une étendue avec l’API grille tarifaire :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Pour obtenir des informations de la table de tarification par période avec l’API grille tarifaire de facturation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Au lieu des points de terminaison API ci-dessus, utilisez les options suivantes pour les contrats de client de Microsoft :

**API de grille tarifaire pour les contrats de client de Microsoft (API REST asynchrone)**

Cette API est pour les contrats de client de Microsoft et fournit des attributs supplémentaires.

**Table de tarification pour une étendue de profil de facturation dans un compte de facturation**

Cette API est l’API existante. Il a été mis à jour afin de fournir la table de tarification pour un profil de facturation dans un compte de facturation.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Table de tarification pour une étendue par compte de facturation

Azure Resource Manager est utilisée lorsque vous obtenez la table de tarification dans la portée de l’inscription dans un compte de facturation.

Pour obtenir la table de tarification au niveau du compte d’inscription dans un compte de facturation :

| Méthode | URI de demande |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Pour un contrat de client de Microsoft, utilisez les informations dans la section suivante. Il fournit les propriétés de champ utilisées pour les contrats de Microsoft Customer.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Table de tarification pour une étendue de profil de facturation dans un compte de facturation

La table de tarification de mise à jour par le compte de facturation API Obtient la table de tarification au format CSV. Pour obtenir la table de tarification dans l’étendue de profil de facturation pour un MCA :

| Méthode | URI de demande |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Portée de l’EA d’inscription, la réponse de l’API et les propriétés sont identiques. Les propriétés correspondent aux propriétés MCA mêmes.

Les propriétés du dossier pour [API de feuille de prix Azure Resource Manager](/rest/api/consumption/pricesheet) et les mêmes propriétés de nouveau se trouvent dans le tableau suivant.

| Ancienne Azure Resource Manager prix feuille API propriété  | Nouvelle propriété de l’API de grille tarifaire Microsoft client contrat   | Description |
| --- | --- | --- |
| ID du compteur | _meterId_ | Identificateur unique pour le compteur. Identique au meterId. |
| Nom du compteur | meterName | Nom du compteur. Compteur représente la ressource de service Azure pouvant être déployées. |
| Catégorie du compteur  | service | Nom de la catégorie de classification pour le compteur. Même que le service dans la table de tarification du contrat de client Microsoft. Les valeurs de chaîne exacte diffèrent. |
| Sous-catégorie du compteur | meterSubCategory | Nom de la catégorie du compteur sous-classification. En fonction de la classification de différenciation d’ensemble de haut niveau de fonctionnalité dans le service. Par exemple, base SQL DB ou base de données SQL Standard. |
| Région du compteur | meterRegion | &nbsp;  |
| Unité | _Non applicable_ | Peut être analysé à partir d’unitOfMeasure. |
| Unité de mesure | unitOfMeasure | &nbsp;  |
| Référence pièce | _Non applicable_ | Au lieu du numéro de référence, utilisez productOrderName et MeterId pour identifier de façon unique le prix pour un profil de facturation. Les champs sont répertoriés sur la facture MCA plutôt que le numéro de référence dans les factures MCA. |
| Prix unitaire | unitPrice | Prix unitaire de contrat de client de Microsoft. |
| Code de devise | pricingCurrency | Accords de clients Microsoft représentent les prix de la tarification de devise et la devise de facturation. Code de devise est identique à la pricingCurrency dans les contrats de client de Microsoft. |
| Quantité incluse | includedQuantity | Non applicable aux services dans les contrats de client de Microsoft. Afficher les valeurs nulles. |
|  Proposer un ID  | productOrderName | Au lieu de OfferId, utilisez productOrderName. Pas identique à OfferId, toutefois le productOrderName et le compteur déterminent la tarification dans les contrats de client de Microsoft. Le lien meterId et l’Offerid inscriptions héritées. |

Le prix pour les contrats de client de Microsoft est défini différemment des contrats entreprise. Le prix pour services lors de l’inscription de l’entreprise est unique pour le produit, un numéro de référence, compteur et offre. Le numéro de référence n’est pas utilisé dans les contrats de client de Microsoft.

Le prix de service de consommation Azure qui fait partie d’un contrat de client de Microsoft est unique pour productOrderName et meterId. Ils représentent le compteur de service et le plan de produit.

Pour rapprocher entre la table de tarification et l’utilisation de l’API de détails d’utilisation, vous pouvez utiliser le productOrderName et meterId.

Les utilisateurs qui ont facturation propriétaire, collaborateur, lecteur de profil et facturer les droits de gestionnaire peuvent télécharger la table de tarification.

La table de tarification inclut les prix pour les services dont le prix est basé sur l’utilisation. Les services incluent la consommation Azure et la place de marché. Le prix le plus récent à la fin de chaque période de service est verrouillé et appliqué à l’utilisation dans une période de service unique. Pour les services de consommation Azure, la période de service est généralement un mois calendaire.

### <a name="retired-price-sheet-api-fields"></a>Champs API grille tarifaire mis hors service

Les champs suivants ne sont pas disponibles dans l’API de feuille de prix Microsoft client contrat ou avoir les mêmes champs.

|Champ mis hors service| Description|
|---|---|
| billingPeriodId | Non applicable. Correspond aux n° facture pour MCA. |
| offerId | Non applicable. Correspond à productOrderName dans MCA. |
| meterCategory  | Non applicable. Correspond au Service dans MCA. |
| unité | Non applicable. Peut être analysé à partir d’unitOfMeasure. |
| currencyCode | Identique à la pricingCurrency dans MCA. |
| meterLocation | Identique à la meterRegion dans MCA. |
| numéro de référence de numéro de référence | Non applicable, car le numéro de référence n’est pas répertorié dans les factures MCA. Au lieu du numéro de référence, utilisez la combinaison de meterId et productOrderName pour identifier de manière unique les prix. |
| totalIncludedQuantity | Non applicable. |
| pretaxStandardRate  | Non applicable. |

## <a name="reservation-instance-charge-api-replaced"></a>API de frais d’Instance de réservation remplacé

Vous pouvez obtenir de facturation des transactions d’achat de réservation avec les [API d’Instance réservée de frais](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). La nouvelle API inclut tous les achats, y compris des offres de la place de marché tierce. Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request). L’API de frais d’Instance réservée est remplacé par l’API de Transactions.

Pour obtenir des transactions avec l’API de Transactions d’achat de réservation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Recommandations API remplacé

Réservé API de recommandations d’achat Instance fournissent l’utilisation de la machine virtuelle sur les derniers 7, 30 ou 60 jours. API fournissent également des recommandations d’achat de réservation. À savoir :

- [Partagé des API d’Instance réservée de recommandation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API de recommandations unique Instance réservée](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request). Les recommandations de réservation API répertoriées précédemment sont remplacées par le [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

Pour obtenir des recommandations de réservation avec l’API de recommandations de réservation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>API d’utilisation de réservation remplacé

Vous pouvez obtenir l’utilisation de la réservation dans une inscription avec l’API d’utilisation de l’Instance réservée. S’il existe plusieurs instances réservées dans une inscription, vous pouvez également obtenir l’utilisation de tous les achats de l’instance réservée à l’aide de cette API.

À savoir :

- [Détails de l’utilisation d’Instance réservée](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Résumé de l’utilisation d’Instance réservée](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Toutes les API de consommation sont remplacés par des API Azure natives qui utilisent Azure AD pour l’authentification et l’autorisation. Pour plus d’informations sur appeler des API REST Azure, consultez [mise en route avec REST](/rest/api/azure/#create-the-request). Les recommandations de réservation API répertoriées précédemment sont remplacées par le [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) et [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API.

Pour obtenir les détails de la réservation avec l’API de détails de réservation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Pour obtenir des résumés de réservation avec l’API de résumés de réservation :

| Méthode | URI de demande |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Déplacer à partir de Cloudyn pour la gestion des coûts

Les organisations qui utilisent [Cloudyn](https://cloudyn.com) doit commencer à utiliser [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) pour les besoins de gestion des coûts. Gestion des coûts est disponible dans le portail Azure avec aucune intégration et une latence de huit heures. Pour plus d’informations, consultez le [documentation de la gestion des coûts](index.yml).

Avec Azure Cost Management, vous pouvez :

- Afficher les coûts au fil du temps par rapport à un budget prédéfini. Analyser tous les jours des modèles de coût pour identifier et arrêter des anomalies de dépense. Décomposer les coûts par balises, groupe de ressources, service et l’emplacement.
- Créer des budgets pour définir des limites sur l’utilisation et les coûts et recevez une notification lors de l’approche de seuils importants. Configurer automation avec des groupes d’actions pour déclencher des événements personnalisés et d’appliquer des limites matérielles sur vos propres conditions.
- Optimiser les coûts et l’utilisation avec des recommandations d’Azure Advisor. Découvrez les optimisations d’achat avec des réservations, réduire les machines virtuelles sous-utilisées et supprimer des ressources inutilisées pour respecter les budgets.
- Planifier une exportation de données de coût et d’utilisation pour publier un fichier CSV à votre compte de stockage, tous les jours. Automatiser l’intégration avec les systèmes externes pour conserver les données de facturation synchronisés et à jour.

## <a name="power-bi-integration"></a>Intégration Power BI

Si vous utilisez Power BI pour les rapports de coût, vous devez passer à ce qui suit :

- Microsoft Azure consommation Insights application Power BI
- Connecteur de bureau Azure Consumption Insights


Le connecteur est recommandé pour les organisations souhaitant la plus grande souplesse. Toutefois, l’application Power BI est également disponible pour une installation rapide.

- Installer le [Microsoft Azure Consumption Insights Power BI app](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Se connecter avec le connecteur Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

Le pack de contenu d’informations sur la consommation et le connecteur antérieure a travaillé au niveau de l’inscription. Il nécessitait l’accès lecture au moins. La nouvelle application de la consommation Insights Power BI et le nouveau connecteur Azure Consumption Insights sont disponibles pour les utilisateurs de profil facturation. Équipes qui ont besoin d’options supplémentaires pour l’examen des coûts ou afficher les coûts sur les profils de facturation doivent utiliser dans [analyse des coûts](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

- Lire le [documentation de la gestion des coûts](index.yml) pour apprendre à surveiller et contrôler les dépenses Azure. Ou, si vous souhaitez optimiser l’utilisation des ressources avec la gestion des coûts.
