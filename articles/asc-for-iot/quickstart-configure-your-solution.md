---
title: Configurer votre solution Azure Security Center pour IoT | Microsoft Docs
description: Découvrez comment configurer votre solution IoT de bout en bout avec Azure Security Center pour IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 15c8aabbcb19d009bb202d111dc7f80da4cebff1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597035"
---
# <a name="quickstart-configure-your-iot-solution"></a>Démarrage rapide : Configurer votre solution IoT

Cet article explique comment effectuer la configuration initiale de votre solution de sécurité IoT en utilisant Azure Security Center pour IoT. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center pour IoT

Azure Security Center pour IoT fournit aux solutions IoT basées sur Azure une sécurité complète de bout en bout.

Avec Azure Security Center pour IoT, vous pouvez superviser l’ensemble de votre solution dans un tableau de bord, en exposant tous vos appareils IoT, les plateformes IoT et les ressources principales dans Azure.

Une fois activé sur votre IoT Hub, Azure Security Center pour IoT identifie automatiquement les autres services Azure, qui sont également connectés à votre IoT Hub et associés à votre solution IoT.

En plus de la détection automatique des relations, vous pouvez choisir d’autres groupes de ressources Azure à étiqueter comme faisant partie de votre solution IoT.

Vos sélections vous permettent d’ajouter des abonnements, groupes de ressources ou ressources uniques dans leur intégralité.

Après avoir défini toutes les relations de ressource, Azure Security Center pour IoT s’appuie sur Azure Security Center en vue de fournir des recommandations de sécurité et des alertes pour ces ressources.

## <a name="add-azure-resources-to-your-iot-solution"></a>Ajouter des ressources Azure à votre solution IoT

Pour ajouter une nouvelle ressource à votre solution IoT, suivez ces étapes : 

1. Ouvrez votre **hub IoT** dans le portail Azure. 
2. Sélectionnez et ouvrez **Ressources** sous **Sécurité** dans le menu de gauche. 
3. Sélectionnez **Modifier**, puis choisissez les groupes de ressources appartenant à votre solution IOT.
5. Cliquez sur **Add**. 

Félicitations ! Vous avez ajouté un nouveau groupe de ressources à votre solution IoT.

Azure Security Center pour IoT supervise désormais les groupes de ressources que vous avez récemment ajoutés, et expose les alertes et recommandations de sécurité pertinentes dans le cadre de votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment créer des modules de sécurité...

> [!div class="nextstepaction"]
> [Créer des modules de sécurité](quickstart-create-security-twin.md)