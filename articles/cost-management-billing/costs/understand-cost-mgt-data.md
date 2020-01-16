---
title: Comprendre les données Azure Cost Management | Microsoft Docs
description: Cet article vous aide à mieux comprendre les données incluses dans Azure Cost Management et la fréquence à laquelle elles sont traitées, collectées, affichées et fermées.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 75d414756d8818bd4e29fc0507af73eccf0e0e01
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986201"
---
# <a name="understand-cost-management-data"></a>Comprendre les données Cost Management

Cet article vise à détailler les informations sur l’utilisation et les coûts incluses dans Azure Cost Management. Il indique la fréquence à laquelle les données sont traitées, collectées, affichées et fermées. L’utilisation d’Azure vous est facturée tous les mois. Bien que les cycles de facturation soient mensuels, les dates de début et de fin d’un cycle varient selon le type d’abonnement. La fréquence à laquelle Cost Management reçoit les données d’utilisation varie selon différents facteurs. Ces facteurs comprennent le temps nécessaire pour traiter les données et la fréquence à laquelle les services Azure envoient les données d’utilisation au système de facturation.

Azure Cost Management inclut toutes les informations sur les activités et achats, y compris les réservations et les offres tierces associées aux comptes des Contrats entreprise. Les comptes des Contrats client Microsoft et les abonnements individuels avec des taux de paiement à l’utilisation portent uniquement sur l’utilisation des services de Place de marché et Azure. Les autres coûts, ainsi que les coûts liés au support, ne sont pas inclus. Les coûts font l’objet d’une estimation tant qu’aucune facture n’a été générée, et n’incluent pas les crédits.

## <a name="supported-microsoft-azure-offers"></a>Offres Microsoft Azure prises en charge

Les informations suivantes présentent les [offres Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) actuellement prises en charge dans Azure Cost Management. Une offre Azure correspond au type d’abonnement Azure que vous avez. Les données sont disponibles dans Cost Management à compter de la date indiquée dans **Données disponibles à partir de/du**. Si un abonnement change d’offre, les coûts avant la date de changement de l’offre ne sont pas disponibles.

