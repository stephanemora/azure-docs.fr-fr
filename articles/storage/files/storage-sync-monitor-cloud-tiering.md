---
title: Surveiller la hiérarchisation Cloud d’Azure File Sync | Microsoft Docs
description: Détails sur les métriques à utiliser pour surveiller vos stratégies de hiérarchisation Cloud.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c27916afb0d199bcb32db9d43202e552a4a04f53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593133"
---
# <a name="monitor-cloud-tiering"></a>Superviser la hiérarchisation cloud
Il existe deux façons de surveiller votre stratégie de hiérarchisation Cloud : le panneau des propriétés du point de terminaison de serveur et Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Surveillance via un point de terminaison de serveur

Connectez-vous au [Portail Azure](https://portal.azure.com/), puis accédez au panneau des **propriétés du point de terminaison de serveur** pour le point de terminaison de serveur que vous souhaitez surveiller, puis faites défiler jusqu’à la section de hiérarchisation Cloud. 

![Capture d’écran de la section de hiérarchisation Cloud dans Propriétés du point de terminaison de serveur.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**L’économie d’espace** correspond à la quantité d’espace économisé en activant la hiérarchisation Cloud. Si vous avez suffisamment de volume pour contenir toutes vos données, vous aurez une économie d’espace de 0 %. Si vous avez 0 % ou un faible pourcentage d’économie d’espace, cela peut indiquer que la hiérarchisation Cloud n’est pas avantageuse avec la taille actuelle du cache local. 

**Le taux de correspondance dans le cache** est le pourcentage de fichiers que vous ouvrez dans votre cache local. Le taux de correspondance dans le cache est calculé par les fichiers ouverts directement à partir du cache/nombre total de fichiers ouverts. Un taux de correspondance dans le cache de 100 % signifie que tous les fichiers que vous avez ouverts au cours de la dernière heure étaient déjà dans votre cache local. Si votre objectif est de réduire la sortie, cela signifie que votre stratégie actuelle fonctionne bien.

> [!NOTE]
> Les charges de travail avec des modèles d’accès aléatoire ont généralement de correspondance dans le cache inférieur. 

**La taille totale (Cloud)** correspond à la taille de vos fichiers qui ont été synchronisés dans le Cloud. 

**La taille mise en cache (serveur)** correspond à la taille totale des fichiers sur votre serveur, à la fois téléchargés et hiérarchisés. Parfois, la taille mise en cache peut être supérieure à la taille totale de vos fichiers dans le Cloud. Des variables comme la taille de cluster du volume sur le serveur peuvent en être à l’origine. Si la taille mise en cache est supérieure à celle que vous souhaitez, envisagez d’augmenter votre stratégie d’espace libre du volume. 

**La stratégie de volume effective** est utilisée par Azure File Sync pour déterminer la quantité d’espace libre à conserver sur le volume sur lequel se trouve le point de terminaison de votre serveur. Lorsqu’il existe plusieurs points de terminaison de serveur sur le même volume, la stratégie d’espace libre du volume le plus élevé parmi les points de terminaison de serveur devient la stratégie de volume effective pour tous les points de terminaison de serveur sur ce volume. Par exemple, s’il existe deux points de terminaison de serveur sur le même volume, l’un avec 30 % d’espace libre de volume et l’autre avec 50 %, la stratégie d’espace libre du volume effectif pour les deux points de terminaison de serveur sera de 50 %.

**L’espace libre du volume actuel** est l’espace libre du volume actuellement disponible sur votre serveur local. Si vous disposez d’un niveau de sortie élevé, mais qu’il n’y a plus d’espace libre de volume disponible avant que la stratégie d’espace libre du volume ne démarre, envisagez de désactiver votre stratégie de date. Un autre problème pourrait être que, parmi les fichiers actuellement hiérarchisés, le fichier le plus récemment consulté est plus volumineux que l’espace libre restant avant l’entrée en vigueur de la politique. Dans ce cas, envisagez d’augmenter la taille du volume local. 

## <a name="monitoring-via-azure-monitor"></a>Surveillance via Azure Monitor

Accédez au **Service de synchronisation de stockage**, puis sélectionnez **Métriques** dans la navigation latérale. 

Vous pouvez utiliser trois métriques différentes pour surveiller votre sortie, en particulier à partir de la hiérarchisation Cloud :

- Taille de rappel de la hiérarchisation Cloud
    - Il s’agit de la taille totale des données rappelées (en octets). Elles peuvent être utilisées pour identifier la quantité de données qui fait l’objet d’un rappel.
- Taille de rappel de hiérarchisation Cloud par application
    - Il s’agit de la taille totale des données rappelées (en octets) par une application. Elles peuvent être utilisées pour identifier les éléments qui rappellent les données.
- Débit de rappel de la hiérarchisation Cloud
    - Cela mesure la vitesse à laquelle les données sont rappelées (en octets) et doivent être utilisées si vous rencontrez des problèmes de performances. 

Pour être plus précis sur ce que vous souhaitez que vos graphiques affichent, pensez à utiliser **Ajouter un filtre** et **Appliquer le fractionnement**.
 
Pour plus d’informations sur les différents types de métriques pour Azure File Sync et leur utilisation, consultez [Surveiller Azure file Sync](storage-sync-files-monitoring.md).

Pour plus d’informations sur l’utilisation des métriques, consultez [Prise en main d’Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md).

Si vous souhaitez modifier votre stratégie de hiérarchisation Cloud, consultez [Choisir des stratégies de hiérarchisation Cloud](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)