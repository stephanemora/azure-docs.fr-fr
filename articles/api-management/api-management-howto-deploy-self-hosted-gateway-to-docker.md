---
title: Déployer une passerelle de gestion des API Azure auto-hébergée sur Docker | Microsoft Docs
description: Découvrez comment déployer une passerelle de gestion des API Azure auto-hébergée sur Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768501"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Déployer une passerelle auto-hébergée de gestion des API Azure sur Docker

Cet article présente les étapes du déploiement de la passerelle de gestion des API Azure auto-hébergée dans un environnement Docker.

> [!NOTE]
> La fonctionnalité de passerelle auto-hébergée est en version préliminaire. Pendant la version préliminaire, la passerelle auto-hébergée est disponible uniquement pour les niveaux Développeur et Premium, sans frais supplémentaires. Le niveau Développeur est limité à un seul déploiement de passerelle auto-hébergée.

## <a name="prerequisites"></a>Conditions préalables requises

- Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Créez un environnement Docker. [Docker pour le bureau](https://www.docker.com/products/docker-desktop) est une bonne option en matière de développement et d’évaluation. Pour plus d’informations sur toutes les éditions de Docker, leurs fonctionnalités et une documentation complète sur Docker, voir la [Documentation Docker](https://docs.docker.com).
- [Approvisionnez une ressource de passerelle dans votre instance Gestion des API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> La passerelle auto-hébergée se présente sous la forme d’un conteneur Docker basé sur Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Déployer une passerelle auto-hébergée sur Docker

1. Sélectionnez **Passerelles** sous **Paramètres**.
2. Sélectionnez la ressource de passerelle que vous souhaitez déployer.
3. Sélectionnez **Déploiement**.
4. Notez qu’un nouveau jeton dans la zone de texte **Jeton** a été généré automatiquement pour vous avec les valeurs par défaut de **Expiration** et **Clé secrète**. Ajustez l’un ou l’autre, ou les deux, si vous le souhaitez, puis sélectionnez **Générer** pour créer un autre jeton.
4. Assurez-vous que **Docker** est sélectionné sous **Scripts de déploiement**.
5. Sélectionnez le lien de fichier **env. conf** en regard de l’**Environnement** pour télécharger le fichier.
6. Sélectionnez l’icône **Copier** située à l’extrémité droite de la zone de texte **Exécuter** pour enregistrer la commande Docker dans le Presse-papiers.
7. Collez la commande dans la fenêtre (ou la commande) du terminal. Ajustez les mappages de port et le nom de conteneur si nécessaire. Notez que la commande s’attend à ce que le fichier d’environnement téléchargé soit présent dans le répertoire actif.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Exécutez la commande. La commande demande à votre environnement Docker d’exécuter le conteneur, à l’aide de l’image de la passerelle auto-hébergée qui a été téléchargée à partir de Microsoft Container Registry, et de mapper les ports HTTP (8080) et HTTPS (8081) du conteneur aux ports 80 et 443 sur l’hôte.
9. Exécutez la commande ci-dessous pour vérifier que le pod de la passerelle est en cours d’exécution :
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Revenez au portail Azure et vérifiez que le nœud de la passerelle que vous venez de déployer signale l’état d’intégrité.

![état de la passerelle](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Utilisez la commande <code>console docker container logs <gateway-name></code> pour afficher un instantané du journal de la passerelle auto-hébergée.
>
> Utilisez la commande <code>docker container logs --help</code> pour voir toutes les options d’affichage des journaux.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md).
* [Configurer un nom de domaine personnalisé pour la passerelle auto-hébergée](api-management-howto-configure-custom-domain-gateway.md).
