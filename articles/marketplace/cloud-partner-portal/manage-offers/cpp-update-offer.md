---
title: Mettre à jour des offres sur une place de marché | Place de marché Microsoft Azure
description: Mettre à jour des offres sur les places de marché Microsoft Azure et AppSource à l’aide du Portail Cloud Partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288510"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Mettre à jour des offres sur la Place de marché Microsoft Azure et AppSource

Il existe différents types de mises à jour que vous pouvez appliquer à votre offre une fois qu’elle est publiée.  Le [Portail Microsoft Cloud Partner](https://cloudpartner.azure.com/) vous aide à modifier correctement les attributs d’une offre, notamment par le biais des opérations suivantes :

-  Ajout d’une nouvelle version du package ou de l’image de la machine virtuelle à une référence SKU existante
-  Modification des régions dans lesquelles une référence (SKU) est disponible
-  Ajout de nouvelles références (SKU)
-  Mise à jour des métadonnées de la Place de marché pour les offres ou les références (SKU) 
-  Mise à jour de la tarification des offres avec paiement à l’utilisation

Le portail a également des fonctionnalités, telles que la possibilité de comparer les fonctionnalités et de voir un historique des fonctionnalités d’une offre, qui vous aident à gérer les modifications.  Après avoir modifié une offre ou une référence SKU, celle-ci doit être republiée avant que les modifications ne soient « mises en ligne ».  Cet article vous présente en détail les différents aspects de la mise à jour de votre offre de place de marché.

## <a name="unpermitted-changes-to-an-offersku"></a>Modifications non autorisées apportées à une offre/référence SKU

Certains attributs d’une offre ou référence SKU ne peuvent pas être modifiés une fois que celle-ci a été publiée sur la place de marché.  Les champs correspondants sont désactivés sous l’onglet **Editor** du portail, par exemple :  

- ID de l’offre et ID de l’éditeur
- RÉFÉRENCE (SKU) 
- Nombre de disques de données de références SKU existantes
- Modifications du modèle de facturation/licence concernant des références SKU existantes
- Balises de version, par exemple : `1.0.1`


## <a name="common-update-operations"></a>Opérations de mise à jour courantes

Les sections suivantes expliquent comment effectuer certaines des principales opérations de mise à jour.  Ces opérations ne sont pas disponibles pour tous les types d’offre.  Vous devez vous connecter au Portail Cloud Partner pour démarrer une de ces opérations.


### <a name="update-offer-contacts"></a>Mettre à jour les contacts de l’offre

Suivez les étapes ci-dessous pour mettre à jour les contacts de support de votre offre.
1. Dans la page **All Offers**, sélectionnez l’offre.
2. Sélectionnez l’onglet **Contacts**. Mettez à jour vos contacts.
3. Sélectionnez le bouton **Enregistrer**.
4. Sélectionnez **Publier** pour démarrer le processus de publication.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Changer les régions dans lesquelles une offre ou une référence SKU est disponible

Au fil du temps, vous pouvez proposer votre offre/référence (SKU) dans d’autres régions.
Vous pouvez également arrêter la prise en charge de l’offre/référence (SKU) dans une région donnée.
Pour implémenter ces modifications, effectuez les étapes suivantes.

1. Dans la page **All Offers**, recherchez l’offre à mettre à jour.

Pour les offres de la Place de marché Microsoft Azure :

1. Sélectionnez l’onglet **Références**.  Sélectionnez la référence SKU à modifier.
1. Cliquez sur le bouton **Select Countries** sous le champ **Country/Region availability** (Disponibilité par pays/région).
1. Dans la boîte de dialogue, ajoutez ou supprimez les régions pour cette référence SKU.

Pour les offres AppSource :

1. Sélectionnez l’onglet **Storefront Details** (Informations sur les vitrines).
1. À côté de l’étiquette **Supported countries/regions** (Pays/régions pris en charge), cliquez sur **Supported countries/regions**. 
1. Dans la boîte de dialogue, ajoutez ou supprimez des régions pour cette offre.

Pour les deux places de marché :

1. Cliquez sur **Publish** pour démarrer le processus de publication. 

Si une référence SKU est proposée dans une nouvelle région, vous devez spécifier les tarifs pour cette région particulière en **exportant les données tarifaires**. Si vous ajoutez une région qui était disponible, vous ne pouvez pas mettre à jour son prix car les modifications de prix ne sont pas autorisées.


### <a name="add-a-new-sku"></a>Ajouter une nouvelle référence SKU 

Pour qu’une nouvelle référence SKU soit disponible pour une offre existante, effectuez les étapes suivantes :

1. Dans la page **All Offers**, recherchez l’offre.
3. Sous le formulaire des **références SKU**, cliquez sur **Add new SKU** (Ajouter une nouvelle référence SKU)et fournissez un **ID de SKU** dans la fenêtre contextuelle.
4. Suivez les étapes détaillées restantes dans [Publier une offre de machine virtuelle](../virtual-machine/cpp-publish-offer.md).
5. Cliquez sur **Publish** pour démarrer le processus de publication.


### <a name="update-offer-marketplace-assets"></a>Mettre à jour des ressources de place de marché d’une offre

Dans certains scénarios, vous pouvez être amené à mettre à jour les ressources d’image ou de texte sur une place de marché, telles que les logos de votre entreprise ou la description d’une offre. Effectuez les étapes suivantes pour mettre à jour ces ressources.

1. Dans la page **All Offers**, recherchez votre offre. 
2. Sélectionnez l’onglet **Marketplace** (Place de marché) et suivez les instructions dans la rubrique *Onglet Marketplace (Place de marché)* liée à votre offre.
3. Cliquez sur **Publish** pour démarrer le processus de publication.


### <a name="update-pricing-on-published-offers"></a>Mettre à jour les tarifs des offres publiées

Une fois votre offre de paiement à l’utilisation publiée, vous ne pouvez pas augmenter le prix d’une référence SKU existante.  Au lieu de cela, créez une référence SKU sous la même offre, supprimez l’ancienne référence SKU, puis republiez votre offre. Vous pouvez réduire le prix des offres publiées. Pour réduire le prix de votre offre :

1. Sélectionnez la référence SKU dont vous souhaitez abaisser les tarifs.
2. Vous devez définir un prix inférieur en recourant au mécanisme que vous avez utilisé initialement : directement dans l’interface utilisateur du portail ou avec la feuille de calcul d’importation/exportation.
3. Cliquez sur **Enregistrer**.
4. Cliquez sur **Publish** pour démarrer le processus de publication.

Les nouveaux clients peuvent voir les tarifs une fois qu’ils sont en ligne sur la place de marché, et ils paient alors le nouveau prix avec réduction.  Les clients actuels bénéficient de la réduction de prix de façon rétroactive depuis le début du cycle de facturation de l’entrée en vigueur de la réduction de prix.  S’ils ont déjà été facturés pour le cycle correspondant à la réduction du prix, ils recevront un remboursement au cours du prochain cycle de facturation afin de tenir compte de la réduction.


## <a name="compare-feature"></a>Fonctionnalité Comparer

Quand vous apportez des modifications à une offre déjà publiée, vous pouvez utiliser la fonction *Compare* pour les vérifier. Pour utiliser cette fonctionnalité :

1. À tout moment dans le processus de modification, vous pouvez cliquer sur le bouton **Compare** sous l’onglet **Editor** de votre offre.
2. Une fenêtre de comparaison affiche côte à côte la version des modifications enregistrées pour cette offre et la version de l’offre de la place de marché. 

![Bouton de comparaison des offres sous l’onglet Editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historique des actions de publication

Pour voir tout l’historique des activités de publication, sélectionnez l’onglet **History** dans la barre de menus verticale sur le côté gauche du Portail Cloud Partner.  La page History fournit un filtrage flexible par plusieurs caractéristiques et prend en charge le classement des colonnes.  Chaque événement de publication est horodaté.  Pour plus d’informations, consultez [Page Historique d’audit](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également utiliser le Portail Cloud Partner pour [supprimer une offre ou référence SKU publiée](./cpp-delete-offer.md).
