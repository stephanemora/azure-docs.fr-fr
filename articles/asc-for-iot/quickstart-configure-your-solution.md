---
title: Configurer votre solution ASC pour IoT (préversion) | Microsoft Docs
description: Découvrez comment configurer votre solution IoT de bout en bout avec ASC pour IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3ba8b1a099dc204730813754fdfc4b0b8ce45303
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541729"
---
# <a name="quickstart-configure-your-iot-solution"></a>Démarrage rapide : Configurer votre solution IoT

> [!IMPORTANT]
> ASC pour IoT est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment effectuer la configuration initiale de votre solution de sécurité IoT en utilisant ASC pour IoT. 

## <a name="asc-for-iot"></a>ASC pour IoT

ASC pour IoT fournit aux solutions IoT basées sur Azure une sécurité complète de bout en bout.

Avec ASC pour IoT, vous pouvez superviser l’ensemble de votre solution dans un tableau de bord, en exposant tous vos appareils IoT, les plateformes IoT et les ressources principales dans Azure.

Une fois activé sur votre hub IoT, ASC pour IoT identifie automatiquement les autres services Azure, qui sont également connectés à votre hub IoT et associés à votre solution IoT.

En plus de la détection automatique des relations, vous pouvez également choisir d’autres ressources Azure à étiqueter comme faisant partie de votre solution IoT.
Vos sélections vous permettent d’ajouter des abonnements, groupes de ressources ou ressources uniques dans leur intégralité.

Après avoir défini toutes les relations de ressource, ASC pour IoT s’appuie sur Azure Security Center en vue de fournir des recommandations de sécurité et des alertes pour ces ressources.

## <a name="add-azure-resources-to-your-iot-solution"></a>Ajouter des ressources Azure à votre solution IoT

Pour ajouter une nouvelle ressource à votre solution IoT, suivez ces étapes : 

1. Ouvrez votre **hub IoT** dans le portail Azure. 
2. Sélectionnez et ouvrez **Ressources** sous **Sécurité** dans le menu de gauche. 
3. Sélectionnez **Ajouter des ressources**.
4. Choisissez des ressources qui appartiennent à votre solution IoT.
4. Cliquez sur **Add**. 

Félicitations ! Vous avez ajouté une nouvelle ressource à votre solution IoT.

ASC pour IoT supervise désormais les ressources que vous avez récemment ajoutées, et expose alertes et recommandations de sécurité pertinentes dans le cadre de votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment créer des modules de sécurité...

> [!div class="nextstepaction"]
> [Créer des modules de sécurité](quickstart-create-security-twin.md)