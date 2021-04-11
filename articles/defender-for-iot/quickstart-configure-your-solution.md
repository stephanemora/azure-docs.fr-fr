---
title: 'Démarrage rapide : Ajouter des ressources Azure à votre solution IoT'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment configurer votre solution IoT de bout en bout avec Azure Defender pour IoT.
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 6a90e8c3007f7b3448fd3f1b6e4fa46ba861081b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384578"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Démarrage rapide : Configurer votre solution Azure Defender pour IoT

Cet article explique comment effectuer la configuration initiale de votre solution de sécurité IoT à l’aide de Defender pour IoT.

## <a name="prerequisites"></a>Prérequis

- Aucun

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

Allez à l’article suivant pour découvrir comment créer des micro-agents Defender-IoT...

> [!div class="nextstepaction"]
> [Créer des micro-agents Defender-IoT](quickstart-create-security-twin.md)
