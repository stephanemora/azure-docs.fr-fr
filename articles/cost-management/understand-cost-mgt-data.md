---
title: Comprendre les données Azure Cost Management | Microsoft Docs
description: Cet article vous aide à mieux comprendre les données incluses dans Azure Cost Management et la fréquence à laquelle elles sont traitées, collectées, affichées et fermées.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: ca0d77ba2d1968d49e8ac556d42137cdc4c81e53
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879435"
---
# <a name="understand-cost-management-data"></a>Comprendre les données Cost Management

Cet article vous aide à mieux comprendre les données incluses dans Azure Cost Management. Il décrit également la fréquence à laquelle les données sont traitées, collectées, affichées et fermées. L’utilisation d’Azure vous est facturée tous les mois. Toutefois, votre type d’abonnement Azure détermine quand votre mois de facturation se termine. La fréquence à laquelle Cost Management reçoit les données d’utilisation varie selon différents facteurs. Ces facteurs comprennent le temps nécessaire pour traiter les données et la fréquence à laquelle les services Azure envoient les données d’utilisation au système de facturation.

## <a name="supported-microsoft-offers"></a>Offres Microsoft prises en charge

Les informations suivantes présentent les [offres Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) actuellement prises en charge dans Azure Cost Management.  Une offre Azure correspond au type d’abonnement Azure que vous avez.

