---
title: Mettre à jour une offre d’application Azure existante | Place de marché Azure
description: Comment mettre à jour une offre d’application Azure existante sur la place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275949"
---
# <a name="update-an-existing-azure-application-offer"></a>Mettre à jour une offre d’application Azure existante

Différents types de mises à jour peuvent s'appliquer à votre offre à l'issue de la publication et de la mise en ligne de celle-ci. Toute modification apportée à la nouvelle version de votre offre doit être enregistrée et republiée pour apparaître sur la Place de marché. Cet article explique en détail comment mettre à jour votre offre d’application gérée sur le [Portail Cloud Partner](https://cloudpartner.azure.com/).

Vous pouvez mettre à jour votre offre pour plusieurs raisons, par exemple :

- Ajouter une nouvelle version d’image à des références SKU existantes
- Ajout de nouvelles références (SKU).
- Mise à jour des métadonnées de la Place de marché pour l’offre ou des références SKU individuelles.

Le portail propose les fonctions **Comparer** et **Historique** pour faciliter ces modifications.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Modifications non autorisées apportées à une référence SKU/offre d’application Azure

Certains attributs d’une référence SKU ou d’une offre de conteneur ne sont plus modifiables une fois l’offre publiée sur la Place de marché Azure. Vous ne pouvez pas modifier les paramètres suivants :

- ID d’offre et ID de l’éditeur de l’offre
- ID de référence (SKU) de références (SKU) existantes
- Balises de version, par exemple : `1.0.1`
- Modifications du modèle de facturation/licence apportées à des références (SKU) existantes

## <a name="common-update-operations"></a>Opérations de mise à jour courantes

Les opérations de mise à jour suivantes sont courantes.

### <a name="update-image-version-for-a-sku"></a>Mettre à jour la version d’image pour une référence SKU

Il est courant qu’une image soit régulièrement mise à jour avec des correctifs de sécurité, des fonctionnalités supplémentaires et ainsi de suite. Dans ce scénario, l’objectif est de mettre à jour l’image associée à la référence SKU en suivant les étapes ci-dessous :

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3. Dans l’onglet **Références**, sélectionnez la référence SKU associée à l’image à mettre à jour.
4. Sélectionnez **+ Nouvelle version de l’image** pour ajouter une nouvelle image.
5. Fournissez les nouvelles versions d’image. La version de l’image doit suivre les mêmes instructions de balises que les versions précédentes. Les instructions de version doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Vérifiez que la nouvelle version que vous fournissez est supérieure à toutes les versions précédentes.
6. Sélectionnez **Publier** pour démarrer le flux de travail visant à publier la nouvelle version d’image conteneur sur la Place de marché Azure.

### <a name="add-a-new-sku"></a>Ajouter une nouvelle référence SKU

Pour qu’une nouvelle référence SKU soit disponible pour votre offre, procédez comme suit :

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3. Sous l’onglet **Références**, cliquez sur **Ajouter une référence** et fournissez un **ID de référence SKU** dans la fenêtre contextuelle.
4. Republiez l’offre en suivant les étapes décrites dans [Publier une offre d’application Azure](./cpp-publish-offer.md).
5. Cliquez sur **Publier** pour démarrer le flux de travail et publier votre nouvelle référence SKU.

### <a name="update-offer-marketplace-metadata"></a>Mettre à jour les métadonnées de Place de marché d’une offre

Pour mettre à jour les métadonnées de Place de marché associées à votre offre, procédez comme suit. (par exemple : nom de l’entreprise, logos, etc.)

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3. Accédez à l’onglet **Place de marché**. Suivez les instructions de la section [Publier une offre d’application Azure](./cpp-publish-offer.md) pour apporter des modifications aux métadonnées.
4. Cliquez sur **Publier** pour démarrer le flux de travail et publier vos modifications.
 
>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](../../cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

## <a name="deleting-an-existing-offer"></a>Suppression d’une offre existante

Vous pouvez décider de supprimer votre offre de la Place de marché. La suppression d’une offre n’a aucune répercussion sur les acquisitions actuelles de cette offre. Ces acquisitions clients continueront à fonctionner comme avant. Toutefois, aucune nouvelle acquisition de l’offre ne sera possible après la suppression.

L’arrêt de l’offre est le processus d’arrêt de l’accord de service et/ou de licence entre vos clients actuels et vous.
L’aide et les stratégies relatives à l’arrêt et à la suppression des offres sont régies par le Contrat d’éditeur de la Place de marché Microsoft (voir section 7) et les Stratégies de participation (voir section 6.2).

Pour plus d’informations, consultez [Supprimer une offre/référence SKU de la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Fonctionnalité Comparer

Lorsque vous apportez des modifications à une offre publiée, vous pouvez utiliser la fonctionnalité Comparer pour les vérifier.

Pour utiliser la fonctionnalité Comparer :

1. Au cours du processus de modification, sélectionnez Comparer pour votre offre.
2. Affichez côte à côte les versions des ressources marketing et des métadonnées.

## <a name="history-of-publishing-actions"></a>Historique des actions de publication

Pour afficher tout l’historique des activités de publication, cliquez sur l’onglet **Historique** dans la barre du menu de navigation sur le côté gauche du Portail Cloud Partner. Vous pouvez consulter ici les actions horodatées effectuées pendant la durée de vie de vos offres sur la Place de marché Microsoft Azure.

## <a name="next-steps"></a>Étapes suivantes

[Offre d’application Azure](./cpp-azure-app-offer.md)