| **Catégorie**  | **Nom de l’offre** | **ID du quota** | **Numéro de l’offre** | **Données disponibles à partir de/du** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Entreprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Mai 2014<sup>1</sup> |
| **Contrat Entreprise (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Mai 2014<sup>1</sup> |
| **Contrat Entreprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Mai 2014<sup>1</sup> |
| **Contrat client Microsoft** | [Offre Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | Mars 2019<sup>3</sup> |
| **Contrat client Microsoft** | [Offre Microsoft Azure pour Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | Mars 2019<sup>3</sup> |
| **Contrat client Microsoft pris en charge par les partenaires** | Offre Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 et CSPDEVTEST_2018-05-01<br><br>L’ID de quota est réutilisé pour le contrat client Microsoft et les abonnements CSP hérités. Actuellement, seuls les abonnements aux contrats clients Microsoft sont pris en charge. | N/A | 2 octobre 2019 |
| **MSDN (Microsoft Developer Network)** | [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 octobre 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 octobre 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 octobre 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 octobre 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Version d’évaluation gratuite](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 octobre 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Azure dans Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 octobre 2018<sup>2</sup> |
| **Pay-As-You-Go** | Pass Azure<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 octobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 octobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 octobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 octobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 octobre 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise : BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 octobre 2018<sup>2</sup> |

_<sup>**1**</sup> Pour les données antérieures à mai 2014, visitez [Azure Enterprise Portal](https://ea.azure.com)._

_<sup>**2**</sup>Pour les données antérieures au 2 octobre 2018, visitez le [Centre des comptes Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Les Contrats client Microsoft ont commencé en mars 2019 et ne disposent pas de données historiques avant cette date._

_<sup>**4**</sup> Les données historiques des abonnements basés sur le crédit et avec règlement à l’avance peuvent ne pas correspondre à votre facture. Consultez [Les données historiques peuvent ne pas correspondre à la facture](#historical-data-might-not-match-invoice) ci-dessous._

Les offres suivantes ne sont pas encore prises en charge :

| Category  | **Nom de l’offre** | **ID du quota** | **Numéro de l’offre** |
| --- | --- | --- | --- |
| **Azure Allemagne** | [Azure Allemagne - Paiement à l'utilisation](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Paiement à l’utilisation Azure Government | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Fournisseur de solutions cloud (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Fournisseur de solutions cloud (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Fournisseur de solutions cloud (CSP)** | Azure Germany en tant que fournisseur CSP pour Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay-As-You-Go**                 | Kit de démarrage Azure pour les étudiants | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay-As-You-Go** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay-As-You-Go**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plans de support** | Support standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plans de support** | Support Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plans de support** | Support Developer Support                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plans de support** | Plan de support Germany                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plans de support** | Support standard Azure Government   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plans de support** | Support Professional Direct Azure Government | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plans de support** | Support Developer Support Azure Government  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Déterminer le type de votre offre
Si vous ne voyez pas de données pour un abonnement et que vous souhaitez déterminer si votre abonnement figure dans les offres prises en charge, vous pouvez valider que votre abonnement est pris en charge. Pour valider qu’un abonnement Azure est pris en charge, connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez ensuite **Tous les services** dans le volet du menu de gauche. Dans la liste des services, sélectionnez **Abonnements**. Dans le menu de la liste des abonnements, cliquez sur l’abonnement que vous souhaitez vérifier. Votre abonnement est affiché sous l’onglet Vue d’ensemble et vous pouvez voir les éléments **Offre** et **ID de l’offre**. L’image suivante en montre un exemple.

![Exemple de l’onglet Vue d’ensemble de l’abonnement montrant l’offre et l’ID de l’offre](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Coûts inclus dans Cost Management

Les tableaux suivants indiquent les données qui sont comprises ou non dans Cost Management. Les coûts font l’objet d’une estimation tant qu’aucune facture n’a été générée. Les coûts affichés n’incluent pas les crédits prépayés et gratuits.

**Données de coût et d’utilisation**

| **Incluses** | **Non incluses** |
| --- | --- |
| Utilisation des services Azure<sup>5</sup>        | Frais de support. Pour plus d’informations, consultez [Conditions de facturation expliquées](../understand/understand-invoice.md). |
| Utilisation des produits de la Place de marché<sup>6</sup> | Taxes. Pour plus d’informations, consultez [Conditions de facturation expliquées](../understand/understand-invoice.md). |
| Achats de la Place de marché<sup>6</sup>      | Crédits. Pour plus d’informations, consultez [Conditions de facturation expliquées](../understand/understand-invoice.md). |
| Achats de réservation<sup>7</sup>      |  |
| Amortissement des achats de réservation<sup>7</sup>      |  |

_<sup>**5**</sup> L’utilisation des services Azure est basée sur la réservation et des prix négociés._

_<sup>**6**</sup> Les achats de la Place de marché ne sont pas disponibles pour les offres de paiement à l’utilisation, MSDN et Visual Studio pour l’instant._

_<sup>**7**</sup> Les achats de réservation sont uniquement disponibles pour les comptes des Contrats entreprise pour l’instant._

**Métadonnées**

| **Incluses** | **Non incluses** |
| --- | --- |
| Étiquettes de ressources<sup>8</sup> | Étiquettes des groupes de ressources |

_<sup>**8**</sup> Les étiquettes de ressources sont appliquées quand les données d’utilisation sont envoyées à partir de chaque service et ne sont pas disponibles rétroactivement pour l’historique d’utilisation._

## <a name="rated-usage-data-refresh-schedule"></a>Planification d’actualisation des données sur l’utilisation estimée

Les données de coût et d’utilisation sont disponibles dans Gestion des coûts + facturation dans le portail Azure et les [API de prise en charge](../index.yml). Gardez à l’esprit les points suivants quand vous passez en revue les coûts :

- Les frais estimés pour la période de facturation en cours sont mis à jour six fois par jour.
- Les frais estimés pour la période de facturation en cours peuvent changer au fur et à mesure que votre utilisation augmente.
- Chaque mise à jour est cumulative et inclut tous les éléments de ligne et les informations de la mise à jour précédente.
- Azure finalise ou _ferme_ la période de facturation en cours jusqu’à 72 heures (trois jours calendaires) après la fin de la période de facturation.

Les exemples suivants illustrent la façon dont les périodes de facturation peuvent se terminer.

Abonnements Contrat Entreprise (EA). Si le mois de facturation se termine le 31 mars, les frais estimés sont mis à jour jusqu’à 72 heures plus tard. Dans cet exemple, à minuit (UTC) le 4 avril.

Abonnements avec paiement à l’utilisation. Si le mois de facturation se termine le 15 mai, les frais estimés peuvent être mis à jour jusqu’à 72 heures plus tard. Dans cet exemple, à minuit (UTC) le 19 mai.

### <a name="rerated-data"></a>Données réestimées

Que vous utilisiez les [API Cost Management](../index.yml), Power BI ou le portail Azure pour récupérer des données, prévoyez une réestimation des frais de la période de facturation en cours, et donc des changements, jusqu’à la clôture de la facture.

## <a name="usage-data-update-frequency-varies"></a>La fréquence de mise à jour des données d’utilisation varie

La disponibilité de vos données d’utilisation engagées dans Cost Management dépend de plusieurs facteurs, notamment :

- La fréquence à laquelle les services Azure (par exemple Stockage, Compute, CDN et SQL) envoient les données d’utilisation.
- Le temps nécessaire pour traiter les données d’utilisation via le moteur de classement et des pipelines de gestion des coûts.

Certains services envoient les données d’utilisation plus fréquemment que d’autres. Par conséquent, vous pouvez voir dans Cost Management des données pour certains services plus tôt que d’autres services qui émettent des données moins fréquemment. En règle générale, l’affichage de l’utilisation des services prend entre 8 et 24 heures dans Cost Management. Gardez à l’esprit que les données pour un mois ouvert sont actualisées au fur et à mesure que votre utilisation augmente, car les mises à jour sont cumulatives.

## <a name="historical-data-might-not-match-invoice"></a>Les données historiques peuvent ne pas correspondre à la facture

Les données historiques des offres basées sur le crédit et avec règlement à l’avance peuvent ne pas correspondre à votre facture. Certaines offres de paiement à l'utilisation, MSDN et Visual Studio peuvent entraîner l’application de crédits et paiements avancés Azure à la facture. Toutefois, les données historiques indiquées dans Azure Cost Management sont uniquement basées sur les frais de consommation estimés. Elles n’incluent pas les paiements et les crédits. Par conséquent, les données historiques indiquées pour les offres suivantes peuvent ne pas correspondre exactement à votre facture.

- Azure for Students (MS-AZR-0170P)
- Azure dans Open (MS-AZR-0111P)
- Pass Azure (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Essai gratuit (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Voir aussi

- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](../../cost-management/quick-acm-cost-analysis.md).
