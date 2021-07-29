---
title: Superviser votre appareil Azure Stack Edge | Microsoft Docs
description: Décrit comment utiliser le portail Azure et l’interface utilisateur web locale pour superviser votre appareil Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 13a11bb23479653ccc88b6e1a2312ff8e6f33609
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110099655"
---
# <a name="monitor-your-azure-stack-edge"></a>Superviser votre appareil Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

Cet article explique comment superviser votre appareil Azure Stack Edge. Pour ce faire, vous pouvez utiliser le portail Azure ou l’interface utilisateur web locale. Utilisez le portail Azure pour voir les événements de l’appareil, configurer et gérer les alertes et voir les métriques. Utilisez l’interface utilisateur web locale sur votre appareil physique pour voir l’état des différents composants matériels de l’appareil.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Afficher les événements de l’appareil et les alertes correspondantes
> * Afficher l’état des composants matériels de l’appareil
> * Afficher les métriques de capacité et de transaction de votre appareil

## <a name="view-device-events"></a>Afficher les événements de l’appareil

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Afficher l’état de matériel

Effectuez les étapes suivantes dans l’interface utilisateur web locale pour afficher l’état des composants matériel de votre appareil.

1. Connectez-vous à l’interface utilisateur web locale de votre appareil.
2. Allez dans **Maintenance > État du matériel**. Vous pouvez afficher l’intégrité des différents composants de l’appareil.

    ![Afficher l’état de matériel](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Afficher les mesures

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Métriques sur votre appareil

Cette section décrit les métriques de surveillance sur votre appareil. Les métriques peuvent être :

* Métriques de capacité. Les métriques de capacité sont liées à la capacité de l’appareil.

* Métriques de transaction. Les métriques de transaction sont liées aux opérations de lecture et d’écriture dans Stockage Azure.

* Métriques Edge Computing. Les métriques Edge Computing sont liées à l’utilisation du computing en périphérie sur votre appareil.

La liste complète des métriques est présentée dans le tableau suivant :

|Métriques de capacité                     |Description  |
|-------------------------------------|-------------|
|**Capacité disponible**               | Fait référence à la taille des données qui peuvent être écrites sur l’appareil. En d’autres termes, cette métrique est la capacité qui peut être rendue disponible sur l’appareil. <br></br>Vous pouvez libérer la capacité de l’appareil en supprimant la copie locale des fichiers dont il existe une copie sur l’appareil et sur le cloud.        |
|**Capacité totale**                   | Fait référence au nombre total d’octets sur l’appareil sur lequel écrire des données (également appelé taille totale du cache local). <br></br> Vous pouvez désormais augmenter la capacité d’un appareil virtuel existant en ajoutant un disque de données. Ajoutez un disque de données par le biais de la gestion de l’hyperviseur pour la machine virtuelle, puis redémarrez celle-ci. Le pool de stockage local de l’appareil de passerelle se développe de manière à accueillir le disque de données nouvellement ajouté. <br></br>Pour plus d’informations, consultez [Ajouter un disque dur pour la machine virtuelle Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Métriques de transaction              | Description         |
|-------------------------------------|---------|
|**Octets cloud chargés (appareil)**    | Somme de tous les octets chargés sur tous les partages de votre appareil        |
|**Octets cloud chargés (partage)**     | Octets chargés par partage. Cette métrique peut être : <br></br> Moyenne, à savoir (somme de tous les octets chargés par partage/nombre de partages)  <br></br>Maximum, à savoir le nombre maximal d’octets chargés à partir d’un partage <br></br>Minimum, à savoir le nombre minimal d’octets chargés à partir d’un partage      |
|**Débit de téléchargement cloud (partage)**| Octets téléchargés par partage. Cette métrique peut être : <br></br> Moyenne, à savoir (somme de tous les octets lus ou téléchargés sur un partage/nombre de partages) <br></br> Maximum, à savoir le nombre maximal d’octets téléchargés à partir d’un partage<br></br> Minimum, à savoir le nombre minimal d’octets téléchargés à partir d’un partage  |
|**Débit de lecture cloud**            | Somme de tous les octets lus à partir du cloud sur tous les partages de votre appareil     |
|**Débit de chargement cloud**          | Somme de tous les octets écrits dans le cloud sur tous les partages de votre appareil     |
|**Débit de chargement cloud (partage)**  | Somme de tous les octets écrits dans le cloud à partir d’un partage/nombre de partages, exprimée en valeur moyenne, maximale ou minimale par partage      |
|**Débit de lecture (réseau)**           | Inclut le débit réseau système pour tous les octets lus à partir du cloud. Cette vue peut inclure des données qui ne sont pas limitées aux partages. <br></br>La division affiche le trafic sur toutes les cartes réseau de l’appareil, y compris celles qui ne sont pas connectées ou activées.      |
|**Débit d’écriture (réseau)**       | Inclut le débit réseau système pour tous les octets écrits dans le cloud. Cette vue peut inclure des données qui ne sont pas limitées aux partages. <br></br>La division affiche le trafic sur toutes les cartes réseau de l’appareil, y compris celles qui ne sont pas connectées ou activées.          |

| Métriques Edge Computing              | Description         |
|-------------------------------------|---------|
|**Computing en périphérie - Utilisation de la mémoire**      |           |
|**Computing en périphérie - Pourcentage du processeur**    |         |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [gérer la bande passante](azure-stack-edge-manage-bandwidth-schedules.md).
Découvrez comment [gérer les notifications d’alerte d’événement d’appareil](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
