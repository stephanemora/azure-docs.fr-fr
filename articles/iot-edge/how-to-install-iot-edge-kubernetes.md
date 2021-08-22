---
title: Guide pratique pour installer IoT Edge sur Kubernetes | Microsoft Docs
description: Découvrez comment installer IoT Edge sur Kubernetes avec un environnement de cluster de développement local.
author: kgremban
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: c2ca09f036bbd15e87d6e633ba470e56637bf272
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531375"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Guide pratique pour installer IoT Edge sur Kubernetes (préversion)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge peut s’intégrer à Kubernetes avec une couche d’infrastructure résiliente et hautement disponible. C’est ici que s'inscrit cette prise en charge dans une solution de haut niveau IoT Edge :

![k8s : présentation](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Dans le cadre de cette intégration, un bon modèle mental consiste à considérer Kubernetes comme un autre environnement d’exploitation où les applications IoT Edge peuvent s’exécuter en plus de Linux et Windows.

## <a name="architecture"></a>Architecture 
Sur Kubernetes, IoT Edge fournit une *définition de ressource personnalisée* (CRD) pour les déploiements de charge de travail de périphérie. L'agent IoT Edge endosse le rôle de *Contrôleur CRD* qui rapproche l’état souhaité géré par le cloud de l’état du cluster local.

La durée de vie des modules est gérée par le planificateur Kubernetes, qui maintient leur disponibilité et choisit leur positionnement. IoT Edge administre la plateforme d’applications de périphérie qui s’exécute dessus, réconciliant en permanence l’état souhaité spécifié dans IoT Hub avec l’état présent sur le cluster de périphérie. Le modèle d’application est toujours celui qui s’appuie sur les itinéraires et les modules IoT Edge. Le contrôleur de l’agent IoT Edge effectue une traduction *automatique* du modèle d'application IoT Edge vers les constructions Kubernetes natives comme les pods, les déploiements, les services, etc.

Voici un diagramme de l’architecture de haut niveau :

![Arche Kubernetes](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Chacun des composants du déploiement de périphérie est limité à un espace de noms Kubernetes propre à l’appareil, ce qui permet de partager les mêmes ressources de cluster entre plusieurs appareils de périphérie et leurs déploiements.

>[!NOTE]
>IoT Edge sur Kubernetes est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Tutoriels et références 

Pour plus d’informations, consultez le [minisite regroupant des documents de préversion sur IoT Edge sur Kubernetes](https://aka.ms/edgek8sdoc), y compris des didacticiels et références approfondis.
