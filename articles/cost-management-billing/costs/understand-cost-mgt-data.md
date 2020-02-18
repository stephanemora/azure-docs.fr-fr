---
title: Comprendre les données Azure Cost Management | Microsoft Docs
description: Cet article vous aide à mieux comprendre les données incluses dans Azure Cost Management et la fréquence à laquelle elles sont traitées, collectées, affichées et fermées.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: a514dc07da3e4fd5928614099eb86ecef311bbb1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188533"
---
# <a name="understand-cost-management-data"></a>Comprendre les données Cost Management

Cet article vise à détailler les informations sur l’utilisation et les coûts incluses dans Azure Cost Management. Il indique la fréquence à laquelle les données sont traitées, collectées, affichées et fermées. L’utilisation d’Azure vous est facturée tous les mois. Bien que les cycles de facturation soient mensuels, les dates de début et de fin d’un cycle varient selon le type d’abonnement. La fréquence à laquelle Cost Management reçoit les données d’utilisation varie selon différents facteurs. Ces facteurs comprennent le temps nécessaire pour traiter les données et la fréquence à laquelle les services Azure envoient les données d’utilisation au système de facturation.

Azure Cost Management inclut toutes les informations sur les activités et achats, y compris les réservations et les offres tierces associées aux comptes des Contrats entreprise. Les comptes des Contrats client Microsoft et les abonnements individuels avec des taux de paiement à l’utilisation portent uniquement sur l’utilisation des services de Place de marché et Azure. Les coûts du support et les autres coûts ne sont pas inclus. Les coûts font l’objet d’une estimation tant qu’aucune facture n’a été générée et ne tiennent pas compte des crédits.

## <a name="supported-microsoft-azure-offers"></a>Offres Microsoft Azure prises en charge

Les informations suivantes présentent les [offres Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) actuellement prises en charge dans Azure Cost Management. Une offre Azure correspond au type d’abonnement Azure que vous avez. Les données sont disponibles dans Cost Management à compter de la date indiquée dans **Données disponibles à partir de/du**. Si les offres d’un abonnement changent, les coûts avant la date de modification de l’offre ne sont pas disponibles.

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

_<sup>**3**</sup> Les Contrats client Microsoft ont commencé en mars 2019 et ne disposent pas de données historiques préalables à cette date._

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
Si vous ne voyez pas de données pour un abonnement et que vous souhaitez déterminer si votre abonnement figure dans les offres prises en charge, vous pouvez valider que votre abonnement est pris en charge. Pour valider la prise en charge d’un abonnement Azure, connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez ensuite **Tous les services** dans le volet du menu de gauche. Dans la liste des services, sélectionnez **Abonnements**. Dans le menu de la liste des abonnements, sélectionnez l’abonnement que vous souhaitez vérifier. Votre abonnement est affiché sous l’onglet Vue d’ensemble et vous pouvez voir les éléments **Offre** et **ID de l’offre**. L’image suivante en montre un exemple.

![Exemple de l’onglet Vue d’ensemble de l’abonnement montrant l’offre et l’ID de l’offre](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Coûts inclus dans Cost Management

Les tableaux suivants indiquent les données qui sont comprises ou non dans Cost Management. Les coûts font l’objet d’une estimation tant qu’aucune facture n’a été générée. Les coûts affichés n’incluent pas les crédits prépayés et gratuits.

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

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Utilisation des étiquettes dans les données de coût et d’utilisation

Azure Cost Management reçoit des étiquettes dans le cadre de chaque enregistrement d’utilisation soumis par les services individuels. Les contraintes suivantes s’appliquent à ces étiquettes :

- Les étiquettes doivent être appliquées directement aux ressources et ne sont pas héritées implicitement du groupe de ressources parent.
- Les étiquettes de ressource sont prises en charge seulement pour les ressources déployées sur des groupes de ressources.
- Certaines ressources déployées peuvent ne pas prendre en charge les étiquettes ou ne pas inclure d’étiquettes dans les données d’utilisation : consultez [Étiquettes prises en charge pour les ressources Azure](../../azure-resource-manager/tag-support.md).
- Les étiquettes de ressource sont incluses seulement dans les données d’utilisation quand l’étiquette est appliquée : les étiquettes ne sont pas appliquées aux données d’historique.
- Les étiquettes de ressource sont disponibles dans Cost Management seulement une fois que les données sont actualisées : consultez [La fréquence de mise à jour des données d’utilisation varie](#usage-data-update-frequency-varies).
- Les étiquettes de ressource sont disponibles dans Cost Management seulement quand la ressource est active/en cours d’exécution et produit des enregistrements d’utilisation (par exemple pas quand une machine virtuelle est désallouée).
- La gestion des étiquettes nécessite un accès Contributeur à chaque ressource.
- La gestion des stratégies d’étiquettes nécessite l’accès Propriétaire ou Contributeur de stratégie à un groupe d’administration, un abonnement ou un groupe de ressources.
    
Si vous ne voyez pas une étiquette spécifique dans Cost Management, prenez en considération les éléments suivants :

- L’étiquette a-t-elle été appliquée directement à la ressource ?
- L’étiquette a-t-elle été appliquée il y a plus de 24 heures ? Consultez [La fréquence de mise à jour des données d’utilisation varie](#usage-data-update-frequency-varies)
- Le type de ressource prend-il en charge les étiquettes ? Les types de ressources suivants ne prennent pas en charge les étiquettes dans les données d’utilisation à la date du 1er décembre 2019. Pour obtenir la liste complète de ce qui est pris en charge, consultez [Prise en charge des étiquettes pour les ressources Azure](../../azure-resource-manager/tag-support.md).
    - Annuaires Azure Active Directory B2C
    - Pare-feux Azure
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Équilibreurs de charge
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    - passerelle VPN
    
Voici quelques conseils pour l’utilisation des étiquettes :

- Planifiez en amont et définissez une stratégie d’étiquetage qui vous permet de ventiler les coûts par organisation, application, environnement, etc.
- Utilisez Azure Policy pour copier les étiquettes des groupes de ressources vers des ressources individuelles et pour appliquer votre stratégie d’étiquetage.
- Utilisez l’API Tags conjointement avec Query ou UsageDetails pour obtenir tous les coûts en fonction des étiquettes actuelles.


**Mise à niveau de l’évaluation gratuite vers un abonnement avec paiement à l’utilisation**

Les clients bénéficiant d’une offre d’évaluation gratuite (044P) qui sera convertie en offre PAYG (003P) peuvent voir leur utilisation pendant la période d’évaluation gratuite. Toutefois, ils perdent la visibilité du traçage gratuit après la conversion. Après la conversion, seuls l’utilisation et les coûts PAYG sont affichés dans Cost Management.

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

Les données historiques des offres basées sur le crédit et avec règlement à l’avance peuvent ne pas correspondre à votre facture. Certaines offres de paiement à l'utilisation, MSDN et Visual Studio peuvent entraîner l’application de crédits et paiements avancés Azure à la facture. Toutefois, les données historiques indiquées dans Azure Cost Management sont uniquement basées sur les frais de consommation estimés. Elles n’incluent pas les paiements et les crédits. Les données historiques indiquées pour les offres suivantes peuvent donc ne pas correspondre exactement à votre facture.

- Azure for Students (MS-AZR-0170P)
- Azure dans Open (MS-AZR-0111P)
- Pass Azure (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Essai gratuit (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Voir aussi

- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](../../cost-management/quick-acm-cost-analysis.md).
