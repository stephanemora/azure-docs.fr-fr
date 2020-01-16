---
title: Mettre à jour une offre de machine virtuelle existante dans la Place de marché Azure
description: Explique comment mettre à jour une offre de machine virtuelle existante dans Place de marché Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: evansma
ms.openlocfilehash: 1ba2abb3fbeb1d08ed780669fb94a2ef83cbfb1b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934241"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Mettre à jour une offre de machine virtuelle existante dans Place de marché Azure

Cet article vous présente en détail comment mettre à jour votre offre de machine virtuelle (VM) dans le [Portail Microsoft Cloud Partner](https://cloudpartner.azure.com/), puis republier l’offre. 

Plusieurs raisons peuvent motiver la mise à jour d’une offre :

-  Ajouter une nouvelle version image de machine virtuelle à des références SKU existantes
-  Modifier les régions dans lesquelles une référence SKU est disponible
-  Ajouter de nouvelles références SKU
-  Mettre à jour les métadonnées de Place de marché pour l’offre ou des références SKU individuelles
-  Mettre à jour la tarification des offres avec paiement à l’utilisation

Le portail contient les fonctions **Comparer** et **Historique** qui vous aident à apporter ces modifications.  

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](../../cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Modifications non autorisées apportées à l’offre ou à la référence SKU de machine virtuelle

Vous ne pouvez pas modifier certains attributs d’une offre ou d’une référence SKU de machine virtuelle une fois l’offre publiée dans Place de marché Microsoft Azure, principalement :

-  **ID d’offre** et **ID de l’éditeur** de l’offre
-  **ID de référence (SKU)** de références (SKU) existantes
-  Nombre de disques de données de références SKU existantes
-  Modifications du modèle de facturation/licence apportées à des références (SKU) existantes
-  Augmentation de prix d’une référence SKU publiée


## <a name="common-update-operations"></a>Opérations de mise à jour courantes

Vous pouvez modifier de nombreuses caractéristiques sur une offre de machine virtuelle, mais les opérations suivantes sont les plus courantes.

### <a name="update-the-vm-image-version-for-a-sku"></a>Mettre à jour la version image de machine virtuelle pour une référence SKU

Il est courant qu’une image de machine virtuelle soit régulièrement mise à jour avec des correctifs de sécurité, des fonctionnalités supplémentaires et ainsi de suite.  Dans de tels scénarios, vous pouvez mettre à jour l’image de la machine virtuelle associée à votre référence SKU à l’aide de la procédure suivante :

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre à mettre à jour.

3.  Dans l’onglet **Références**, cliquez sur la référence SKU associée à l’image de la machine virtuelle à mettre à jour.

4.  Sous **Version disque**, cliquez sur **+Nouvelle version disque** pour ajouter une nouvelle image de machine virtuelle.

5.  Fournissez la **version disque** des nouvelles images de machine virtuelle. La version disque doit suivre le format [version sémantique](https://semver.org/). Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Vérifiez que la nouvelle version indiquée est ultérieure à toutes les versions précédentes. Sinon, après la republication, la nouvelle version ne s’affiche pas dans le portail ou dans Place de marché Azure.

6.  Dans le champ **URL de disque dur virtuel de système d’exploitation**, saisissez [l’URI de signature d’accès partagé (SAS)](./cpp-get-sas-uri.md) créé pour le disque dur virtuel du système d’exploitation. 

    > [!WARNING] 
    > Le nombre de disques de données ne peut pas varier entre différentes versions de la référence SKU. Si des versions précédentes avaient des disques de données configurés, cette nouvelle version doit avoir le même nombre de disques de données.

7.  Cliquez sur **Publier** pour démarrer le flux de travail et publier la nouvelle version de votre machine virtuelle vers Place de marché Azure.


### <a name="change-region-availability-of-a-sku"></a>Modifier les régions dans lesquelles une référence SKU est disponible

Au fil du temps, vous pouvez proposer votre offre/référence (SKU) dans d’autres régions.  Vous pouvez également arrêter la prise en charge de l’offre/référence (SKU) dans une région donnée.
Pour modifier la disponibilité, effectuez les étapes suivantes :

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Dans l’onglet **Références**, cliquez sur la référence SKU dont vous souhaitez modifier la disponibilité.

4.  Cliquez sur le bouton **Sélectionner des pays** sous le champ **Disponibilité par pays/région**.

5.  Dans la fenêtre contextuelle, ajoutez ou supprimez les régions pour cette référence SKU.

6.  Cliquez sur **Publier** pour démarrer le workflow de publication afin de mettre à jour la disponibilité des régions pour vos références SKU.

Si une référence (SKU) est proposée dans une nouvelle région, vous devez spécifier les tarifs pour cette région particulière en **exportant les données de tarification**. Si vous ajoutez une région qui était disponible auparavant, vous ne pourrez pas mettre à jour son prix car les modifications de prix ne sont pas autorisées.


### <a name="add-a-new-sku"></a>Ajouter une nouvelle référence SKU

Pour qu’une nouvelle référence SKU soit disponible pour votre offre existante, procédez comme suit : 

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Sous l’onglet **Références**, cliquez sur **Ajouter une référence** et fournissez un **ID de référence SKU** dans la fenêtre contextuelle.

4.  Republiez la machine virtuelle, comme expliqué dans l’article [Publier une machine virtuelle sur la Place de marché Microsoft Azure](./cpp-publish-offer.md).

5.  Cliquez sur **Publier** pour démarrer le flux de travail et publier votre nouvelle référence SKU.


### <a name="update-offer-marketplace-metadata"></a>Mettre à jour les métadonnées de Place de marché d’une offre

Pour mettre à jour les métadonnées de Place de marché (nom de l’entreprise, logos, etc.) associées à votre offre, procédez comme suit : 

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Ouvrez l’onglet **Place de marché**, puis suivez les instructions de l’article [Publier une machine virtuelle sur la Place de marché Microsoft Azure](./cpp-publish-offer.md) pour apporter des modifications aux métadonnées.

4.  Cliquez sur **Publier** pour démarrer le flux de travail et publier vos modifications.


### <a name="update-pricing-on-published-offers"></a>Mettre à jour la tarification des offres publiées

Une fois votre offre de paiement à l’utilisation publiée, vous ne pouvez pas directement augmenter la tarification de la référence SKU.  (Cependant, vous pouvez créer une nouvelle référence SKU sous la même offre, supprimer l’ancienne référence SKU, puis republier votre offre pour les nouveaux clients.)  En revanche, vous pouvez réduire le prix d’une offre publiée en procédant comme suit :

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre à mettre à jour.

3.  Cliquez sur la référence (SKU) pour laquelle vous souhaitez abaisser la tarification.

4.  Si vous avez configuré la tarification dans l’interface graphique utilisateur 1x1, vous pouvez modifier le prix directement dans l’interface utilisateur. Si vous configurez la tarification par le biais d’un tableur d’importation/exportation, vous ne pouvez diminuer les prix qu’avec la fonction Importation/exportation.

3.  Cliquez sur **Enregistrer**.

4.  Cliquez sur **Publier** pour démarrer le flux de travail et publier vos modifications.

La nouvelle tarification réduite est visible pour les nouveaux clients une fois en ligne sur le site web.  Cette nouvelle tarification est appliquée différemment en fonction des clients :

- Les nouveaux clients sont facturés avec ce nouveau taux. 
- Les clients actuels bénéficieront de la réduction de prix de façon rétroactive depuis le début du cycle de facturation de l’entrée en vigueur de la réduction de prix.
S’ils ont déjà été facturés pour le cycle correspondant à la réduction du prix, ils recevront un remboursement au cours du prochain cycle de facturation afin de tenir compte de la réduction.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Tarification simplifiée en devise

Depuis le 1er septembre 2018, une nouvelle section intitulée **Simplified Currency Pricing (Tarification simplifiée en devise)** est ajoutée dans le portail. Microsoft simplifie les activités de la Place de marché Microsoft Azure en améliorant la prévisibilité de la tarification et des prélèvements de vos clients dans le monde entier. Pour ce faire, nous réduisons le nombre de devises dans lesquelles nous facturons vos clients.

La nouvelle section prendra en compte la tarification dans ces nouvelles devises. Lorsque tous les clients seront passés aux devises de ce nouvel accord, la section de tarification initiale sera supprimée et seule la section Tarification simplifiée des devises sera conservée.

Vous aurez jusqu’au 1er novembre 2018 pour fixer un nouveau prix dans les régions où l’accord modifie la devise. Vous ne pourrez pas augmenter le prix dans les régions où l’accord ne modifie pas la devise.

> [!NOTE] 
> Si vous publiez votre offre à l’aide d’API, une nouvelle section s’affiche dans l’offre JSON. Elle indiquera `virtualMachinePricingV2` ou `monthlyPricingV2`, selon le type d’offre. 

Pour toute question concernant cette modification, contactez le support de la [Place de marché Microsoft Azure](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Fonction Comparer

Lorsque vous apportez des modifications à une offre déjà publiée, vous pouvez utiliser la fonction **Comparer** pour les vérifier. Quand utiliser cette fonction :

1.  À tout moment dans le processus de modification, cliquez sur le bouton **Comparer** de votre offre.

    ![Bouton de la fonctionnalité Comparer](./media/publishvm_037.png)


2.  Affichez côte à côte les versions des ressources marketing et des métadonnées.


## <a name="history-of-publishing-actions"></a>Historique des actions de publication

Pour afficher tout l’historique des activités de publication, cliquez sur l’élément **Historique** dans la barre du menu de navigation sur le côté gauche du Portail Cloud Partner. Vous pouvez consulter ici les actions horodatées qui ont été effectuées pendant la durée de vie de vos offres sur la Place de marché Microsoft Azure.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

