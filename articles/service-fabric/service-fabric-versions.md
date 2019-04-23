---
title: Prise en charge des versions de cluster dans Azure Service Fabric | Microsoft Docs
description: En savoir plus sur les versions de cluster dans Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998819"
---
# <a name="supported-service-fabric-versions"></a>Versions de Service Fabric prises en charge

Assurez-vous que votre cluster exécute toujours une version d’Azure Service Fabric prises en charge. Un minimum de 60 jours après son nous annonçons le lancement d’une nouvelle version de Service Fabric, prise en charge de la version précédente se termine. Vous trouverez des annonces de nouvelles versions sur le [blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consultez les documents suivants pour plus d’informations sur la façon de garder votre cluster exécute une version de Service Fabric prises en charge :

- [Mise à niveau un cluster Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Mettre à niveau la version de Service Fabric qui s’exécute sur votre cluster autonome Windows Server](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versions prises en charge

Le tableau suivant répertorie les versions de Service Fabric et leurs dates de fin de prise en charge.

| Runtime service Fabric dans le cluster | Peut mettre à niveau directement à partir de la version de cluster |Version de package NuGet ou de kit de développement logiciel compatible | Fin de la prise en charge |
| --- | --- |--- | --- |
| Toutes les versions antérieures à 5.3.121 du cluster | 5.1.158.* |Inférieure ou égale à la version 2.3 |20 janvier 2017 |
| 5.3.* | 5.1.158.* |Inférieure ou égale à la version 2.3 |24 février 2017 |
| 5.4.* | 5.1.158.* |Inférieure ou égale à la version 2.4 |10 mai 2017       |
| 5.5.* | 5.4.164.* |Inférieure ou égale à la version 2.5 |10 août 2017    |
| 5.6.* | 5.4.164.* |Inférieure ou égale à la version 2.6 |13 octobre 2017   |
| 5.7.* | 5.4.164.* |Inférieure ou égale à la version 2.7 |15 décembre 2017  |
| 6.0.* | 5.6.205.* |Inférieure ou égale à la version 2.8 |30 mars 2018     |
| 6.1.* | 5.7.221.* |Inférieure ou égale à la version 3.0 |15 juillet 2018      |
| 6.2.* | 6.0.232.* |Inférieure ou égale à la version 3.1 |26 octobre 2018   |
| 6.3.* | 6.1.480.* |Inférieure ou égale à la version 3.2 |31 mars 2019  |
| 6.4.* | 6.2.301.* |Inférieure ou égale à la version 3.3 |Version actuelle, par conséquent, aucune date de fin |

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau suivant répertorie les systèmes d’exploitation pris en charge pour les versions de Service Fabric prises en charge.

| Système d’exploitation | Plus ancienne version de Service Fabric prise en charge |
| --- | --- |
| Windows Server 2012 R2 | Toutes les versions |
| Windows Server 2016 | Toutes les versions |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4. |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

