---
title: Utiliser l’interface CLI Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Découvrez comment utiliser l’interface CLI Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725293"
---
# <a name="use-the-azure-digital-twins-cli"></a>Utiliser l’interface CLI Azure Digital Twins

En plus de gérer votre instance Azure Digital Twins dans le portail Azure, vous pouvez utiliser l’**interface de ligne de commande (CLI)** d’Azure Digital Twins pour effectuer la plupart des actions principales dans le service, notamment :
* Gestion d’une instance Azure Digital Twins
* Gestion des modèles
* Gestion des jumeaux numériques
* Gestion des relations entre les jumeaux
* Configuration de points de terminaison
* Gestion des [routes](concepts-route-events.md)
* Configuration de la [sécurité](concepts-security.md) via le contrôle d’accès en fonction du rôle (RBAC)

Les commandes Azure Digital Twins font partie de l’[extension Azure IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Vous pouvez consulter la documentation de référence sur ces commandes dans le cadre du jeu de commandes `az iot` : [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Déployer et valider

En plus de la gestion générale de votre instance, l’interface CLI vous permet également d’effectuer des déploiements et des validations.
* Les commandes de plan de contrôle peuvent être utilisées pour rendre le déploiement d’une nouvelle instance reproductible ou automatisé.
* Les commandes de plan de données peuvent être utilisées pour vérifier rapidement les valeurs de votre instance et vérifier que les opérations se sont terminées comme prévu.

## <a name="next-steps"></a>Étapes suivantes

Pour une alternative aux commandes CLI, découvrez comment gérer une instance Azure Digital Twins à l’aide des API et des SDK :
* [Guide pratique pour Utiliser les API et les kits SDK Azure Digital Twins](how-to-use-apis-sdks.md)
