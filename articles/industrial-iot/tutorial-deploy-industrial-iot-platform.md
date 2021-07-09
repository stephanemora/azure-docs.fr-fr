---
title: Déployer la plateforme Azure Industrial IoT
description: Dans ce tutoriel, vous allez apprendre à déployer la plateforme IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 995ee04e913ad4e363045e0aacc295aaec25f3e6
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110677923"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Tutoriel : Déployer la plateforme Azure Industrial IoT

Dans ce tutoriel, vous allez voir comment :

> [!div class="checklist"]
> * Les principaux composants de la plateforme IIoT.
> * Les différents types d’installation.
> * Comment déployer la plateforme Industrial IoT.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure doit être créé
- Télécharger [Git](https://git-scm.com/downloads)
- Les inscriptions d’applications Azure Active Directory (AAD) utilisées pour l’authentification nécessitent des droits d’Administrateur général, d’Administrateur d’application ou d’Administrateur d’application cloud pour fournir un consentement d’administrateur à l’échelle du locataire (voir ci-dessous pour obtenir des options supplémentaires)
- Les systèmes d’exploitation pris en charge pour le déploiement sont Windows, Linux et Mac
- IoT Edge prend en charge Windows 10 IoT Enterprise LTSC et Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Composants principaux

- Dépendances minimales : IoT Hub, Cosmos DB, Service Bus, Event Hub, Key Vault, Stockage
- Dépendances standard : Minimum + SignalR Service, inscriptions d’applications AAD, Service Device Provisioning, Time Series Insights, Workbook, Log Analytics, Application Insights
- Microservices : plan App Service, App Service
- Interface utilisateur (application web) : plan App Service (partagé avec des microservices), App Service
- Simulation : machine virtuelle, réseau virtuel, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Types d’installation

- Minimum : dépendances minimales
- Local : dépendances minimales et standard
- Services : local et microservices
- Simulation : dépendances minimales et composants de simulation
- Application : services et interface utilisateur
- Tout (par défaut) : application et simulation

## <a name="deployment"></a>Déploiement

1. Pour commencer le déploiement de la plateforme IIoT, clonez le dépôt à partir de l’invite de commandes ou du terminal.

    git clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. Démarrez le déploiement guidé. Le script collecte les informations requises, telles que le compte Azure, l’abonnement, la ressource cible et le nom du groupe et de l’application.

    Sur Windows :
        ```
        .\deploy -version <version>
        ```

    Sur Linux ou Mac :
        ```
        ./deploy.sh -version <version>
        ```

    Remplacez \<version> par la version à déployer.

3. Les microservices et l’interface utilisateur sont des applications web qui requièrent une authentification. Cela nécessite trois inscriptions d’applications dans AAD. Si les droits requis sont manquants, il existe deux solutions possibles :

    - Demander à l’administrateur AAD d’accorder le consentement de l’administrateur au niveau du locataire pour l’application
    - Un administrateur AAD peut créer les applications AAD. Le dossier deploy/scripts contient le script aad- register.ps1 pour effectuer l’inscription AAD séparément du déploiement. La sortie du script est un fichier contenant les informations pertinentes à utiliser dans le cadre du déploiement. Il doit être passé au script deploy.ps1 dans le même dossier à l’aide de l’argument - aadConfig.
        ```bash
        cd deploy/scripts
        ./aad-register.ps1 -Name <application-name> -Output aad.json
        ./deploy.ps1 -aadConfig aad.json
        ```

Pour les déploiements de production qui requièrent la mise en lots, la restauration, la mise à l’échelle et la résilience, la plateforme peut être déployée dans [AKS (Azure Kubernetes Service)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Références :
- [Deploying Azure Industrial IoT Platform](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [How to deploy all-in-one](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [How to deploy platform into AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez déployé la plateforme IIoT, vous pouvez découvrir comment personnaliser la configuration des composants :

> [!div class="nextstepaction"]
> [Personnaliser la configuration des composants](tutorial-configure-industrial-iot-components.md)
