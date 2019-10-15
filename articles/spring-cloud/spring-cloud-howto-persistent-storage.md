---
title: Guide pratique pour utiliser le stockage persistant dans Azure Spring Cloud | Microsoft Docs
description: Guide pratique pour utiliser le stockage persistant dans Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038448"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Guide pratique pour utiliser le stockage persistant dans Azure Spring Cloud

Azure Spring Cloud fournit deux types de stockage pour votre application : persistant et temporaire.  Azure Spring Cloud active le stockage temporaire par défaut pour chaque instance d’application. Le stockage temporaire est limité à 5 Go et son chemin de montage par défaut est `/tmp`.

> [!WARNING]
> Le redémarrage d’une instance d’application va supprimer définitivement son stockage temporaire associé.

Le stockage persistant est un conteneur de partage de fichiers géré par Azure et alloué sur une étendue par application. Les données stockées dans le stockage persistant sont partagées entre toutes les instances de l’application. Une instance du service Azure Spring Cloud peut avoir un maximum de 10 applications avec un disque persistant activé et chaque application reçoit 50 Go de stockage persistant. Le chemin de montage par défaut pour le stockage persistant est `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Activer le stockage persistant avec le portail Azure

1. Dans la page de votre service Azure Spring Cloud, sélectionnez **Tableau de bord de l’application**, puis sélectionnez l’application qui nécessite un stockage persistant.
1. Sous l’onglet **Vue d’ensemble**, localisez l’attribut **Stockage persistant**, puis sélectionnez **Désactivé**.
1. Cliquez sur **Activer** pour activer le stockage persistant, puis sélectionnez le bouton **OK** pour appliquer la modification.

Quand le stockage persistant est activé, sa taille et son chemin sont montrés dans l’attribut **Stockage persistant** de la page **Vue d’ensemble**.

> [!WARNING]
> La *désactivation* du stockage persistant va désallouer le stockage pour cette application.  Toutes les données de ce compte de stockage sont alors perdues. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Activer le stockage persistant avec Azure CLI

Créez une application avec un disque persistant activé :
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Activez le stockage persistant dans une application existante :

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Désactivez le stockage persistant dans une application existante :

> [!WARNING]
> La désactivation du stockage persistant va désallouer le stockage pour cette application, ce qui entraîne la perte définitive de toutes les données qui y étaient stockées. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [quotas pour les applications et les services](spring-cloud-quotas.md) ou la [mise à l’échelle manuelle de votre application](spring-cloud-tutorial-scale-manual.md).