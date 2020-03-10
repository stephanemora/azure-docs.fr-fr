---
title: Adresses IP Azure Integration Runtime
description: Découvrez les adresses IP à partir desquelles vous devez autoriser le trafic entrant, afin de configurer correctement les pare-feu pour sécuriser l’accès réseau aux magasins de données.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 842fcaf6ea06ed4aaaff04f6bb5d7d38a672573c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162349"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Adresses IP Azure Integration Runtime

Les adresses IP qu’Azure Integration Runtime utilise dépendent de la région où se trouve votre runtime d’intégration Azure. *Tous* les runtimes d’intégration Azure qui se trouvent dans une même région utilisent les mêmes plages d’adresses IP.

> [!IMPORTANT]  
> Les flux de données n’utilisent pas actuellement ces adresses IP. 
>
> Vous pouvez utiliser ces plages d’adresses IP pour le déplacement des données, le pipeline et les exécutions d’activités externes. Ces plages d’adresses IP peuvent être utilisées pour la mise en liste verte dans les magasins de données/groupes de sécurité réseau (NSG)/pare-feu pour l’accès entrant à partir du runtime d’intégration Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Adresses IP d’Azure Integration Runtime : Régions spécifiques

Autorisez le trafic provenant des adresses IP listées pour le runtime d’intégration Azure dans la région Azure spécifique où vos ressources sont situées :

|                | Région              | Adresses IP                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Asia           | Asie Est           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Asie Sud-Est      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Australie      | Australie Est      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Sud-Australie Est | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brésil         | Brésil Sud        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | Centre du Canada      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Europe         | Europe Nord        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europe Ouest         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| France         | France Centre      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Inde          | Inde centrale       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japon          | Japon Est          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Corée du Sud          | Centre de la Corée       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Afrique du Sud   | Afrique du Sud Nord  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Royaume-Uni | Sud du Royaume-Uni            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| États-Unis  | USA Centre          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | USA Est             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | USA Est 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | USA Est 2 (EUAP)      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Centre-Nord des États-Unis    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | États-Unis - partie centrale méridionale    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Centre-USA Ouest     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | USA Ouest             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | USA Ouest 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | Gouvernement américain - Virginie     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Problème connu avec le stockage Azure

* Lors de la connexion au compte de stockage Azure, les règles de réseau IP n’ont aucun effet sur les demandes provenant du runtime d’intégration Azure dans la même région que le compte de stockage. Pour plus d’informations, [consultez cet article](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Au lieu de cela, nous vous suggérons d’utiliser des [services approuvés pour vous connecter au stockage Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Étapes suivantes

* [Considérations relatives à la sécurité des déplacements de données dans Azure Data Factory](data-movement-security-considerations.md)
