---
title: Utiliser l’interface CLI Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Découvrez comment utiliser l’interface CLI Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 34afc732d64a7aa29261989cc790c8a113208d96
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331768"
---
# <a name="use-the-azure-digital-twins-cli"></a>Utiliser l’interface CLI Azure Digital Twins

En plus de gérer votre instance Azure Digital Twins dans le portail Azure, vous pouvez utiliser l’ **interface de ligne de commande (CLI)** d’Azure Digital Twins pour effectuer la plupart des actions principales dans le service, notamment :
* Gestion d’une instance Azure Digital Twins
* Gestion des modèles
* Gestion des jumeaux numériques
* Gestion des relations entre les jumeaux
* Configuration de points de terminaison
* Gestion des [routes](concepts-route-events.md)
* Configuration de la [sécurité](concepts-security.md) via le contrôle d’accès en fonction du rôle Azure (Azure RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Utilisations (déployer et valider)

En plus de la gestion générale de votre instance, l’interface CLI vous permet également d’effectuer des déploiements et des validations.
* Les commandes de plan de contrôle peuvent être utilisées pour rendre le déploiement d’une nouvelle instance reproductible ou automatisé.
* Les commandes de plan de données peuvent être utilisées pour vérifier rapidement les valeurs de votre instance et vérifier que les opérations se sont terminées comme prévu.

## <a name="get-the-extension"></a>Obtient l’extension

Les commandes Azure Digital Twins font partie de l’[extension Azure IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Vous pouvez afficher la liste complète des commandes et leur utilisation dans le cadre de la documentation de référence pour l’ensemble de commandes `az iot` : [*az dt* , informations de référence sur la commande](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest).

Vous pouvez vous assurer de disposer de la version la plus récente de l’extension en procédant comme suit. Vous pouvez exécuter ces commandes dans [Azure Cloud Shell](../cloud-shell/overview.md) ou dans une [interface de ligne de commande Azure locale](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Étapes suivantes

Explorez l’interface CLI et son ensemble complet de commandes via les documents de référence :
* [*az dt* , informations de référence sur les commandes](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)