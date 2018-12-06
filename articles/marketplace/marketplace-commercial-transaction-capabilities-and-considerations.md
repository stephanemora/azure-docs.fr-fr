---
title: Considérations et fonctionnalités relatives aux transactions commerciales ‎dans la Place de marché | Azure
description: Cet article expose diverses considérations ayant trait à la tarification, la facturation et le paiement pour le type d’offre Transaction.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: 32198387be680fd01558e900e4c438cde2cdc5b1
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52266289"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Considérations et fonctionnalités relatives aux transactions commerciales ‎dans la Place de marché Azure

Les options de publication de la Place de marché Azure constituent des moyens uniques pour mettre les fournisseurs de logiciels et de services cloud en relation avec les clients. Cet article comporte les rubriques suivantes sur les transactions commerciales dans la Place de marché Azure :

* Options de publication de la Place de marché
* Présentation générale des offres Transaction
* Modèles de facturation des offres Transaction
* Exigences relatives aux offres Transaction

## <a name="marketplace-publishing-options"></a>Options de publication de la Place de marché

Les éditeurs disposent des options de publication suivantes dans la Place de marché Azure.

### <a name="list--trial-publishing-options"></a>Options de publication Liste et Essai

Dans la Place de marché Azure, les éditeurs peuvent utiliser les options de publication Liste et Essai pour la promotion et l’achat par l’utilisateur. Avec les options de publication Liste et Essai, Microsoft ne participe pas directement aux transactions de licence logicielle des éditeurs et ne facture aucuns frais sur ces transactions. Les éditeurs ont la responsabilité et la charge de tous les aspects des transactions de licence logicielle, y compris mais sans s’y limiter, la commande, le traitement, le contrôle, la tarification, la facturation, le paiement et la collecte. Avec les options de publication Liste et Essai, les éditeurs conservent 100 % du montant collecté auprès du client en paiement des frais de licence logicielle. 

### <a name="transact-publishing-option"></a>Option de publication Transaction

En plus des options de publication Liste et Essai, les éditeurs disposent de l’option de publication Transaction dans la Place de marché Azure.   Elle tire parti des fonctionnalités de transactions commerciales de Microsoft qui sont disponibles dans le monde entier. Elle autorise Microsoft à gérer les transactions de la Place de marché dans le cloud pour le compte de l’éditeur.

## <a name="transact-general-overview"></a>Présentation générale des offres Transaction

Quand vous choisissez l’option de publication Transaction, Microsoft permet la vente et le déploiement de logiciels tiers dans l’abonnement Azure du client. Pour choisir le modèle de facturation et le type d’offre appropriés dans la Place de marché Azure, l’éditeur doit prendre en compte la facturation des frais d’infrastructure Azure et de ses propres frais de licence logicielle. 

L’option de publication Transaction dans la Place de marché Azure est actuellement disponible pour ces types d’offres : Machines virtuelles, Applications Azure et Applications SaaS.

