---
title: Mettre à jour une offre de conteneur Azure existante | Place de marché Azure
description: Guide pratique pour mettre à jour une offre de conteneur existante sur la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: cc378dcc1d5f777f4e81825e2f99dedd37a87f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143820"
---
# <a name="update-an-existing-container-offer"></a>Mettre à jour une offre de conteneur existante

> [!IMPORTANT]
> À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres de conteneur Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions données dans [Créer une offre de conteneur Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) pour gérer vos offres migrées.

Cet article explique en détail comment mettre à jour une offre de conteneur sur le [Portail Cloud Partner](https://cloudpartner.azure.com/).

Vous pouvez mettre à jour votre offre pour plusieurs raisons, par exemple :

-  Ajout d’une nouvelle version d’image conteneur à des références SKU existantes.
-  Ajout de nouvelles références (SKU).
-  Mise à jour des métadonnées de la Place de marché pour l’offre ou des références SKU individuelles.

Le portail propose les fonctions **Comparer** et **Historique** pour faciliter ces modifications.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Modifications non autorisées sur une référence SKU ou une offre de conteneur

Certains attributs d’une référence SKU ou d’une offre de conteneur ne sont plus modifiables une fois l’offre publiée sur la Place de marché Azure. Vous ne pouvez pas modifier les paramètres suivants :

-  **ID d’offre** et **ID de l’éditeur** de l’offre
-  **ID de référence (SKU)** de références (SKU) existantes
-  Balises de version, par exemple : `1.0.1`
-  Modifications du modèle de facturation/licence apportées à des références (SKU) existantes

## <a name="common-update-operations"></a>Opérations de mise à jour courantes

Les opérations de mise à jour suivantes sont courantes.

### <a name="update-container-image-version-for-a-sku"></a>Mettre à jour la version d’image conteneur d’une référence SKU

Il est courant qu’une image conteneur soit régulièrement mise à jour avec des correctifs de sécurité, des fonctionnalités supplémentaires, etc. Dans ce scénario, l’objectif est de mettre à jour l’image conteneur associée à la référence SKU en suivant les étapes ci-dessous :

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3. Dans l’onglet **Références**, sélectionnez la référence SKU associée à l’image conteneur à mettre à jour.
4. Sous **Image conteneur**, sélectionnez **+ Nouvelle version d’image** pour ajouter une nouvelle image de conteneur.
5. Indiquez les nouvelles **versions d’image** conteneur. La version de l’image doit suivre les mêmes instructions de balises que les versions précédentes. Les instructions de version doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Vérifiez que la nouvelle version que vous fournissez est supérieure à toutes les versions précédentes.
6. Sélectionnez **Publier** pour démarrer le flux de travail visant à publier la nouvelle version d’image conteneur sur la Place de marché Azure.

### <a name="add-a-new-sku"></a>Ajouter une nouvelle référence SKU

Pour qu’une nouvelle référence SKU soit disponible pour votre offre, procédez comme suit :

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3. Sous l’onglet **Références**, cliquez sur **Ajouter une référence** et fournissez un **ID de référence SKU** dans la fenêtre contextuelle.
4. Republiez le conteneur suivant les étapes décrites dans [Publier une offre de conteneur](./cpp-publish-offer.md).
5. Cliquez sur **Publier** pour démarrer le flux de travail et publier votre nouvelle référence SKU.

### <a name="update-offer-marketplace-metadata"></a>Mettre à jour les métadonnées de Place de marché d’une offre

Pour mettre à jour les métadonnées de Place de marché associées à votre offre, procédez comme suit. (par exemple : nom de l’entreprise, logos, etc.)

1. Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).
2. Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.
3. Accédez à l’onglet **Place de marché**. Suivez les instructions de l’article [Publier une offre de conteneur](./cpp-publish-offer.md) pour apporter des modifications aux métadonnées.
4. Cliquez sur **Publier** pour démarrer le flux de travail et publier vos modifications.

## <a name="compare-feature"></a>Fonctionnalité Comparer

Lorsque vous apportez des modifications à une offre publiée, vous pouvez utiliser la fonctionnalité **Comparer** pour les vérifier.

### <a name="to-use-the-compare-feature"></a>Pour utiliser la fonctionnalité Comparer :

1. Au cours du processus de modification, sélectionnez Comparer pour votre offre.
2. Affichez côte à côte les versions des ressources marketing et des métadonnées.


## <a name="history-of-publishing-actions"></a>Historique des actions de publication

Pour afficher tout l’historique des activités de publication, cliquez sur l’onglet **Historique** dans la barre du menu de navigation sur le côté gauche du Portail Cloud Partner. Vous pouvez consulter ici les actions horodatées effectuées pendant la durée de vie de vos offres sur la Place de marché Microsoft Azure.
