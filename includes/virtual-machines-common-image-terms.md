---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880553"
---
## <a name="terminology"></a>Terminologie

Une image de Place de Marché dans Azure a les attributs suivants :

* **Éditeur** : L’organisation qui a créé l’image. Exemples : Canonical, MicrosoftWindowsServer
* **Offre** : Nom du groupe d’images associées, créé par un éditeur. Exemples : UbuntuServer, WindowsServer
* **SKU** : L’instance d’une offre, par exemple une version majeure d’un logiciel. Exemples : 18.04-LTS, 2019-Datacenter
* **Version** : Numéro de version d’une référence SKU d’image. 

Pour identifier une image de Place de Marché lorsque vous déployez une machine virtuelle par programmation, indiquez ces valeurs individuellement en tant que paramètres. Certains outils acceptent l’*URN* de l’image, qui combine ces valeurs en les séparant par un deux-points (:) : *Éditeur*:*Offre*:*Référence SKU*:*Version*. Dans l’URN, vous pouvez remplacer le numéro de version par la valeur « latest », qui permet de sélectionner la dernière version de l’image. 

Si l’éditeur d’images fournit des conditions de licences et d’achat supplémentaires, vous devez accepter les termes du contrat et activer le déploiement par programmation. Vous devez également indiquer les paramètres du *forfait* lors du déploiement d’une machine virtuelle par programmation. Consultez [Déployer une image avec les termes du contrat de la Place de Marché](#deploy-an-image-with-marketplace-terms).

<!-- Update_Description: wording update -->