---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c97c8231265cf87f52333a56d21d6fb13180c554
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808847"
---
## <a name="create-base-resources"></a>Créer les ressources de base

Avant de pouvoir configurer le routage des messages, vous devez créer un hub IoT, un compte de stockage et une file d’attente Service Bus. Ces ressources peuvent être créées en utilisant l’un des quatre articles disponibles pour la première partie de ce tutoriel : le portail Azure, un modèle Azure Resource Manager, l’interface Azure CLI ou Azure PowerShell.

Utilisez le même groupe de ressources et le même emplacement pour toutes les ressources. À la fin du processus, vous pouvez supprimer toutes les ressources en une seule fois en supprimant le groupe de ressources.

Vous trouverez ci-dessous un récapitulatif des étapes à effectuer dans les sections suivantes : 

1. Créez un [groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md).

2. Créez un IoT Hub dans le niveau S1. Ajoutez un groupe de consommateurs à votre IoT Hub. Le groupe de consommateurs est utilisé par Azure Stream Analytics lors de la récupération des données.

   > [!NOTE]
   > Vous devez utiliser un hub IoT dans le cadre d'un niveau payant pour suivre ce tutoriel. Le niveau gratuit ne vous permet de configurer qu'un seul point de terminaison alors que ce tutoriel en nécessite plusieurs.
   > 

3. Créez un compte de stockage V1 standard avec la réplication Standard_LRS.

4. Créez un espace de noms et une file d’attente Service Bus.

5. Créez une identité d’appareil pour l’appareil simulé qui envoie des messages à votre Hub. Enregistrez la clé pour la phase de test. (Si vous créez un modèle Resource Manager, faites cela après avoir déployé le modèle.)