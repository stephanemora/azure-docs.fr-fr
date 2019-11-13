---
title: Comprendre les données Azure Cost Management | Microsoft Docs
description: Cet article vous aide à mieux comprendre les données incluses dans Azure Cost Management et la fréquence à laquelle elles sont traitées, collectées, affichées et fermées.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721368"
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
| **Contrat client Microsoft pris en charge par les partenaires** | Offre Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 et CSPDEVTEST_2018-05-01<br><br>L’ID de quota est réutilisé pour le contrat client Microsoft et les abonnements CSP hérités. Actuellement, seuls les abonnements aux contrats clients Microsoft sont pris en charge. | N/A | Octobre 2019 |
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
| Utilisation des services Azure<sup>5</sup>        | Frais de support. Pour plus d’informations, consultez [Conditions de facturation expliquées](../billing/billing-understand-your-invoice.md). |
| Utilisation des produits de la Place de marché<sup>6</sup> | Taxes. Pour plus d’informations, consultez [Conditions de facturation expliquées](../billing/billing-understand-your-invoice.md). |
| Achats de la Place de marché<sup>6</sup>      | Crédits. Pour plus d’informations, consultez [Conditions de facturation expliquées](../billing/billing-understand-your-invoice.md). |
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

Les données de coût et d’utilisation sont disponibles dans Gestion des coûts + facturation dans le portail Azure et les [API de prise en charge](index.yml). Gardez à l’esprit les points suivants quand vous passez en revue les coûts :

- Les frais estimés pour la période de facturation en cours sont mis à jour six fois par jour.
- Les frais estimés pour la période de facturation en cours peuvent changer au fur et à mesure que votre utilisation augmente.
- Chaque mise à jour est cumulative et inclut tous les éléments de ligne et les informations de la mise à jour précédente.
- Azure finalise ou _ferme_ la période de facturation en cours jusqu’à 72 heures (trois jours calendaires) après la fin de la période de facturation.

Les exemples suivants illustrent la façon dont les périodes de facturation peuvent se terminer.

Abonnements Contrat Entreprise (EA). Si le mois de facturation se termine le 31 mars, les frais estimés sont mis à jour jusqu’à 72 heures plus tard. Dans cet exemple, à minuit (UTC) le 4 avril.

Abonnements avec paiement à l’utilisation. Si le mois de facturation se termine le 15 mai, les frais estimés peuvent être mis à jour jusqu’à 72 heures plus tard. Dans cet exemple, à minuit (UTC) le 19 mai.

### <a name="rerated-data"></a>Données réestimées

Que vous utilisiez les [API Cost Management](index.yml), Power BI ou le portail Azure pour récupérer des données, prévoyez une réestimation des frais de la période de facturation en cours, et donc des changements, jusqu’à la clôture de la facture.

## <a name="cost-management-data-fields"></a>Champs de données Cost Management

Les champs de données suivants sont disponibles dans les fichiers de détails sur l’utilisation et les API Cost Management. Pour les champs en gras suivants, les partenaires peuvent utiliser les fonctionnalités Filtrer et Regrouper lors de l’analyse des coûts pour analyser les frais en fonction de plusieurs champs. Les champs en gras s’appliquent uniquement aux contrats client Microsoft pris en charge par les partenaires.

