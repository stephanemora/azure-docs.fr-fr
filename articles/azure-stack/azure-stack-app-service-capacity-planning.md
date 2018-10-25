---
title: Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack | Microsoft Docs
description: Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: a769bb4cce84fe78f442cce8440e6e828ed7f76d
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354136"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour configurer un déploiement prêt pour la production d’Azure App Service sur Azure Stack, vous devez planifier la capacité que vous attendez que le système prenne en charge.  

Cet article fournit des instructions pour le nombre minimal d’instances de calcul et de références SKU de calcul à utiliser pour un déploiement de production.

Vous pouvez planifier votre stratégie de capacité App Service à l’aide de ces instructions.

| Rôle serveur App Service | Nombre d’instances minimal recommandé | Référence SKU de calcul recommandée|
| --- | --- | --- |
| Contrôleur | 2 | A1 |
| Front-end | 2 | A1 |
| Gestion | 2 | A3 |
| Serveur de publication | 2 | A1 |
| Rôles de travail - partagés | 2 | A1 |
| Rôles de travail - dédiés | 2 par niveau | A1 |

## <a name="controller-role"></a>rôle de contrôleur

**Minimum recommandé** : deux instances de A1 standard

Le contrôleur Azure App Service consomme généralement peu d’UC, de mémoire et de ressources réseau. Toutefois, pour la haute disponibilité, vous devez disposer de deux contrôleurs. Deux contrôleurs sont également le nombre maximal de contrôleurs autorisé. Vous pouvez créer le second contrôleur de sites web directement à partir du programme d’installation lors du déploiement.

## <a name="front-end-role"></a>Rôle du front-end

**Minimum recommandé** : deux instances de A1 standard

Le front-end route les requêtes vers les traitements web en fonction de leur disponibilité. Pour une haute disponibilité, vous devez disposer de plusieurs front-ends. À des fins de planification de la capacité, notez que chaque cœur peut traiter approximativement 100 demandes par seconde.

## <a name="management-role"></a>Rôle de gestion

**Minimum recommandé** : deux instances de A3 standard

Le rôle de gestion Azure App Service est responsable des points de terminaison App Service Azure Resource Manager, des points de terminaison des API, des extensions du portail (administration, locataire, portail Functions) et du service des données. Le rôle de serveur de gestion ne nécessite généralement que 4 Go de RAM environ dans un environnement de production. Toutefois, l’utilisation de l’UC peut augmenter lorsque de nombreuses tâches de gestion (par exemple, la création de sites web) sont effectuées. Pour une haute disponibilité, vous devez disposer de plusieurs serveurs affectés à ce rôle, et au moins deux cœurs par serveur.

## <a name="publisher-role"></a>Rôle de serveur de publication

**Minimum recommandé** : deux instances de A1 standard

Si de nombreux utilisateurs publient simultanément, le rôle de serveur de publication peut faire face à une utilisation élevée du processeur. Pour une haute disponibilité, vérifiez que plusieurs rôles de serveur de publication sont disponibles. Le serveur de publication gère uniquement le trafic FTP/FTPS.

## <a name="web-worker-role"></a>Rôle de travail

**Minimum recommandé** : deux instances de A1 standard

Pour une haute disponibilité, vous devez disposer d’au moins quatre rôles de traitement web : deux pour le mode de site web partagé et deux pour chaque niveau de travail dédié que vous prévoyez de proposer. Les modes de calcul partagé et dédié offrent différents niveaux de service aux locataires. Vous aurez peut-être besoin de traitements web supplémentaires si vous avez de nombreux clients :

- Utilisant des niveaux de travail en mode de calcul dédié (qui consomment beaucoup de ressources).
- S’exécutant en mode de calcul partagé.

Après qu’un utilisateur a créé un plan App Service pour une référence (SKU) en mode de calcul dédié, le nombre de traitements web spécifiés dans ce plan App Service n’est plus disponible pour les utilisateurs.

Pour fournir Azure Functions aux utilisateurs dans le modèle de plan de consommation, vous devez déployer des traitements web partagés.

Quand vous choisissez le nombre de traitements web partagés à utiliser, passez en revue les considérations suivantes :

- **Mémoire** : la mémoire est la ressource la plus critique pour un rôle de traitement web. Une mémoire insuffisante a un impact sur les performances du site web lorsque la mémoire virtuelle est échangée à partir du disque. Chaque serveur nécessite environ 1,2 Go de RAM pour le système d’exploitation. La RAM au-dessus de ce seuil peut être utilisée pour exécuter des sites web.
- **Pourcentage de sites web actifs** : en général, environ 5 pourcent des applications d’un service Azure App Service sur un déploiement Azure Stack sont actives. Toutefois, le pourcentage d’applications actives à un moment donné peut être supérieur ou inférieur. Avec un taux d’applications actives de 5 %, le nombre maximal d’applications à placer dans un plan Azure App Service sur un déploiement Azure Stack doit être inférieur à 20 fois le nombre de sites web actifs (5 x 20 = 100).
- **Encombrement de mémoire moyen** : l’encombrement de mémoire moyen pour les applications observées dans les environnements de production est d’environ 70 Mo. En utilisant cet encombrement, la mémoire allouée sur l’ensemble des ordinateurs ou machines virtuelles du rôle de traitement web peut être calculée comme suit :

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Par exemple, s’il y a 5 000 applications dans un environnement qui exécute 10 rôles de traitement web, chaque machine virtuelle du rôle de traitement web doit disposer de 7 060 Mo de RAM :

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   Pour plus d’informations sur l’ajout d’instances de traitement supplémentaires, consultez [Ajout de rôles de travail supplémentaires](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Rôle de serveur de fichiers

Pour le rôle de serveur de fichiers, vous pouvez utiliser un serveur de fichiers autonome pour le développement et le test. Par exemple, lors du déploiement d’Azure App Service sur le Kit de développement Azure Stack (ASDK), vous pouvez utiliser ce modèle : https://aka.ms/appsvconmasdkfstemplate. À des fins de production, vous devez utiliser un serveur de fichiers Windows préconfiguré ou un serveur de fichiers préconfiguré autre que Windows.

Dans les environnements de production, le rôle de serveur de fichiers fait face à des E/S de disque intensives. Étant donné qu’il héberge tous les fichiers de contenu et d’application des sites web des utilisateurs, vous devez préconfigurer l’un des éléments suivants pour ce rôle :

- Serveur de fichiers Windows
- Cluster de serveurs de fichiers Windows
- Serveur de fichiers non Windows
- Cluster de serveurs de fichiers non Windows
- Appareil NAS (Network Attached Storage)

Pour plus d’informations, consultez la rubrique [Provisionner un serveur de fichiers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez l’article suivant :

[Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md)
