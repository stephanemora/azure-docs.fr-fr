---
title: Activer le service ASC pour IoT dans IoT Hub (préversion) | Microsoft Docs
description: Découvrez comment activer le service ASC pour IoT dans votre hub IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4fd7fb68fd2eb8be0c1c109d76cff341faba9391
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541734"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Démarrage rapide : Activer le service dans IoT Hub

> [!IMPORTANT]
> ASC pour IoT est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment activer le service ASC pour IoT en préversion sur votre hub IoT.  

> [!NOTE]
> Actuellement, ASC pour IoT prend uniquement en charge les hubs IoT de niveau standard ou supérieur.
>ASC pour IoT est une solution de hub unique. Si plusieurs hubs sont nécessaires, vous avez besoin de plusieurs solutions. 

## <a name="prerequisites-for-enabling-the-service"></a>Prérequis pour l’activation du service

- Espace de travail Log Analytics
  - Par défaut, deux types d’informations sont stockés par ASC pour IoT dans votre espace de travail Log Analytics : les **alertes de sécurité** et les **recommandations**. 
  - Vous pouvez choisir d’ajouter le stockage d’un type d’information supplémentaire : les **événements bruts**. Notez que le stockage d’**événements bruts** dans Log Analytics engendre des frais de stockage supplémentaires. 
- IoT Hub (niveau standard ou supérieur)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Activer ASC pour IoT sur votre hub IoT 

Pour activer la sécurité sur votre hub IoT, procédez de la façon suivante : 

1. Ouvrez votre **hub IoT** dans le portail Azure. 
2. Sélectionnez et ouvrez **Sécurité** dans le menu de gauche. 
3. Choisissez **Activer la sécurité IoT**. 
4. Fournissez les détails de votre espace de travail Log Analytics. 
   - Choisissez de stocker les **événements bruts** en plus des types d’informations par défaut de stockage en laissant le bouton bascule d’**événement brut** sur **Actif**. 
   - Choisissez d’activer la **collection de jumeaux** en laissant le bouton bascule de **collection de jumeaux** sur **Actif**. 
5. Cliquez sur **OK**. 
6. Cliquez sur **Enregistrer**. 

Félicitations ! Vous avez terminé l’activation d’ASC pour IoT sur votre hub IoT. 

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment configurer votre solution...

> [!div class="nextstepaction"]
> [Configurer votre solution](quickstart-configure-your-solution.md)