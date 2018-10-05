---
title: Adresses de gestion Azure App Service Environment
description: Répertorie les adresses de gestion utilisées pour la commande d’un environnement App Service
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: 590679daff20f9c469fb8fcfcc0fbbad77f91b5b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162848"
---
# <a name="app-service-environment-management-addresses"></a>Adresses de gestion App Service Environment

L’environnement App Service Environment (ASE) est un déploiement du service Azure App Service dans un sous-réseau de votre réseau virtuel Azure.  L’environnement ASE doit être accessible à partir du plan de gestion utilisé par Azure App Service.  Ce trafic de gestion ASE parcourt le réseau contrôlé par l’utilisateur. Si ce trafic est bloqué ou incorrectement routé, l’environnement ASE est suspendu. Pour plus d’informations sur les dépendances réseau d’un ASE, consultez [Considérations relatives à la mise en réseau pour un environnement App Service Environment][networking]. Pour des informations générales sur l’environnement ASE, vous pouvez commencer par consulter la page [Présentation de l’environnement App Service Environment][intro].

Ce document liste les adresses sources App Service pour le trafic de gestion vers l’environnement ASE et répond à deux objectifs importants.  

1. Vous pouvez utiliser ces adresses pour créer des groupes de sécurité réseau pour verrouiller le trafic entrant.  
2. Vous pouvez créer des itinéraires avec ces adresses pour prendre en charge les déploiements de tunnels forcés. Pour plus d’informations sur la configuration de l’environnement ASE dans un environnement où le trafic sortant est envoyé en local, consultez la page [Configurer un environnement ASE avec le tunneling forcé][forcedtunnel].

Tous les environnements ASE ont une adresse IP virtuelle publique, qui reçoit le trafic de gestion. Le trafic de gestion entrant provenant de ces adresses provient des ports 454 et 455 sur l’adresse IP virtuelle publique de votre environnement ASE.  

## <a name="list-of-management-addresses"></a>Liste des adresses de gestion ##

| Région | Adresses |
|--------|-----------|
| Toutes les régions publiques | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure Government (Fairfax ou MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>Récupérer les adresses de gestion avec l’API ##

Vous pouvez lister les adresses de gestion qui correspondent à votre environnement ASE avec l’appel d’API suivant.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

L’API retourne un document JSON qui contient toutes les adresses de trafic entrant de votre environnement ASE. La liste des adresses inclut les adresses de gestion, l’adresse IP virtuelle utilisée par votre environnement ASE et la plage d’adresses de sous-réseau ASE elle-même.  

Pour appeler l’API avec [armclient](http://github.com/projectkudu/ARMClient), utilisez les commandes suivantes en indiquant votre ID d’abonnement, votre groupe de ressources et le nom de l’environnement ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
