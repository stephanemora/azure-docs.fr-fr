---
title: Guide pratique pour démarrer, arrêter et supprimer votre application Azure Spring Cloud | Microsoft Docs
description: Guide pratique pour démarrer, arrêter et supprimer votre application Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038438"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Guide pratique pour démarrer, arrêter et supprimer votre application Azure Spring Cloud

Ce guide explique comment changer l’état d’une application dans Azure Spring Cloud en utilisant le portail Azure ou Azure CLI.

## <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Une fois que vous avez déployé une application, vous pouvez la **démarrer**, l’**arrêter** et la **supprimer** avec le portail Azure.

1. Accédez à votre instance du service Azure Spring Cloud dans le portail Azure.
1. Sélectionnez l’onglet **Tableau de bord de l’application**.
1. Sélectionnez l’application dont vous voulez changer l’état.
2. Dans la page **Vue d’ensemble** pour cette application, recherchez les boutons pour **Démarrer/Arrêter**, **Redémarrer** et **Supprimer** l’application.

## <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

> [!NOTE]
> Vous pouvez utiliser des paramètres facultatifs et configurer des valeurs par défaut avec Azure CLI. Découvrez plus d’informations à ce sujet en lisant notre [documentation de référence](spring-cloud-cli-reference.md).

* Pour démarrer votre application :
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour arrêter votre application :
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour redémarrer votre application :
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour supprimer votre application :
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
