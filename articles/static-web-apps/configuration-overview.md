---
title: Vue d’ensemble de la configuration pour Azure Static Web Apps
description: Découvrez les différentes façons de configurer Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 34a93dc1203fb404d0d6c2edfd70954545e26b15
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154923"
---
# <a name="configuration-overview"></a>Présentation de la configuration

Les différents concepts suivants s’appliquent à la configuration d’une application web statique.

- [Configuration de l’application](./configuration.md) : définissez des règles dans le fichier `staticwebapp.config.json` pour contrôler le comportement et les fonctionnalités de l’application. Utilisez ce fichier pour définir les règles de routage et de sécurité, les en-têtes personnalisés et les paramètres de mise en réseau.

- [Configuration de build](./build-configuration.md) : définissez les paramètres qui contrôlent le processus de génération.

- [Paramètres d’application](./application-settings.md) : définissez les paramètres de niveau application et les variables d’environnement qui peuvent être utilisés par les API backend.

## <a name="example-scenarios"></a>Exemples de scénarios

| Pour... | alors... |
|---|---|
| Définir les règles de routage | [Créer des règles dans le fichier staticwebapps.config.json](./configuration.md) |
| Définir la branche qui déclenche les builds | [Mettre à jour le nom de la branche suivie dans le fichier de configuration de build](./build-configuration.md)  |
| Définir les rôles de sécurité qui ont accès à un itinéraire | [Sécuriser les itinéraires avec des rôles dans le fichier staticwebapps.config.json](./configuration.md#securing-routes-with-roles) |
| Définir le fichier HTML qui est servi si un itinéraire ne correspond pas à un fichier réel | [Définir l’itinéraire de secours dans le fichier staticwebapps.config.json](./configuration.md#fallback-routes) |
| Définir des en-têtes globaux pour les requêtes HTTP | [Définir des en-têtes globaux dans le fichier staticwebapps.config.json](./configuration.md#global-headers)|
| Définir une commande de génération personnalisée | [Définir une valeur de commande de génération personnalisée dans le fichier de configuration de l’application](./build-configuration.md) |
| Définir une variable d’environnement pour une build frontale | [Définir une variable d’environnement dans le fichier de configuration de build](./build-configuration.md#environment-variables) |
| Définir une variable d’environnement pour une API | [Définir un paramètre d’application dans le portail](./application-settings.md) |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de l’application](configuration.md)
