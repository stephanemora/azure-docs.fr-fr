---
title: 'Démarrage rapide : Configurer votre solution'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment configurer votre solution IoT de bout en bout avec Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 3ecb2e2022cab80abf6a8692ac8cc3ba54eff1e4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945083"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Démarrage rapide : Configurer votre solution Azure Defender pour IoT

Cet article explique comment effectuer la configuration initiale de votre solution de sécurité IoT à l’aide de Defender pour IoT.

## <a name="what-is-defender-for-iot"></a>Qu’est-ce que Defender pour IoT ?

Defender pour IoT fournit aux solutions IoT basées sur Azure une sécurité complète de bout en bout.

Avec Defender pour IoT, vous pouvez superviser l’ensemble de votre solution dans un tableau de bord exposant tous vos appareils IoT, plateformes IoT et ressources principales dans Azure.

Une fois activé sur votre hub IoT, Defender pour IoT identifie automatiquement les autres services Azure, qui sont également connectés à votre hub IoT et associés à votre solution IoT.

En plus de la détection automatique des relations, vous pouvez choisir d’autres groupes de ressources Azure à étiqueter comme faisant partie de votre solution IoT.

Vos sélections vous permettent d’ajouter des abonnements, groupes de ressources ou ressources uniques dans leur intégralité.

Après avoir défini toutes les relations de ressource, Defender pour IoT s’appuie sur Defender en vue de fournir des recommandations de sécurité et des alertes pour ces ressources.

## <a name="add-azure-resources-to-your-iot-solution"></a>Ajouter des ressources Azure à votre solution IoT

Pour ajouter une nouvelle ressource à votre solution IoT, suivez ces étapes :

1. Ouvrez votre **hub IoT** dans le portail Azure.
1. Sélectionnez et ouvrez **Paramètres** dans la section **Sécurité** dans le menu de gauche, puis sélectionnez **Ressources supervisées**.
1. Sélectionnez **Modifier**, puis les ressources supervisées appartenant à votre solution IoT.
1. Cliquez sur **Add**.

Félicitations ! Vous avez ajouté un nouveau groupe de ressources à votre solution IoT.

Defender pour IoT supervise désormais les groupes de ressources que vous avez récemment ajoutés, et expose les alertes et recommandations de sécurité pertinentes dans le cadre de votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment créer des modules de sécurité...

> [!div class="nextstepaction"]
> [Créer des modules de sécurité](quickstart-create-security-twin.md)
