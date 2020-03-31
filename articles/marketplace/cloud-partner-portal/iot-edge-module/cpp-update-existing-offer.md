---
title: Mettre à jour une offre de module Azure IoT Edge existante | Place de marché Microsoft Azure
description: Méthode de mise à jour d’une offre de module IoT Edge existante dans la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: dceff3e320554edc972654aa49552bffbc4c9a13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286487"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Mettre à jour une offre de module IoT Edge existante

Cet article vous présente en détail comment mettre à jour votre offre de module IoT Edge dans le [Portail Cloud Partner](https://cloudpartner.azure.com/), puis republier l’offre.

Vous pouvez mettre à jour votre offre pour plusieurs raisons, par exemple :

-  Ajout d’une nouvelle version d’image de module IoT Edge pour les références (SKU) existantes.
-  Ajout de nouvelles références (SKU).
-  Mise à jour des métadonnées de la Place de marché pour l’offre ou des références SKU individuelles.

Le portail contient les fonctions **Comparer** et **Historique** qui vous aident à apporter ces modifications.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Modifications non autorisées apportées à l’offre de module IoT Edge ou aux références (SKU)

Certains attributs d'une offre de module IoT Edge ou d'une référence SKU ne peuvent pas être modifiés une fois l'offre publiée sur la Place de marché Azure. Vous ne pouvez pas modifier les paramètres suivants :

-  **ID d’offre** et **ID de l’éditeur** de l’offre
-  **ID de référence (SKU)** de références (SKU) existantes
-  Balises de version, par exemple : `1.0.1`
-  Modifications du modèle de facturation/licence apportées à des références (SKU) existantes

## <a name="common-update-operations"></a>Opérations de mise à jour courantes

Les opérations de mise à jour suivantes sont courantes.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Mise à jour de la version d’image de module IoT Edge pour une référence (SKU)

Il est courant qu'une image de module IoT Edge soit régulièrement mise à jour avec des correctifs de sécurité, des fonctionnalités supplémentaires, etc. Dans ce scénario, vous pouvez mettre à jour l’image de module IoT Edge associée à vos références SKU à l’aide de la procédure suivante :

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Dans l’onglet **Références**, cliquez sur la référence SKU associée à l’image de module IoT Edge à mettre à jour.

4.  Sous **image de module Edge**, sélectionnez **+ Nouvelle version de l’image** pour ajouter une nouvelle image de module IoT Edge.

5.  Fournissez les nouvelles **versions de l’image** de module IoT Edge. La version de l’image doit suivre les mêmes instructions de balises que les versions précédentes. Les instructions de version doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Vérifiez que la nouvelle version que vous fournissez est supérieure à toutes les versions précédentes.

6.  Sélectionnez **Publier** pour démarrer le flux de travail et publier la nouvelle version de votre module IoT Edge vers la Place de marché Azure.

### <a name="add-a-new-sku"></a>Ajouter une nouvelle référence SKU

Pour qu’une nouvelle référence SKU soit disponible pour votre offre, procédez comme suit : 

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Sous l’onglet **Références**, cliquez sur **Ajouter une référence** et fournissez un **ID de référence SKU** dans la fenêtre contextuelle.

4.  Republiez le module IoT Edge en suivant la procédure décrite dans [Publier un module IoT Edge sur la Place de marché Azure](./cpp-publish-offer.md).

5.  Cliquez sur **Publier** pour démarrer le flux de travail et publier votre nouvelle référence SKU.


### <a name="update-offer-marketplace-metadata"></a>Mettre à jour les métadonnées de Place de marché d’une offre

Pour mettre à jour les métadonnées de Place de marché associées à votre offre, procédez comme suit. (par exemple : nom de l’entreprise, logos, etc.)

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Accédez à l’onglet **Place de marché**. Suivez les instructions de l'article [Publier un module IoT Edge sur la Place de marché Azure](./cpp-publish-offer.md) pour apporter des modifications aux métadonnées.

4.  Cliquez sur **Publier** pour démarrer le flux de travail et publier vos modifications.

## <a name="compare-feature"></a>Fonction Comparer

Lorsque vous apportez des modifications à une offre déjà publiée, vous pouvez utiliser la fonction **Comparer** pour les vérifier. 

**Pour utiliser la fonctionnalité Comparer :**

1.  À tout moment du processus de modification, sélectionnez **Comparer** pour votre offre.

    ![Bouton de la fonctionnalité Comparer](./media/iot-edge-module-compare.png)


2.  Affichez côte à côte les versions des ressources marketing et des métadonnées.


## <a name="history-of-publishing-actions"></a>Historique des actions de publication

Pour afficher tout l’historique des activités de publication, cliquez sur l’onglet **Historique** dans la barre du menu de navigation sur le côté gauche du Portail Cloud Partner. Vous pouvez consulter ici les actions horodatées effectuées pendant la durée de vie de vos offres sur la Place de marché Microsoft Azure.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
