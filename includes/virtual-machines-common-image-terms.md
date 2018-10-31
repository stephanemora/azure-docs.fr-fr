---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437096"
---
## <a name="terminology"></a>Terminologie

Une image de Place de Marché dans Azure a les attributs suivants :

* **Éditeur** : organisation qui a créé l’image. Exemples : Canonical, MicrosoftWindowsServer
* **Offre** : nom du groupe d’images associées créé par un éditeur. Exemples : Ubuntu Server, Windows Server
* **Référence SKU** : instance d’une offre, par exemple une version majeure d’une distribution. Exemples : 16.04-LTS, 2016-Datacenter
* **Version** : numéro de version d’une référence SKU d’image. 

Pour identifier une image de Place de Marché lorsque vous déployez une machine virtuelle par programmation, indiquez ces valeurs individuellement en tant que paramètres. Certains outils acceptent *l’URN* de l’image qui combine ces valeurs en les séparant par un caractère deux-points (:) : *Éditeur*:*Offre*:*Référence SKU*:*Version*. Dans l’URN, vous pouvez remplacer le numéro de version par la valeur « latest », qui permet de sélectionner la dernière version de l’image. 

Si l’éditeur d’images fournit des conditions de licences et d’achat supplémentaires, vous devez accepter les termes du contrat et activer le déploiement par programmation. Vous devez également indiquer les paramètres du *forfait* lors du déploiement d’une machine virtuelle par programmation. Consultez [Déployer une image avec les termes du contrat de la Place de Marché](#deploy-an-image-with-marketplace-terms).