| Catégorie  | **Nom de l’offre** | **ID de quota** | **Numéro de l’offre** |
| --- | --- | --- | --- |
| **Azure Germany** | [Paiement à l’utilisation Azure Germany](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Entreprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Contrat Entreprise (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Contrat Entreprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Microsoft Developer Network (MSDN)** | [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Pay-As-You-Go** | [Dev/Test - Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Pay-As-You-Go** | [Version d’évaluation gratuite](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Pay-As-You-Go** | [Azure dans Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Pay-As-You-Go** | [Azure pour les étudiants](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay-As-You-Go** | Pass Azure                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise-MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise : BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

Le tableau suivant présente les offres non prises en charge.

| Catégorie  | **Nom de l’offre** | **ID de quota** | **Numéro de l’offre** |
| --- | --- | --- | --- |
| **Fournisseur de solutions cloud (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Fournisseur de solutions cloud (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Fournisseur de solutions cloud (CSP)** | Azure Germany en tant que fournisseur CSP pour Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay-As-You-Go**                 | Kit de démarrage Azure pour les étudiants | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay-As-You-Go**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plans de support** | Support standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plans de support** | Support Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plans de support** | Support Developer Support                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plans de support** | Plan de support Germany                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plans de support** | Support standard Azure Government   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plans de support** | Support Professional Direct Azure Government | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plans de support** | Support Developer Support Azure Government  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

Pour les clients des catégories d'offre Paiement à l'utilisation, MSDN et Visual Studio, les données sont disponibles dans Cost Management à partir du 10/02/2018. Pour accéder aux données de votre abonnement avant le 10/02/2018, vous pouvez utiliser la [centre des comptes Azure](https://account.azure.com/subscriptions) pour télécharger votre utilisation sur les détails dans un fichier CSV ou vous pouvez utiliser la [API détails d’utilisation](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Déterminer votre type d’offre
Si vous ne voyez pas de données pour un abonnement et que vous souhaitez déterminer si votre abonnement figure dans les offres prises en charge, vous pouvez valider que votre abonnement est pris en charge. Pour valider qu’un abonnement Azure est pris en charge, connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez ensuite **Tous les services** dans le volet du menu de gauche. Dans la liste des services, sélectionnez **Abonnements**. Dans le menu de la liste des abonnements, cliquez sur l’abonnement que vous souhaitez vérifier. Votre abonnement est affiché sous l’onglet Vue d’ensemble et vous pouvez voir les éléments **Offre** et **ID de l’offre**. L’image suivante en montre un exemple.

![Exemple de l’onglet Vue d’ensemble de l’abonnement montrant l’offre et l’ID de l’offre](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Coûts inclus dans Cost Management

Les tableaux suivants indiquent les données qui sont comprises ou non dans Cost Management. Tous les coûts sont estimés jusqu'à ce qu’une facture est générée. Des coûts figurant n’incluent pas de crédits gratuits et prépayées.

**Données de coût et d’utilisation**

| **Inclus** | **Non inclus** |
| --- | --- |
| Utilisation des services Azure<sup>1</sup> | Achats de réservation. Pour plus d’informations, consultez [API dédiées à l’automatisation de la réservation Azure](../billing/billing-reservation-apis.md). |
| Utilisation des offres de la Place de marché | Achats sur la Place de marché. Pour plus d’informations, consultez [Frais de service tiers](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Frais de support. Pour plus d’informations, consultez [Conditions de facturation expliquées](../billing/billing-understand-your-invoice.md). |
|   | Taxes. Pour plus d’informations, consultez [Conditions de facturation expliquées](../billing/billing-understand-your-invoice.md). |
|   | Crédits. Pour plus d’informations, consultez [Conditions de facturation expliquées](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> L’utilisation des services Azure est basée sur la réservation et des prix négociés.

**Métadonnées**

| **Inclus** | **Non inclus** |
| --- | --- |
| Étiquettes de ressources<sup>2</sup> | Étiquettes des groupes de ressources |

<sup>2</sup> Les étiquettes de ressources sont appliquées quand les données d’utilisation sont envoyées à partir de chaque service et ne sont pas disponibles rétroactivement pour l’historique d’utilisation.

## <a name="rated-usage-data-refresh-schedule"></a>Planification d’actualisation des données sur l’utilisation estimée

Les données de coût et d’utilisation sont disponibles dans Gestion des coûts + facturation dans le portail Azure et les [API de prise en charge](index.yml). Gardez à l’esprit les points suivants quand vous passez en revue les coûts :

- Les frais estimés pour la période de facturation en cours sont mis à jour six fois par jour.
- Les frais estimés pour la période de facturation en cours peuvent changer au fur et à mesure que votre utilisation augmente.
- Chaque mise à jour est cumulative et inclut tous les éléments de ligne et les informations de la mise à jour précédente.
- Azure finalise ou _ferme_ la période de facturation en cours jusqu’à 72 heures (trois jours calendaires) après la fin de la période de facturation.

Les exemples suivants illustrent la façon dont les périodes de facturation peuvent se terminer.

Abonnements Contrat Entreprise (EA). Si le mois de facturation se termine le 31 mars, les frais estimés sont mis à jour jusqu’à 72 heures plus tard. Dans cet exemple, à minuit (UTC) le 4 avril.

Abonnements avec paiement à l’utilisation. Si le mois de facturation se termine le 15 mai, les frais estimés peuvent être mis à jour jusqu’à 72 heures plus tard. Dans cet exemple, à minuit (UTC) le 19 mai.

### <a name="rerated-data"></a>Données réestimées

Que vous utilisiez les [API Cost Management](https://aka.ms/costmgmt/docs), Power BI ou le portail Azure pour récupérer des données, prévoyez une réestimation des frais de la période de facturation en cours, et donc des changements, jusqu’à la clôture de la facture.

## <a name="usage-data-update-frequency-varies"></a>La fréquence de mise à jour des données d’utilisation varie

La disponibilité de vos données d’utilisation engagées dans Cost Management dépend de plusieurs facteurs, notamment :

- La fréquence à laquelle les services Azure (par exemple Stockage, Compute, CDN et SQL) envoient les données d’utilisation.
- Le temps nécessaire pour traiter les données d’utilisation via le moteur de classement et des pipelines de gestion des coûts.

Certains services envoient les données d’utilisation plus fréquemment que d’autres. Par conséquent, vous pouvez voir dans Cost Management des données pour certains services plus tôt que d’autres services qui émettent des données moins fréquemment. En règle générale, l’affichage de l’utilisation des services prend entre 8 et 24 heures dans Cost Management. Gardez à l’esprit que les données pour un mois ouvert sont actualisées au fur et à mesure que votre utilisation augmente, car les mises à jour sont cumulatives.

## <a name="see-also"></a>Voir aussi

- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](quick-acm-cost-analysis.md).
