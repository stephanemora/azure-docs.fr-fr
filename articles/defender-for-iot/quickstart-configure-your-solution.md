---
title: 'Démarrage rapide : Ajouter des ressources Azure à votre solution IoT'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment configurer votre solution IoT de bout en bout avec Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: Shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: afe62e5cf255df28ea395405fc894ec5c15bb18c
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449711"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Démarrage rapide : Configurer votre solution Azure Defender pour IoT

Cet article explique comment effectuer la configuration initiale de votre solution de sécurité IoT à l’aide de Defender pour IoT.

## <a name="prerequisites"></a>Prérequis

Aucun

## <a name="what-is-defender-for-iot"></a>Qu’est-ce que Defender pour IoT ?

Defender pour IoT fournit aux solutions IoT basées sur Azure une sécurité complète de bout en bout.

Avec Defender pour IoT, vous pouvez superviser l’intégralité de votre solution IoT dans un seul tableau de bord exposant l’ensemble de vos appareils IoT, plateformes IoT et ressources back-end dans Azure.

Une fois activé sur votre hub IoT, Defender pour IoT identifie automatiquement les autres services Azure, qui sont également connectés à votre hub IoT et associés à votre solution IoT.

En plus de la détection automatique des relations, vous pouvez choisir d’autres groupes de ressources Azure à étiqueter comme faisant partie de votre solution IoT.

Vos sélections vous permettent d’ajouter des abonnements, groupes de ressources ou ressources uniques dans leur intégralité.

Après avoir défini toutes les relations de ressources, Defender pour IoT utilise Defender afin de fournir des alertes et des recommandations de sécurité pour ces ressources.

## <a name="add-azure-resources-to-your-iot-solution"></a>Ajouter des ressources Azure à votre solution IoT

Pour ajouter une nouvelle ressource à votre solution IoT :

1. Ouvrez votre **hub IoT** dans le portail Azure.

1. Sous **Sécurité**, sélectionnez **Vue d’ensemble** suivi de **Paramètres**, puis sélectionnez **Ressources supervisées**.

1. Sélectionnez **Modifier**, puis les ressources supervisées appartenant à votre solution IoT.

1. Sélectionnez **Ajouter**.

Félicitations ! Vous avez ajouté un nouveau groupe de ressources à votre solution IoT.

Defender pour IoT supervise désormais les groupes de ressources que vous avez récemment ajoutés, et expose les alertes et recommandations de sécurité pertinentes dans le cadre de votre solution IoT.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment créer des modules de sécurité...

> [!div class="nextstepaction"]
> [Créer des modules de sécurité](quickstart-create-security-twin.md)
