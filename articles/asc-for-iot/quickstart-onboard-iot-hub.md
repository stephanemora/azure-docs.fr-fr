---
title: Activer le service Azure Security Center pour IoT dans IoT Hub | Microsoft Docs
description: Découvrez comment activer le service Azure Security Center pour IoT dans votre hub IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d9c5352a90d5bcacbaf27b7b62be61fc404e87a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299488"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Démarrage rapide : Intégrer le service Azure Security Center pour IoT dans IoT Hub

Cet article explique comment activer le service Azure Security Center pour IoT sur votre IoT Hub. Si vous n’avez pas de hub IoT, pour commencer, voir [Créer un hub IoT à l’aide du portail Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal). 

> [!NOTE]
> Actuellement, Azure Security Center pour IoT prend uniquement en charge les hubs IoT de niveau standard.
> Azure Security Center pour IoT est une solution de hub unique. Si vous avez besoin de plusieurs hubs, plusieurs solutions Azure Security Center pour IoT sont requises. 

## <a name="prerequisites-for-enabling-the-service"></a>Prérequis pour l’activation du service

- Espace de travail Log Analytics
  - Par défaut, Azure Security Center pour IoT stocke deux types d’informations dans votre espace de travail Log Analytics : des **alertes de sécurité** et des **recommandations**. 
  - Vous pouvez choisir d’ajouter le stockage d’un type d’information supplémentaire : les **événements bruts**. Notez que le stockage d’**événements bruts** dans Log Analytics engendre des frais de stockage supplémentaires. 
- IoT Hub (niveau standard)
- Satisfaire à tous les [prérequis du service](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Activer Azure Security Center pour IoT sur votre IoT Hub 

Pour activer la sécurité sur votre hub IoT, procédez de la façon suivante : 

1. Ouvrez votre **hub IoT** dans le portail Azure. 
1. Dans le menu **Sécurité**, cliquez sur **Sécuriser votre solution IoT**.
1. Laissez l’option par défaut **Activer** sélectionnée. 
1. Sélectionnez votre espace de travail Log Analytics.
1. Fournissez les détails de votre espace de travail Log Analytics. 
   - Choisissez d’activer la **collection de jumeaux** en laissant le bouton bascule de **collection de jumeaux** sur **Actif**.
   - Choisissez de stocker les **événements bruts** en plus des types d’informations de stockage par défaut en sélectionnant **Stocker les événements bruts de sécurité d’appareil** dans Log Analytics. Laissez l’option **événement brut** **Activée**. 
    
1. Cliquez sur **Enregistrer**. 

Félicitations ! Vous avez activé Azure Security Center pour IoT sur votre IoT Hub. 

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour configurer votre solution...

> [!div class="nextstepaction"]
> [Configurer votre solution](quickstart-configure-your-solution.md)