![[Transactions commerciales dans la Place de marché Azure]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure

**Pour les offres Machines virtuelles et Applications Azure**

Pour les offres Machines virtuelles et Applications Azure, les frais d’utilisation de l’infrastructure Azure sont facturés sur l’abonnement Azure du client.  Les frais d’utilisation de l’infrastructure sont détaillés et présentés séparément des frais de licence logicielle du fournisseur sur la facture du client.

**Pour les offres Applications SaaS**

Pour les offres Applications SaaS, l’éditeur doit regrouper les frais d’utilisation de l’infrastructure Azure et les frais de licence logicielle dans le même élément de coût.  Ils font facturés au client selon un tarif mensuel fixe. L’utilisation de l’infrastructure Azure est présentée et facturée directement au partenaire.  Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client.  Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle.  Les frais de licence logicielle ne sont pas comptabilisés ni basés sur la consommation.

## <a name="transact-billing-models"></a>Modèles de facturation des offres Transaction

Selon l’option Transaction choisie, les frais de licence logicielle de l’éditeur peuvent se présenter comme suit :  

* Gratuit : aucuns frais ne sont facturés pour les licences logicielles. 

* BYOL (apportez votre propre licence) : les frais facturables pour les licences logicielles sont gérés directement entre l’éditeur et le client. Microsoft transmet uniquement les frais d’utilisation de l’infrastructure Azure. (Pour les offres Machines virtuelles et Applications Azure uniquement.)

* Paiement à l’utilisation : des frais de licence logicielle sont facturés à un taux tarifaire par heure et par cœur (processeur virtuel) basé sur l’utilisation de l’infrastructure Azure. Cela s’applique aux offres Machines virtuelles et Applications Azure uniquement.

* Abonnement (en fonction du site) : les frais de licence logicielle sont facturés selon un tarif mensuel fixe.  Cela s’applique uniquement aux offres Applications SaaS et Applications Azure – Applications managées.

* Essai logiciel gratuit : aucuns frais ne sont facturés pour les licences logicielles pendant une période de 30 ou 90 jours.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Options tarifaires Gratuit et BYOL (apportez votre propre licence)

Quand vous publiez une offre Transaction avec l’option Gratuit ou BYOL (apportez votre propre licence), Microsoft n’intervient pas pour faciliter la transaction des ventes pour vos frais de licence logicielle. Comme avec les options de publication Liste et Essai, l’éditeur conserve 100 % du montant des frais de licence logicielle. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Options de tarification Paiement à l'utilisation et Abonnement (en fonction du site)

Quand vous publiez une offre Transaction avec l’option de tarification Paiement à l’utilisation ou Abonnement, Microsoft fournit la technologie et les services nécessaires pour traiter les achats, retours et rétrofacturations des licences logicielles. Dans ce scénario, l’éditeur autorise Microsoft à agir en tant qu’agent. L’éditeur autorise Microsoft à faciliter la transaction de licence logicielle, tout en conservant sa qualité de vendeur, fournisseur, distributeur et concédant.

Microsoft permet aux clients de commander, acquérir une licence et utiliser le logiciel de l’éditeur selon les conditions générales de la Place de marché Azure et du contrat de licence utilisateur final de l’éditeur (voir le portail Cloud Partner). Les éditeurs sont tenus de fournir leur contrat de licence utilisateur final avec l’offre publiée dans la Place de marché.

Les commandes effectuées par le biais de la Place de marché sont facturées sur l’abonnement Azure du client sous la forme d’une seule facture, selon la même méthode de facturation que celle des coûts d’infrastructure Azure du client. Les clients peuvent choisir la méthode de facturation et l’instrument de paiement qu’ils utilisent par défaut pour la facturation de leur abonnement Azure.

### <a name="free-software-trials"></a>Essai logiciel gratuit

Dans les scénarios de publication d’offres Transaction, l’éditeur peut proposer gratuitement une licence logicielle pendant une période de 30 ou 90 jours. Cette remise n’inclut pas le coût de l’utilisation de l’infrastructure Azure occasionnée par l’utilisation de la solution du partenaire.

### <a name="private-offers"></a>Offres privées

Outre la sélection de types d’offres et de modèles de facturation pour monétiser une offre, les éditeurs peuvent proposer une version privée de l’offre de solution, l’associer à des tarifs par transaction négociés, et personnaliser les configurations à l’aide d’une image personnalisée. Les offres privées sont disponibles pour les trois options de publication Transaction.

Cette option tarifaire peut être supérieure ou inférieure au tarif affiché publiquement.  Les offres privées permettent d’appliquer une remise ou d’ajouter un supplément à une offre. Elles peuvent être proposées à un ou plusieurs clients en autorisant leur abonnement Azure au niveau de l’offre.

### <a name="examples"></a>Exemples

**Paiement à l’utilisation** 

* Si vous activez l’option Paiement à l’utilisation, vous obtenez la structure de coûts suivante.

|Coût de votre licence  | 1 $/heure  |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
|*Microsoft facture au client le montant suivant :*    |  *1,14 $/heure*       |

* Dans ce scénario, Microsoft facture 1,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

|Microsoft facture  | 1,14 $/heure  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences|   0,80 $/heure     |
|Microsoft conserve 20 % des revenus générés par les licences  |  0,20 $/heure       |
|Microsoft conserve 100 % des revenus générés par l’utilisation d’Azure | 0,14 $/heure |

**BYOL (apportez votre propre licence)**

* Si vous activez l’option BYOL, vous obtenez la structure de coûts suivante.

|Coût de votre licence  | Frais de licence négociés et facturés par vos soins  |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
|*Microsoft facture au client le montant suivant :*    |  *0,14 $/heure*       |

* Dans ce scénario, Microsoft facture 0,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

|Microsoft facture  | 0,14 $/heure  |
|---------|---------|
|Microsoft conserve les revenus générés par l’utilisation Azure    |   0,14 $/heure     |
|Microsoft conserve 0 % des revenus générés par les licences   |  0 $/heure       |

**Abonnement Application SaaS (Vendre via Azure)**

Cette option doit être configurée pour la vente via Microsoft et peut être facturée selon un ou plusieurs plans tarifaires mensuels fixes définis au niveau de l’offre.

* Si vous activez l’option Vendre via Azure, vous obtenez la structure de coûts suivante.

|Coût de votre licence       | 100 $/mois  |
|--------------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    | Facturé directement à l’éditeur au lieu du client |
|*Microsoft facture au client le montant suivant :*    |  *100 $/mois (remarque : l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)*  |

* Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 80 $ à l’éditeur.

|Microsoft facture  | 100 $/mois  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences    |   80 $/mois     |
|Microsoft conserve 20 % des revenus générés par les licences   |  20 $/mois       |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Tarification, paiement, facturation et collecte côté client

**Facturation et paiement**

L’éditeur peut choisir la méthode de facturation par défaut du client pour présenter les frais de licence logicielle avec abonnement ou paiement à l’utilisation.

**Contrat Entreprise** 

Si la méthode de facturation par défaut du client est un Contrat Entreprise Microsoft, vos frais de licence logicielle seront facturés selon cette méthode et présentés de manière détaillée et séparée des coûts propres à l’utilisation d’Azure.

**Cartes de crédit et facture mensuelle** 

Les clients peuvent également payer à l’aide d’une carte de crédit et d’une facture mensuelle. Dans ce cas, vos frais de licence logicielle sont facturés comme dans le scénario du Contrat Entreprise, c’est-à-dire de manière détaillée et séparée des coûts de l’utilisation d’Azure.

Exemple d’un client qui fait un achat avec une carte de crédit :

|Description    |    Date  |
|----------|----------|
|Période de commande   | 15 août 2018 au 30 août 2018 |
|Échéance (mois)   | 30 août 2018 |
|Date de facturation | 1er septembre 2018 |
|Date de paiement du client | 1er septembre 2018 |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | 1er septembre 2018 au 30 septembre 2018 |
|Début de la période de collecte | 1er septembre 2018 |
|Fin de la période de collecte (30 jours au maximum) | 30 septembre 2018 |
|Date de calcul du paiement (tous les mois le 15) | 1er octobre 2018 |
|Date de paiement | 15 octobre 2018 |

Exemple d’un client qui fait un achat avec un Contrat Entreprise :  
|Description    |    Date  |
|----------|----------|
|Période de commande | 15 août 2018 au 30 août 2018 |
|Échéance (trimestre) | 30 septembre 2018 |
|Date de facturation | 15 octobre 2018 |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | n/a |
|Début de la période de collecte | 15 octobre 2018 |
|Fin de la période de collecte (90 jours au maximum) | 15 janvier 2018 |
|Date de paiement du client | 30 décembre 2018 |
|Date de calcul du paiement (tous les mois le 15) | 15 janvier 2018 |
|Date de paiement | 15 février 2019 |

**Crédits gratuits et engagement financier** 

Certains clients choisissent de prépayer l’utilisation d’Azure avec un engagement monétaire dans le Contrat Entreprise, ou ont obtenu des crédits gratuits à utiliser avec Azure. Ces crédits peuvent servir à payer l’utilisation d’Azure, mais pas les frais de licence logicielle de l’éditeur.

**Facturation et collecte** 

Les licences logicielles des éditeurs sont facturées avec la méthode de facturation choisie par le client et selon le calendrier de facturation établi. Les clients sans Contrat Entreprise sont facturés au mois pour les licences logicielles de la Place de marché. Les clients ayant un Contrat Entreprise sont facturés au mois, mais reçoivent une seule facture par trimestre.

Quand les modèles tarifaires Abonnement ou Paiement à l'utilisation sont sélectionnés, Microsoft agit en tant qu’agent de l’éditeur et est responsable de tous les aspects de la facturation, du paiement et de la collecte.

### <a name="publisher-payout-and-reporting"></a>Paiement de l’éditeur et rapports

* Les frais de licence logicielle collectés par Microsoft en tant qu’agent sont soumis à des frais de transaction de 20 %, sauf indication contraire, et sont déduits du moment payé à l’éditeur.

* Les clients achètent généralement avec un Contrat Entreprise, ou par carte de crédit dans le cadre d’un contrat de paiement à l’utilisation. Le type de contrat détermine la tarification, la facturation, la collecte et le calendrier de paiement.

>[!NOTE] 
>Tous les rapports et insights associés à l’option de publication Transaction sont disponibles dans la section Insights du portail Cloud Partner.

#### <a name="billing-questions-and-support"></a>Questions et support sur la facturation

Pour obtenir plus d’informations et connaître les règles juridiques, consultez le [Contrat d’éditeur](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponible dans le portail Cloud Partner).

Pour obtenir de l’aide sur des problèmes de facturation, [créez un incident de support](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) et choisissez Machines virtuelles ou Applications web (également appelés Applications SaaS) selon le type d’offre utilisé.

## <a name="transact-requirements"></a>Exigences relatives aux offres Transaction

Cette section expose les exigences relatives aux différents types d’offres Transaction.

### <a name="requirements-for-all-offer-types"></a>Exigences applicables à tous les types d’offres

**Compte du Centre de développement et compte Microsoft** 

* Un compte du Centre de développement et un compte Microsoft sont requis pour l’offre de publication Transaction, quel que soit le modèle de tarification de l’offre.
* Le compte du Centre de développement contient tous les détails financiers dont Microsoft a besoin pour collecter les frais auprès du client en tant qu’agent de l’éditeur, et payer ensuite l’éditeur.
* Vous pouvez utiliser les mêmes informations d’identification pour votre compte professionnel et le compte Microsoft. Toutefois, le compte du Centre de développement est un compte indépendant du compte du portail Cloud Publisher. Pour utiliser l’option de publication Transaction, l’éditeur doit suivre le processus d’inscription d’un compte du Centre de développement, en plus de s’inscrire pour accéder au portail Cloud Partner.

*Pour plus d’informations sur la création de ces comptes, consultez [Devenir un éditeur sur la Place de marché dans le cloud](https://docs.microsoft.com/azure/marketplace/become-publisher).*

### <a name="requirements-for-specific-offer-types"></a>Exigences applicables à certains types d’offres

L’option de publication Transaction est uniquement disponible pour les types d’offres de la Place de marché suivants : 

**Machine virtuelle** 

Choisissez entre le modèle tarifaire Gratuit, BYOL (apportez votre propre licence) et Paiement à l’utilisation, et présentez les frais comme des références SKU définies au niveau de l’offre. Sur la facture Azure du client, Microsoft présente les frais de licence logicielle de l’éditeur séparément des frais attribuables à l’utilisation de l’infrastructure Azure. Les frais d’utilisation de l’infrastructure Azure sont occasionnés par l’utilisation du logiciel de l’éditeur.

**Applications Azure : modèle de solution ou application managée** 

Vous devez provisionner une ou plusieurs machines virtuelles et transmettre le total à payer pour chaque machine virtuelle. Pour les applications managées dans un plan unique, il est possible de sélectionner un abonnement mensuel fixe comme modèle tarifaire à la place de la tarification par machine virtuelle. Dans les deux cas, les frais d’utilisation de l’infrastructure Azure sont dissociés des frais de licence logicielle, mais ils sont présentés au client sur la même facture.

## <a name="next-steps"></a>Étapes suivantes

* Passez en revue les conditions d’éligibilité dans la section relative aux options de publication par type d’offre pour finaliser la sélection et la configuration de votre offre.
* Passez en revue les modèles de publication par vitrine pour obtenir des exemples sur la manière dont votre solution correspond à un type d’offre et à une configuration.
* Devenez éditeur sur la Place de marché, et connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com) pour créer et configurer votre offre.
