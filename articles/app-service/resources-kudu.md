---
title: Présentation du service Kudu
description: Découvrez le moteur qui soutient le déploiement en continu dans App Service et ses fonctionnalités.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608590"
---
# <a name="kudu-service-overview"></a>Présentation du service Kudu

Kudu est le moteur derrière un certain nombre de fonctionnalités d’[Azure App service](overview.md) liées au déploiement basé sur le contrôle de code source, ainsi que d’autres méthodes de déploiement telles que la synchronisation Dropbox et OneDrive. 

## <a name="access-kudu-for-your-app"></a>Accéder à Kudu pour votre application
Chaque fois que vous créez une application, App Service crée une application auxiliaire qui est sécurisée par HTTPS. Cette application Kudu est accessible sur :

- Application absente du niveau Isolé : `https://<app-name>.scm.azurewebsites.net`
- Application au niveau Isolé (App Service Environment) : `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Pour plus d’informations, consultez l’article [Accéder au service Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Fonctionnalités de Kudu

Kudu fournit des informations utiles sur votre application App Service, notamment les suivantes :

- Paramètres de l’application
- Chaînes de connexion
- Variables d'environnement
- Variables de serveur
- En-têtes HTTP

Il fournit également d’autres fonctionnalités, dont les suivantes :

- Exécuter des commandes dans la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Télécharger les copies de sauvegarde des diagnostics IIS ou les journaux Docker.
- Gérer les processus IIS et les extensions de site.
- Ajouter des webhooks de déploiement pour les applications Windows.
- Donne accès à l’interface utilisateur de déploiement ZIP avec `/ZipDeploy`.
- Génère des [scripts de déploiement personnalisés](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Permet l’accès avec l’[API REST](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>Ressources complémentaires

Kudu est un [projet open source](https://github.com/projectkudu/kudu) et a sa documentation sur le [Wiki Kudu](https://github.com/projectkudu/kudu/wiki).

