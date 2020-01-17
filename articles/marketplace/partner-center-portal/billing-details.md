---
title: Facturation de la Place de marché commerciale | Place de marché Azure
description: Cet article couvre les rubriques liées au commerce dans la Place de marché commerciale.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 3f610dbc2c5cf052729857c09de1d437e52ac20a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475167"
---
# <a name="commercial-marketplace-billing"></a>Facturation de la Place de marché commerciale 

Cet article couvre les rubriques liées au commerce dans la Place de marché commerciale :

- [Options de publication de la Place de marché](#marketplace-publishing-options) 
- [Présentation générale des offres Transaction](#transact-general-overview)
- [Modèles de facturation des offres Transaction](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Options de publication de la Place de marché 

La Place de marché commerciale offre plusieurs options de publication pour les éditeurs.

### <a name="list-and-trial-publishing-options"></a>Options de publication Liste et Essai

Les éditeurs peuvent utiliser les options de publication Liste, Essai et BYOL (apportez votre propre licence) pour la promotion et l’achat par l’utilisateur. Avec ces options, Microsoft ne participe pas directement aux transactions de licence logicielle des éditeurs et ne facture rien sur ces transactions. Les éditeurs ont la responsabilité et la charge de tous les aspects des transactions de licence logicielle, y compris mais sans s’y limiter, la commande, le traitement, le contrôle, la tarification, la facturation, le paiement et la collecte. Avec les options de publication Liste et Essai, les éditeurs conservent 100 % du montant collecté auprès du client en paiement des frais de licence logicielle.

### <a name="transact-publishing-option"></a>Option de publication Transaction

En plus des options de publication Liste et Essai, les éditeurs disposent de l’option de publication Transaction. Cette option tire parti des fonctionnalités de transactions commerciales de Microsoft qui sont disponibles dans le Monde entier, et autorise Microsoft à héberger les transactions de la Place de marché dans le cloud pour le compte de l’éditeur.

## <a name="transact-general-overview"></a>Présentation générale des offres Transaction

Lorsque vous choisissez l’option de publication Transaction, Microsoft permet la vente de logiciels tiers et le déploiement de certains types d’offres dans l’abonnement Azure du client. Pour choisir le modèle de facturation et le type d’offre, l’éditeur doit prendre en compte la facturation des frais d’infrastructure et ses propres frais de licence logicielle. 

L’option de publication Transaction est actuellement disponible pour ces types d’offres : machines virtuelles, applications Azure et applications SaaS. 

![Offres Translation dans la Place de marché Azure](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure

#### <a name="for-virtual-machines-and-azure-applications"></a>Pour les offres Machines virtuelles et les Applications Azure

Pour les offres Machines virtuelles et Applications Azure, des frais d’utilisation de l’infrastructure Azure sont facturés sur l’abonnement Azure du client. Ils sont détaillés et présentés séparément des frais de licence logicielle du fournisseur sur la facture du client.

#### <a name="for-saas-apps"></a>Pour les offres Applications SaaS

Pour les offres Applications SaaS, l’éditeur doit regrouper les frais d’utilisation de l’infrastructure Azure et les frais de licence logicielle dans le même élément de coût. Ils font facturés au client selon un tarif fixe. L’utilisation de l’infrastructure Azure est présentée et facturée directement au partenaire. Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client. Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle. Les frais de licence logicielle ne sont pas comptabilisés ni basés sur la consommation.

## <a name="transact-billing-models"></a>Modèles de facturation des offres Transaction

Selon l’option Transaction choisie, les frais de licence logicielle de l’éditeur peuvent se présenter ainsi :

- *Gratuit* : aucuns frais ne sont facturés pour les licences logicielles.
- *BYOL (apportez votre propre licence)* : les frais facturables pour les licences logicielles sont gérés directement entre l’éditeur et le client. Microsoft transmet uniquement les frais d’utilisation de l’infrastructure Azure. (Pour les offres Machines virtuelles et Applications Azure uniquement.)
- *Paiement à l’utilisation* : les frais de licence logicielle sont facturés à un taux tarifaire par heure et par cœur (processeur virtuel) basé sur l’utilisation de l’infrastructure Azure. Ce Modèle s’applique uniquement aux offres Machines virtuelles et Applications Azure.
- *Tarifs des abonnements* : les frais de licence logicielle sont facturés mensuellement ou annuellement selon un tarif fixe ou par poste. Ce Modèle s’applique uniquement aux offres Applications SaaS et Applications Azure - Applications managées.
- *Essai gratuit de logiciel* : aucuns frais ne sont facturés pour les licences logicielles pendant une période de 30 ou 90 jours.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Options tarifaires Gratuit et BYOL (apportez votre propre licence)

Quand vous publiez une offre Transaction avec l’option Gratuit ou BYOL (apportez votre propre licence), Microsoft n’intervient pas pour faciliter la transaction des ventes pour vos frais de licence logicielle. Comme avec les options de publication Liste et Essai, l’éditeur conserve 100 % du montant des frais de licence logicielle.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Options de tarification Paiement à l'utilisation et Abonnement (en fonction du site)

Quand vous publiez une offre Transaction avec l’option de tarification Paiement à l’utilisation ou Abonnement, Microsoft fournit la technologie et les services nécessaires pour traiter les achats, retours et rétrofacturations des licences logicielles. Dans ce scénario, l’éditeur autorise Microsoft à agir en tant qu’agent. L’éditeur autorise Microsoft à faciliter la transaction de licence logicielle, tout en conservant sa qualité de vendeur, fournisseur, distributeur et concédant.

Microsoft permet aux clients de commander, d’acquérir sous licence et d’utiliser le logiciel de l’éditeur selon les conditions générales de la Place de marché commerciale de Microsoft et du contrat de licence utilisateur final de l’éditeur. Les éditeurs sont tenus de fournir leur contrat de licence utilisateur final ou de sélectionnez le [contrat Standard](https://docs.microsoft.com/azure/marketplace/standard-contract) lors de la création de l’offre.

### <a name="free-software-trials"></a>Essai logiciel gratuit

Dans les scénarios de publication d’offres Transaction, l’éditeur peut proposer gratuitement une licence logicielle pendant une période de 30 ou 90 jours. Cette remise n’inclut pas le coût de l’utilisation de l’infrastructure Azure occasionnée par l’utilisation de la solution du partenaire.

### <a name="private-offers"></a>Offres privées

Outre la sélection de types d’offres et de Modèles de facturation pour Monétiser une offre, les éditeurs peuvent proposer une offre privée, l’associer à des tarifs par transaction et négociés ou personnaliser les configurations. Les offres privées sont disponibles pour les trois options de publication Transaction.

Cette option tarifaire peut être supérieure ou inférieure à l'offre disponible publiquement. Les offres privées permettent d’appliquer une remise ou d’ajouter un supplément à une offre. Elles peuvent être proposées à un ou plusieurs clients en autorisant leur abonnement Azure au niveau de l’offre.

### <a name="examples"></a>Exemples

#### <a name="pay-as-you-go"></a>Paiement à l’utilisation

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

### <a name="bring-your-own-license-byol"></a>BYOL (apportez votre propre licence)

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

### <a name="saas-app-subscription"></a>Abonnement Application SaaS

Cette option doit être configurée pour la vente via Microsoft et peut être facturée mensuellement ou annuellement à un tarif fixe ou par utilisateur.

•   Si vous activez l’option Vendre via Microsoft pour une offre SaaS, vous obtenez la structure de coûts suivante.

|Coût de votre licence       | 100 $/mois  |
|--------------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    | Facturé directement à l’éditeur au lieu du client |
|*Microsoft facture au client le montant suivant :*    |  *100 $/mois (remarque : l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)*  |

* Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 80 $ à l’éditeur.
* Les partenaires ayant été qualifiés pour les Frais liés au service Place de marché réduits verront des frais de transaction réduits sur les offres SaaS de mai 2019 à juin 2020. Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 90 $ à l’éditeur.

|Microsoft facture  | 100 $/mois  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences <br> \* Microsoft paie 90 % de vos coûts de licence pour les applications SaaS qualifiées   |   80 $/mois <br> \* 90 $/mois    |
|Microsoft conserve 20 % des revenus générés par les licences <br> \* Microsoft conserve 10 % de vos coûts de licence pour les applications SaaS qualifiées  |  20 $/mois <br> \* 10 $     |

**Frais liés au service Place de marché réduits** : Pour certains produits SaaS que vous publiez sur notre Place de marché commerciale, Microsoft réduit ses frais liés au service Place de marché de 20 % (comme décrit dans le Contrat d'éditeur Microsoft) à 10 %.  Afin que votre produit soit qualifié, au moins un de vos produits doit avoir été désigné par Microsoft comme Prêt à la co-vente IP ou Priorité à la co-vente IP. Pour recevoir ces frais liés au service Place de marché réduits pour le mois, l’éligibilité doit être respectée au moins cinq (5) jours ouvrés avant la fin du mois précédent. Les frais liés au service Place de marché réduits ne s’appliquent pas aux machines virtuelles, aux applications managées ou à tout autre produit, disponible via notre Place de marché commerciale.  Les frais liés au service Place de marché réduits sont disponibles pour les offres qualifiées, avec frais de licence collectés par Microsoft entre le 1er mai 2019 et le 30 juin 2020.  Au-delà de cette date, les frais liés au service Place de marché réduits retrouveront leur montant initial.