| **Nom du champ** | **Description** |
| --- | --- |
| invoiceId | ID de facture affiché sur la facture associée à la transaction spécifique. |
| previousInvoiceID | Référence à une facture d’origine s’il y a un remboursement (coût négatif). Ce champ est renseigné uniquement en cas de remboursement. |
| billingAccountName | Nom du compte de facturation représentant le partenaire. Il accumule tous les coûts associés aux clients qui ont été intégrés à un contrat client Microsoft et les clients CSP qui ont effectué des achats de droits tels que SaaS, la Place de marché Azure et les réservations. |
| billingAccountID | Identificateur du compte de facturation représentant le partenaire. |
| billingProfileID | Identificateur du profil de facturation qui regroupe les coûts de l’ensemble des factures dans une même devise, pour tous les clients qui ont été intégrés dans un contrat client Microsoft et les clients CSP ayant effectué des achats de droits tels que SaaS, la Place de marché Azure et les réservations. |
| billingProfileName | Nom du profil de facturation qui regroupe les coûts de l’ensemble des factures dans une même devise, pour tous les clients qui ont été intégrés dans un contrat client Microsoft et les clients CSP ayant effectué des achats de droits tels que SaaS, la Place de marché Azure et les réservations. |
| invoiceSectionName | Nom du projet dont le coût est répertorié dans la facture. Non applicable pour les contrats client Microsoft intégrés par les partenaires. |
| invoiceSectionID | Identificateur du projet dont le coût est répertorié dans la facture. Non applicable pour les contrats client Microsoft intégrés par les partenaires. |
| **CustomerTenantID** | Identificateur du locataire Azure Active Directory de l’abonnement du client. |
| **CustomerName** | Nom du locataire Azure Active Directory de l’abonnement du client. |
| **CustomerTenantDomainName** | Nom de domaine du locataire Azure Active Directory de l’abonnement du client. |
| **PartnerTenantID** | Identificateur du locataire Azure Active Directory du partenaire. |
| **PartnerName** | Nom du locataire Azure Active Directory du partenaire. |
| **ResellerMPNID** | Valeur MPNID du revendeur associée à l’abonnement. |
| costCenter | Centre de coûts associé à l’abonnement. |
| billingPeriodStartDate | Date de début de la période de facturation, comme indiqué sur la facture. |
| billingPeriodEndDate | Date de fin de la période de facturation, comme indiqué sur la facture. |
| servicePeriodStartDate | Date de début de la période d’évaluation pendant laquelle l’utilisation du service a été évaluée, dans le but de déterminer les frais. Les tarifs associés aux services Azure sont déterminés pour la période d’évaluation. |
| servicePeriodEndDate | Date de fin de la période pendant laquelle l’utilisation du service a été évaluée, dans le but de déterminer les frais. Les tarifs associés aux services Azure sont déterminés en fonction de la période d’évaluation. |
| date | Pour les données de consommation Azure, ce champ affiche la date d’utilisation évaluée. Pour une instance réservée, il affiche la date d’achat. Dans le cas des frais récurrents et ponctuels, tels que la Place de marché et le support, il affiche la date d’achat. |
| productID | Identificateur du produit qui a accumulé des frais par consommation ou achat. Il s’agit de la clé concaténée de productID et SKuID, comme indiqué dans l’Espace partenaires. |
| product | Nom du produit qui a accumulé des frais par consommation ou achat, comme indiqué dans la facture. |
| serviceFamily | Affiche la famille de services du produit acheté ou facturé. Par exemple, le stockage ou le calcul. |
| productOrderID | Identificateur de la ressource ou nom du plan Azure auquel appartient l’abonnement. Par exemple, « Azure Plan ». |
| productOrderName | Nom du plan Azure auquel appartient l’abonnement. Par exemple, « Azure Plan ». |
| consumedService | Service consommé (taxonomie héritée) tel qu’il est utilisé dans les détails sur l’utilisation d’EA hérités. |
| meterID | Identificateur contrôlé pour la consommation mesurée. |
| meterName | Identifie le nom du compteur associé à la consommation mesurée. |
| meterCategory | Identifie le service de niveau supérieur pour l’utilisation. |
| meterSubCategory | Définit le type ou la sous-catégorie de service Azure qui peuvent affecter le tarif. |
| meterRegion | Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement. |
| ID d’abonnement | Identificateur unique de l’abonnement Azure généré par Microsoft. |
| subscriptionName | Nom de l’abonnement Azure. |
| Terme | Affiche le terme de validité de l’offre. Par exemple, les instances réservées affichent 12 mois d’une période annuelle de l’instance réservée. Pour les achats ponctuels ou récurrents, cette période indique un mois pour SaaS, la Place de marché Azure et le support. Cela ne s’applique pas à la consommation d’Azure. |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Type de serveur de publication qui identifie le serveur de publication en tant que premier tiers, revendeur tiers ou agence tierce. |
| partNumber | Numéro de référence de l’instance réservée inutilisée et des services de la Place de marché Azure. |
| publisherName | Nom de l’éditeur du service, y compris Microsoft ou des serveurs de publication tiers. |
| reservationId | Identificateur de l’achat de l’instance réservée. |
| reservationName | Nom de l’instance réservée. |
| reservationOrderId | ID de commande de l’instance réservée. |
| frequency | Fréquence des paiements d’une instance réservée. |
| resourceGroup | Nom du groupe de ressources Azure utilisé pour la gestion des ressources du cycle de vie. |
| instanceID (ou) ResourceID | Identificateur de l’instance de ressource. |
| resourceLocation | Nom de l’emplacement de la ressource. |
| Location | Emplacement normalisé de la ressource. |
| effectivePrice | Prix unitaire effectif du service, dans la devise de tarification. Il est unique pour un produit, une famille de services, un compteur et une offre. Utilisé avec la tarification dans la grille tarifaire associée au compte de facturation. Lorsque la tarification est hiérarchisée ou qu’une quantité est incluse, cette option affiche le prix de consommation combiné. |
| Quantité | Quantité mesurée achetée ou consommée. Quantité du compteur utilisée au cours de la période de facturation. |
| unitOfMeasure | Identifie l’unité dans laquelle le service est facturé. Par exemple, des Go et des heures. |
| pricingCurrency | Devise définissant le prix unitaire. |
| billingCurrency | Devise définissant le coût facturé. |
| chargeType | Définit le type de frais représenté par le coût dans Azure Cost Management, comme un achat et un remboursement. |
| costinBillingCurrency | Coût étendu ou combiné avant l’application des taxes, dans la devise facturée. |
| CostInPricingCurrency | Coût étendu ou combiné avant l’application des taxes, dans la devise de tarification, à corréler avec les prix. |
| **costinUSD** | Estimation du coût du coût étendu ou combiné avant l’application des taxes, en USD. |
| **paygCostInBillingCurrency** | Affiche les coûts si la tarification est indiquée dans les prix de vente au détail. Indique les tarifs du paiement à l’utilisateur dans la devise de facturation. Disponible uniquement dans les étendues RBAC. |
| **paygCostInUSD** | Affiche les coûts si la tarification est indiquée dans les prix de vente au détail. Affiche les tarifs du paiement à l’utilisation, en USD. Disponible uniquement dans les étendues RBAC. |
| exchangeRate | Taux de change utilisé pour convertir un montant de la devise de tarification vers la devise de facturation. |
| exchangeRateDate | Date associée au taux de change utilisé pour convertir un montant de la devise de tarification vers la devise de facturation. |
| isAzureCreditEligible | Indique si le coût est éligible au paiement en crédits Azure. |
| serviceInfo1 | Champ hérité capturant les métadonnées facultatives propres au service. |
| serviceInfo2 | Champ hérité capturant les métadonnées facultatives propres au service. |
| additionalInfo | Métadonnées relatives au service. Par exemple, le type d’image d’une machine virtuelle. |
| tags | Balise que vous affectez au compteur. Utilisez des balises pour regrouper les enregistrements de facturation. Par exemple, vous pouvez utiliser des balises pour répartir les coûts par département qui utilise le compteur. |
| **partnerEarnedCreditRate** | Taux de remise appliqué en présence d’un crédit Partenaires basé sur l’accès au lien d’administrateur partenaire. |
| **partnerEarnedCreditApplied** | Indique si le crédit Partenaires a été appliqué. |


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

- Si vous n’avez pas encore effectué le premier guide de démarrage rapide relatif à Cost Management, lisez-le à partir de [Démarrer l’analyse des coûts](quick-acm-cost-analysis.md).
