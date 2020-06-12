---
title: Fonctionnalités de transaction de la place de marché commerciale de Microsoft
description: Cet article décrit les considérations sur les prix, la facturation et le paiement pour l’option de transaction de la place de marché commerciale.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 8818b06e84f6ffe05398087c4aaeba282adc8b2e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298208"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Fonctionnalités de transaction de la place de marché commerciale

## <a name="transactions-by-publishing-option"></a>Transactions par option de publication

Il incombe à l’éditeur ou à Microsoft de gérer les transactions de licences logicielles pour les offres de la place de marché commerciale. L’option de publication que vous choisissez pour votre offre détermine qui gère la transaction. Pour connaître la disponibilité de chaque option de publication et en obtenir les explications, consultez [Déterminer votre option de publication](./determine-your-listing-type.md#choose-a-publishing-option).

### <a name="list-trial-and-byol-publishing-options"></a>Options de publication Liste, Essai et BYOL

Les éditeurs disposant de fonctionnalités de transactions commerciales peuvent choisir les options de publication Liste, Essai et BYOL (apportez votre propre licence) dans un but promotionnel et de vente. Avec ces options, Microsoft ne participe pas directement aux transactions de licence logicielle des éditeurs et ne facture rien sur ces transactions. Les éditeurs ont la responsabilité et la charge de tous les aspects des transactions de licence logicielle, y compris mais sans s’y limiter, la commande, le traitement, le contrôle, la tarification, la facturation, le paiement et la collecte. Avec les options de publication Liste et Essai, les éditeurs conservent 100 % du montant collecté auprès du client en paiement des frais de licence logicielle.

### <a name="transact-publishing-option"></a>Option de publication Transaction

L’option de publication Transaction tire parti des fonctionnalités de transactions commerciales de Microsoft et fournit une expérience de bout en bout, de la découverte et l’évaluation à l’achat et l’implémentation. Les offres de transaction sont facturées en fonction d’un abonnement Microsoft existant ou sur une carte de crédit, ce qui permet à Microsoft d’héberger des transactions de la place de marché dans le cloud pour le compte de l’éditeur.

Vous choisissez l’option de transaction au moment de la création d’une offre dans l’Espace partenaires. Dans la page **Configuration de l’offre**, sous **Détails de la configuration**, sélectionnez « Oui, je souhaite vendre via Microsoft et que Microsoft héberge les transactions en mon nom ». Cette option s’affiche seulement si la transaction est disponible pour votre type d’offre.

## <a name="transact-overview"></a>Présentation de l’option Transaction

Lorsque vous choisissez l’option de publication Transaction, Microsoft permet la vente de logiciels tiers et le déploiement de certains types d’offres dans l’abonnement Azure du client. Pour choisir le modèle de facturation et le type d’offre, l’éditeur doit prendre en compte la facturation des frais d’infrastructure et ses propres frais de licence logicielle.

L’option de publication Transaction est actuellement disponible pour ces types d’offres :

- Machines virtuelles
- Applications Azure
- Applications SaaS

### <a name="billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure

Pour les **Machines virtuelles et les Applications Azure**, des frais d’utilisation de l’infrastructure Azure sont facturés sur l’abonnement Azure du client. Ils sont détaillés et présentés séparément des frais de licence logicielle du fournisseur sur la facture du client.

Pour les **Applications SaaS**, l’éditeur doit regrouper les frais d’utilisation de l’infrastructure Azure et les frais de licence logicielle dans le même élément de coût.  Ils font facturés au client selon un tarif fixe. L’utilisation de l’infrastructure Azure est présentée et facturée directement au partenaire. Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client. Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle. Les frais de licence logicielle ne sont pas comptabilisés ni basés sur la consommation.

## <a name="transact-billing-models"></a>Modèles de facturation des offres Transaction

Selon l’option Transaction choisie, les frais de licence logicielle se présentent ainsi :

- **Gratuit** : aucuns frais ne sont facturés pour les licences logicielles.
- **BYOL (apportez votre propre licence)**  : les frais facturables pour les licences logicielles sont gérés directement entre l’éditeur et le client. Microsoft transmet uniquement les frais d’utilisation de l’infrastructure Azure. Cela s’applique uniquement aux offres Machines virtuelles et Applications Azure.
- **Paiement à l’utilisation** : des frais de licence logicielle sont facturés à un taux tarifaire par heure et par cœur (processeur virtuel) basé sur l’utilisation de l’infrastructure Azure. Cela s’applique uniquement aux offres Machines virtuelles et Applications Azure.
- **Tarification par abonnement** : les frais de licence logicielle sont facturés mensuellement ou annuellement selon un tarif fixe ou par poste. Cela s’applique uniquement aux offres Applications SaaS (mensuelle ou annuelle) et Applications Azure - Applications gérées uniquement (mensuelle).
- **Essai logiciel gratuit** : aucuns frais ne sont facturés pour les licences logicielles pendant une période de 30 ou 90 jours.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Options tarifaires Gratuit et BYOL (apportez votre propre licence)

Quand vous publiez une offre Transaction avec l’option Gratuit ou BYOL (apportez votre propre licence), Microsoft n’intervient pas pour faciliter la transaction des ventes pour vos frais de licence logicielle. Comme avec les options de publication Liste et Essai, l’éditeur conserve 100 % du montant des frais de licence logicielle.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Options de tarification Paiement à l'utilisation et Abonnement (en fonction du site)

Quand vous publiez une offre Transaction avec l’option de tarification Paiement à l’utilisation ou Abonnement, Microsoft fournit la technologie et les services nécessaires pour traiter les achats, retours et rétrofacturations des licences logicielles. Dans ce scénario, l’éditeur autorise Microsoft à agir en tant qu’agent. L’éditeur autorise Microsoft à faciliter la transaction de licence logicielle, tout en conservant sa qualité de vendeur, fournisseur, distributeur et concédant.

Microsoft permet aux clients de commander, d’acquérir sous licence et d’utiliser votre logiciel selon les conditions générales de la place de marché commerciale Microsoft et de votre contrat de licence utilisateur final. Vous êtes tenu de fournir votre contrat de licence utilisateur final ou de sélectionner le [contrat Standard](./standard-contract.md) lors de la création de l’offre.

### <a name="free-software-trials"></a>Essai logiciel gratuit

Dans les scénarios de publication d’offres Transaction, vous pouvez proposer gratuitement une licence logicielle pendant une période de 30 ou 90 jours. Cette remise n’inclut pas le coût de l’utilisation de l’infrastructure Azure occasionnée par l’utilisation de la solution du partenaire.

### <a name="private-offers"></a>Offres privées

Outre la sélection de types d’offres et de modèles de facturation pour monétiser une offre, vous pouvez proposer une offre privée, l’associer à des tarifs par transaction négociés ou personnaliser les configurations. Les offres privées sont disponibles pour les 3 options de publication Transaction.

Cette option tarifaire peut être supérieure ou inférieure à l'offre disponible publiquement. Les offres privées permettent d’appliquer une remise ou d’ajouter un supplément à une offre. Elles peuvent être proposées à un ou plusieurs clients en autorisant leur abonnement Azure au niveau de l’offre.

### <a name="examples"></a>Exemples

**Paiement à l’utilisation** 

Le paiement à l’utilisation a la structure de coûts suivante :

|Coût de votre licence  | 1 $/heure   |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
|*Microsoft facture au client le montant suivant :*    |  *1,14 $/heure*       |
||

Dans ce scénario, Microsoft facture 1,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

|Microsoft facture  | 1,14 $/heure  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences|   0,80 $/heure     |
|Microsoft conserve 20 % des revenus générés par les licences  |  0,20 $/heure       |
|Microsoft conserve 100 % des revenus générés par l’utilisation d’Azure | 0,14 $/heure |
||

**BYOL (apportez votre propre licence)**

L’option BYOL a la structure de coûts suivante :

|Coût de votre licence  | Frais de licence négociés et facturés par vos soins  |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
|*Microsoft facture au client le montant suivant :*    |  *0,14 $/heure*       |
||

Dans ce scénario, Microsoft facture 0,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

|Microsoft facture  | 0,14 $/heure  |
|---------|---------|
|Microsoft conserve les revenus générés par l’utilisation Azure    |   0,14 $/heure     |
|Microsoft conserve 0 % des revenus générés par les licences   |  0 $/heure       |
||

**Abonnement Application SaaS**

Cette option doit être configurée pour la vente via Microsoft et peut être facturée mensuellement ou annuellement à un tarif fixe ou par utilisateur. Si vous activez l’option **Vendre via Microsoft** pour une offre SaaS, vous obtenez la structure de coûts suivante :

| Coût de votre licence       | 100 $/mois  |
|--------------|---------|
| Coût d’utilisation Azure (D1/1 cœur)    | Facturé directement à l’éditeur au lieu du client |
| *Microsoft facture au client le montant suivant :*    |  *100 $/mois (l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)*  |
||

Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 80 $ à l’éditeur.

Les partenaires ayant été qualifiés pour les Frais liés au service Place de marché réduits verront des frais de transaction réduits sur les offres SaaS de mai 2019 à juin 2020.

Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 90 $ à l’éditeur :

|Microsoft facture  | 100 $/mois  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences <br> \* Microsoft paie 90 % de vos coûts de licence pour les applications SaaS qualifiées   |   80 $/mois <br> \* 90 $/mois    |
|Microsoft conserve 20 % des revenus générés par les licences <br> \* Microsoft conserve 10 % de vos coûts de licence pour les applications SaaS qualifiées  |  20 $/mois <br> \* 10 $     |

Pour certains produits SaaS que vous publiez sur notre Place de marché commerciale, Microsoft réduit ses **frais liés au service de la Place de marché** de 20 % (comme décrit dans le Contrat d’éditeur Microsoft) à 10 %. Pour que votre offre soit qualifiée, au moins une de vos offres doit avoir été désignée par Microsoft comme étant Prête à la co-vente IP ou Priorité à la co-vente IP. Pour recevoir ces frais réduits liés au service de la Place de marché pour le mois, l’éligibilité doit être respectée au moins 5 jours ouvrés avant la fin du mois précédent. Les frais réduits liés au service de la Place de marché ne s’appliquent pas aux machines virtuelles, aux applications managées ou à tout autre produit, disponible via la place de marché commerciale. Les frais réduits sont disponibles pour les offres qualifiées, avec frais de licence collectés par Microsoft entre le 1er mai 2019 et le 30 juin 2020. Au-delà de cette date, les frais retrouvent leur montant initial.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Tarification, paiement, facturation et collecte côté client

**Facturation et paiement** : vous pouvez appliquer la méthode de facturation par défaut du client pour présenter les frais de licence logicielle avec abonnement ou paiement à l’utilisation.

**Contrat Entreprise** : si la méthode de facturation par défaut du client est un Contrat Entreprise Microsoft, les frais de licence logicielle sont facturés selon cette méthode et présentés de manière détaillée et séparée des coûts propres à l’utilisation d’Azure.

**Cartes de crédit et facture mensuelle** : les clients peuvent également payer à l’aide d’une carte de crédit et d’une facture mensuelle. Dans ce cas, vos frais de licence logicielle sont facturés comme dans le scénario du Contrat Entreprise, c’est-à-dire de manière détaillée et séparée des coûts de l’utilisation d’Azure.

**Crédits gratuits et engagement financier** : certains clients choisissent de prépayer l’utilisation d’Azure avec un engagement financier dans le Contrat Entreprise, ou ont obtenu des crédits gratuits à utiliser avec Azure. Ces crédits peuvent servir à payer l’utilisation d’Azure, mais pas les frais de licence logicielle de l’éditeur.

**Facturation et collections** : les licences logicielles des éditeurs sont facturées avec la méthode de facturation choisie par le client et selon le calendrier de facturation établi. Les clients sans Contrat Entreprise sont facturés au mois pour les licences logicielles de la Place de marché. Les clients ayant un Contrat Entreprise sont facturés au mois, mais reçoivent une seule facture par trimestre.

Quand les modèles tarifaires Abonnement ou Paiement à l'utilisation sont sélectionnés, Microsoft agit en tant qu’agent de l’éditeur et est responsable de tous les aspects de la facturation, du paiement et de la collecte.

### <a name="publisher-payout-and-reporting"></a>Paiement de l’éditeur et rapports

Les frais de licence logicielle collectés par Microsoft en tant qu’agent sont soumis à des frais de transaction de 20 %, sauf indication contraire, et sont déduits du moment payé à l’éditeur.

Les clients achètent généralement avec un Contrat Entreprise, ou par carte de crédit dans le cadre d’un contrat de paiement à l’utilisation. Le type de contrat détermine la tarification, la facturation, la collecte et le calendrier de paiement.

>[!NOTE]
>Tous les rapports et insights associés à l’option de publication Transaction sont disponibles dans la section Analytiques de l’Espace partenaires.

#### <a name="billing-questions-and-support"></a>Questions et support sur la facturation

Pour obtenir plus d’informations et connaître les règles juridiques, consultez le [Contrat d’éditeur](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponible dans l’Espace partenaires).

Pour obtenir de l’aide sur les questions de facturation, contactez le [support de l’éditeur de la place de marché commerciale](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Exigences relatives aux offres Transaction

Cette section décrit la configuration requise pour les différents types d’offres.

### <a name="requirements-for-all-offer-types"></a>Exigences applicables à tous les types d’offres

- Un compte Microsoft et des informations financières sont requis pour l’offre de publication Transaction, quel que soit son modèle de tarification.
- Les informations financières obligatoires comprennent un compte de paiement et un profil fiscal.

Pour plus d’informations sur la configuration de ces comptes, consultez [Gérer votre compte Place de marché commerciale dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account).

### <a name="requirements-for-specific-offer-types"></a>Exigences applicables à certains types d’offres

L’option de publication Transaction est uniquement disponible pour les types d’offres de la Place de marché suivants :

- **Machine virtuelle** : choisissez entre le modèle tarifaire Gratuit, BYOL (apportez votre propre licence) et Paiement à l’utilisation, et présentez les frais comme des références SKU définies au niveau de l’offre. Sur la facture Azure du client, Microsoft présente les frais de licence logicielle de l’éditeur séparément des frais d’infrastructure Azure sous-jacents. Les frais d’utilisation de l’infrastructure Azure sont occasionnés par l’utilisation du logiciel de l’éditeur.
- **Applications Azure : Modèle de solution ou application gérée** : vous devez provisionner une ou plusieurs machines virtuelles et transmettre le total à payer pour chaque machine virtuelle. Pour les applications managées dans un plan unique, il est possible de sélectionner un abonnement mensuel fixe comme modèle tarifaire à la place de la tarification par machine virtuelle. Dans certains cas, les frais d’utilisation de l’infrastructure Azure sont dissociés des frais de licence logicielle, mais ils sont présentés au client sur la même facture. Toutefois, si vous configurez une offre d'application managée pour les frais d'infrastructure ISV, les ressources Azure sont facturées à l'éditeur et le client se voit présenter un tarif fixe incluant le coût de l'infrastructure, des licences logicielles et des services de gestion.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les conditions d’éligibilité dans la section relative aux options de publication par type d’offre pour finaliser la sélection et la configuration de votre offre.
- Passez en revue les modèles de publication par vitrine pour obtenir des exemples sur la manière dont votre solution correspond à un type d’offre et à une configuration.
