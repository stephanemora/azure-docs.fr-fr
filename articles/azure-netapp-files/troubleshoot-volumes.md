---
title: Résoudre les erreurs liées au volume pour Azure NetApp Files | Microsoft Docs
description: Décrit les messages d’erreur et les résolutions qui peuvent vous aider à résoudre les problèmes avec les volumes Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: a9df7577e1426fb355995cd4486338003a753c86
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534628"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Résoudre les erreurs liées au volume pour Azure NetApp Files

Cet article décrit les messages d’erreur et les résolutions qui peuvent vous aider à résoudre les problèmes de double protocole pour Azure NetApp Files. 

## <a name="errors-for-volume-allocation"></a>Erreurs pour l’allocation de volume 

Lorsque vous créez un nouveau volume ou redimensionnez un volume existant dans Azure NetApp Files, Microsoft Azure alloue des ressources de stockage et de mise en réseau à votre abonnement. Il est possible que vous rencontriez des échecs d’allocation de ressources en raison d’une augmentation sans précédent de la demande de services Azure dans certaines régions.

Cette section explique les causes de certains échecs d’allocation courants et propose des solutions possibles.

|     Conditions d’erreur    |     Résolutions    |
|-|-|
|Erreur lors de la création de volumes ou du redimensionnement des volumes existants. <br> Message d’erreur : `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | L’erreur indique que le service a rencontré une erreur lors de la tentative d’allocation de ressources pour cette demande. <br> Réessayez l’opération après un certain temps. Contactez le support si le problème persiste.|
|Capacité de stockage ou de réseau insuffisante dans une région pour les volumes ordinaires. <br> Message d’erreur : `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | L’erreur indique que les ressources disponibles dans la région sont insuffisantes pour créer ou redimensionner des volumes. <br> Essayez l'une des solutions suivantes : <ul><li>Créez le volume sous un nouveau réseau virtuel. Cela permet d’éviter d’atteindre les limites de ressources liées à la mise en réseau.</li> <li>Recommencez l’opération un peu plus tard. Les ressources ont peut-être été libérées dans le cluster, la région ou la zone entretemps.</li></ul> |
|Capacité de stockage insuffisante lors de la création d’un volume avec les fonctionnalités réseau définies sur `Standard`. <br> Message d’erreur : `No storage available with Standard network features, for the provided VNet.` | L’erreur indique que les ressources disponibles dans la région sont insuffisantes pour créer des volumes avec des fonctionnalités de mise en réseau `Standard`. <br> Essayez l'une des solutions suivantes : <ul><li>Si les fonctionnalités réseau `Standard` ne sont pas requises, créez le volume avec les fonctionnalités réseau `Basic`.</li> <li>Essayez de créer le volume sous un nouveau réseau virtuel. Cela permet d’éviter d’atteindre les limites de ressources liées à la mise en réseau</li><li>Recommencez l’opération un peu plus tard.  Les ressources ont peut-être été libérées dans le cluster, la région ou la zone entretemps.</li></ul> |

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md) 
* [Configurer les fonctionnalités réseau d’un volume](configure-network-features.md)
