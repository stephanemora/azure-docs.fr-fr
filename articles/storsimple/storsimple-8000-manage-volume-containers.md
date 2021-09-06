---
title: Gérer les conteneurs de volumes pour les appareils StorSimple de la série 8000
description: Explique comment utiliser la page des conteneurs de volumes du service StorSimple Device Manager pour ajouter, modifier ou supprimer un conteneur de volumes.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: d48f1cf99b22c091f069e7e5f572941a234eaebe
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449872"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Utiliser le service StorSimple Device Manager pour gérer les conteneurs de volumes StorSimple

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel explique comment utiliser le service StorSimple Device Manager pour créer et gérer des conteneurs de volumes StorSimple.

Un conteneur de volumes créé dans un appareil Microsoft Azure StorSimple contient un ou plusieurs volumes hébergeant les paramètres de consommation du compte de stockage, du chiffrement et de la bande passante. Un appareil peut comporter plusieurs conteneurs de volumes pour l’ensemble de ses volumes. 

Un conteneur de volumes présente les attributs suivants :

* **Volumes** : les volumes StorSimple à plusieurs niveaux ou épinglés en local et hébergés dans le conteneur de volumes. 
* **Chiffrement** : clé de chiffrement pouvant être définie pour chaque conteneur de volume. Cette clé est utilisée pour chiffrer les données envoyées depuis votre appareil StorSimple sur le cloud. Une clé AES 256 bits de niveau militaire est utilisée avec la clé saisie par l’utilisateur. Pour sécuriser vos données, nous vous recommandons de toujours activer le chiffrement du stockage cloud.
* **Compte de stockage** : compte de stockage Azure utilisé pour stocker les données. L’ensemble des volumes hébergés dans votre conteneur de volumes partagent ce compte de stockage. Vous pouvez choisir un compte de stockage à partir d’une liste existante ou créer un compte lors de la création du conteneur de volumes, avant de spécifier les informations d’identification d’accès associées à ce compte.
* **Bande passante cloud** : bande passante utilisée par le périphérique lorsque les données du périphérique sont envoyées vers le cloud. Si vous souhaitez que le périphérique utilise toute la bande passante disponible, paramétrez ce champ sur **Illimité**. Vous pouvez également créer et appliquer un modèle de bande passante, afin d’allouer la bande passante suivant une planification.

Les procédures suivantes expliquent comment utiliser le panneau **Conteneurs de volumes** StorSimple pour effectuer les opérations courantes ci-dessous :

* Ajouter un conteneur de volumes
* Modifier un conteneur de volumes
* Supprimer un conteneur de volumes

## <a name="add-a-volume-container"></a>Ajouter un conteneur de volumes
Pour ajouter un conteneur de volumes, procédez comme suit.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modifier un conteneur de volumes
Pour modifier un conteneur de volumes, procédez comme suit.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Supprimer un conteneur de volumes
Un conteneur de volumes comporte plusieurs volumes. Il peut être supprimé uniquement après la suppression de l’ensemble des volumes qu’il héberge. Pour supprimer un conteneur de volumes, procédez comme suit.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [gestion des volumes StorSimple](storsimple-8000-manage-volumes-u2.md). 
* En savoir plus sur l’[utilisation du service StorSimple Device Manager pour gérer votre appareil StorSimple](storsimple-8000-manager-service-administration.md).

