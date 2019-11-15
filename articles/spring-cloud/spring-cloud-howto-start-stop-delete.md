---
title: Guide pratique pour démarrer, arrêter et supprimer votre application Azure Spring Cloud | Microsoft Docs
description: Guide pratique pour démarrer, arrêter et supprimer votre application Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607719"
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

Installez l’extension Spring Cloud pour Azure CLI :

```azurecli
az extension add --name spring-cloud
```

* Pour démarrer votre application :
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour arrêter votre application :
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour redémarrer votre application :
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour supprimer votre application :
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
