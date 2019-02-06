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
ms.openlocfilehash: 19a78b772d2813c263017515f18da06fdb20aa70
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082263"
---
## <a name="terminology"></a>Terminologie

Une image de Place de Marché dans Azure a les attributs suivants :

* **Éditeur** : L’organisation qui a créé l’image. Exemples : Canonical, MicrosoftWindowsServer
* **Offre** : Nom du groupe d’images associées, créé par un éditeur. Exemples : Ubuntu Server, Windows Server
* **SKU** : L’instance d’une offre, par exemple une version majeure d’un logiciel. Exemples : 18.04-LTS, 2019-Datacenter
* **Version** : Numéro de version d’une référence SKU d’image. 

Pour identifier une image de Place de Marché lorsque vous déployez une machine virtuelle par programmation, indiquez ces valeurs individuellement en tant que paramètres. Certains outils acceptent l’*URN* de l’image, qui combine ces valeurs en les séparant par un deux-points (:) : *Éditeur*:*Offre*:*Référence SKU*:*Version*. Dans l’URN, vous pouvez remplacer le numéro de version par la valeur « latest », qui permet de sélectionner la dernière version de l’image. 

Si l’éditeur d’images fournit des conditions de licences et d’achat supplémentaires, vous devez accepter les termes du contrat et activer le déploiement par programmation. Vous devez également indiquer les paramètres du *forfait* lors du déploiement d’une machine virtuelle par programmation. Consultez [Déployer une image avec les termes du contrat de la Place de Marché](#deploy-an-image-with-marketplace-terms).