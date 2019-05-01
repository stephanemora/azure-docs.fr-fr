---
title: Mettre à jour une offre de module Azure IoT Edge existante | Place de marché Azure
description: Méthode de mise à jour d’une offre de module IoT Edge existante dans la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 1dc6832fe09a610634ad934e3b173be6cc3331e6
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942152"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Mettre à jour une offre de module IoT Edge existante

Cet article vous présente en détail comment mettre à jour votre offre de module IoT Edge dans le [Portail Cloud Partner](https://cloudpartner.azure.com/), puis republier l’offre.

Vous pouvez mettre à jour votre offre pour plusieurs raisons, par exemple :

-  Ajout d’une nouvelle version d’image de module IoT Edge pour les références (SKU) existantes.
-  Ajout de nouvelles références (SKU).
-  Mise à jour des métadonnées de la Place de marché pour l’offre ou des références SKU individuelles.

Le portail contient les fonctions **Comparer** et **Historique** qui vous aident à apporter ces modifications.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Modifications non autorisées apportées à l’offre de module IoT Edge ou aux références (SKU)

Il existe des attributs d’une offre de module IoT Edge ou d’une référence (SKU) qui ne peut pas être modifiée une fois que l’offre est publiée sur la place de marché Azure. Vous ne pouvez pas modifier les paramètres suivants :

-  **ID d’offre** et **ID de l’éditeur** de l’offre
-  **ID de référence (SKU)** de références (SKU) existantes
-  Balises de version, par exemple : `1.0.1`
-  Modifications du modèle de facturation/licence apportées à des références (SKU) existantes

## <a name="common-update-operations"></a>Opérations de mise à jour courantes

Les opérations de mise à jour suivantes sont courantes.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Mise à jour de la version d’image de module IoT Edge pour une référence (SKU)

Il est courant pour une image de module IoT Edge à mettre à jour régulièrement avec les correctifs de sécurité, des fonctionnalités supplémentaires et ainsi de suite. Dans ce scénario, vous pouvez mettre à jour l’image de module IoT Edge associée à vos références SKU à l’aide de la procédure suivante :

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

4.  Republier le module IoT Edge à l’aide de la procédure décrite dans [publier un module IoT Edge à la place de marché Azure](./cpp-publish-offer.md).

5.  Cliquez sur **Publier** pour démarrer le flux de travail et publier votre nouvelle référence SKU.


### <a name="update-offer-marketplace-metadata"></a>Mettre à jour les métadonnées de Place de marché d’une offre

Pour mettre à jour les métadonnées de Place de marché associées à votre offre, procédez comme suit. (par exemple : nom de l’entreprise, logos, etc.)

1.  Connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com/).

2.  Sous **Toutes les offres**, recherchez l’offre que vous souhaitez mettre à jour.

3.  Accédez à l’onglet **Place de marché**. Suivez les instructions de la [publier un module IoT Edge à la place de marché Azure](./cpp-publish-offer.md) article pour apporter des modifications de métadonnées.

4.  Cliquez sur **Publier** pour démarrer le flux de travail et publier vos modifications.

## <a name="compare-feature"></a>Fonction Comparer

Lorsque vous apportez des modifications à une offre déjà publiée, vous pouvez utiliser la fonction **Comparer** pour les vérifier. 

**Pour utiliser la fonctionnalité Comparer :**

1.  À tout moment du processus de modification, sélectionnez **Comparer** pour votre offre.

    ![Bouton de la fonctionnalité Comparer](./media/iot-edge-module-compare.png)


2.  Affichez côte à côte les versions des ressources marketing et des métadonnées.


## <a name="history-of-publishing-actions"></a>Historique des actions de publication

Pour afficher tout l’historique des activités de publication, cliquez sur l’onglet **Historique** dans la barre du menu de navigation sur le côté gauche du Portail Cloud Partner. Vous pouvez consulter ici les actions horodatées effectuées pendant la durée de vie de vos offres sur la Place de marché Microsoft Azure.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
