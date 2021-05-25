---
title: Présentation de Microsoft Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit Azure Stack Edge Pro avec GPU, solution de stockage qui utilise un appareil physique pour le transfert vers Azure via le réseau.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/19/2021
ms.author: alkohli
ms.openlocfilehash: b56b79c29838c00fd79a0ed91a1c3ff0686c56ce
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109737975"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Qu’est-ce qu’Azure Stack Edge Pro avec GPU ?

Azure Stack Edge Pro avec GPU est un appareil de computing en périphérie qui prend en charge l’IA et qui présente des fonctionnalités de transfert de données via le réseau. Cet article vous fournit une vue d’ensemble de la solution Azure Stack Edge Pro, ses avantages, ses principales fonctionnalités et les scénarios dans lesquels vous pouvez déployer cet appareil.

Azure Stack Edge Pro avec GPU est une solution de matériel en tant que service. Microsoft fournit un appareil managé dans le cloud qui fait office de passerelle de stockage réseau et intègre un processeur graphique (GPU) permettant une inférence accélérée de l’IA. 

## <a name="use-cases"></a>Cas d'utilisation

Voici différents scénarios d’utilisation d’Azure Stack Edge Pro avec GPU pour l’inférence rapide de Machine Learning (ML) à la périphérie et le prétraitement des données avant leur envoi à Azure.

- **Inférence avec Azure Machine Learning** : Azure Stack Edge Pro avec GPU vous permet d’exécuter des modèles Machine Learning (ML) pour obtenir des résultats rapides qui peuvent être traités avant l’envoi des données vers le cloud. Le jeu de données complet peut également être transféré pour continuer à reformer et améliorer vos modèles ML. Pour plus d’informations sur l’utilisation des modèles accélérés matériellement d’Azure ML sur un appareil Azure Stack Edge Pro avec GPU, consultez [Déployer des modèles accélérés matériellement Azure ML sur Azure Stack Edge Pro avec GPU](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Prétraiter les données** : transformez les données avant de les envoyer à Azure via des options de calcul telles que des charges de travail conteneurisées et des machines virtuelles pour créer un jeu de données plus actionnable. Le prétraitement peut servir à : 

    - Agréger les données.
    - Modifiez les données, par exemple, pour supprimer des données personnelles.
    - Fractionnez les données pour optimiser le stockage et la bande passante, ainsi que pour les analyser de façon plus approfondie.
    - Analyser et réagir aux événements IoT. 

- **Transférer des données sur le réseau à Azure** : Utilisez Azure Stack Edge Pro avec GPU pour transférer rapidement et facilement des données vers Azure pour le calcul et l’analyse à des fins d’archivage. 

## <a name="key-capabilities"></a>Fonctionnalités clés

Azure Stack Edge Pro avec GPU intègre les fonctionnalités suivantes :

|Fonctionnalité |Description  |
|---------|---------|
|Inférence accélérée avec l’intelligence artificielle| Activé par le GPU intégré (un ou deux selon le modèle).|
|Edge Computing      |Prend en charge les charges de travail de machine virtuelle et conteneurisées pour permettre l’analyse, le traitement et le filtrage des données. |
|Accès aux données     | Accès direct aux données à partir d’objets blob Stockage Azure et Azure Files à l’aide des API cloud pour le traitement d’autres données dans le cloud. Un cache local sur l’appareil est utilisé pour accéder rapidement aux fichiers les plus récemment utilisés.|
|Géré par le cloud     |L’appareil et le service sont gérés via le portail Azure.  |
|Chargement hors connexion     | Un mode déconnecté prend en charge les scénarios de chargement hors connexion.|
|Protocoles de transfert de fichiers pris en charge      | Prise en charge des protocoles SMB, NFS et REST standard pour l’ingestion des données. <br> Pour plus d’informations sur les versions prises en charge, consultez [Conditions requises pour Azure Stack Edge Pro avec GPU](azure-stack-edge-system-requirements.md).|
|Actualisation des données     | Possibilité d’actualiser les fichiers locaux avec la dernière version issue du cloud.|
|Chiffrement    | Prise en charge de BitLocker pour chiffrer les données localement et transférer de façon sécurisée des données vers le cloud via *https*.|
|Limitation de bande passante| Limiter l’utilisation de la bande passante pendant les heures de pointe.|

<!--|ExpressRoute | Added security through ExpressRoute. Use peering configuration where traffic from local devices to the cloud storage endpoints travels over the ExpressRoute. For more information, see [ExpressRoute overview](../expressroute/expressroute-introduction.md).|-->

## <a name="components"></a>Components

La solution Azure Stack Edge Pro avec GPU se compose d’une ressource Azure Stack Edge, d’un appareil physique Azure Stack Edge Pro avec GPU et d’une interface utilisateur web locale.

* **Appareil physique Azure Stack Edge Pro avec GPU** : serveur 1U monté en rack, fourni par Microsoft qui peut être configuré pour envoyer des données à Azure.
    
* **Ressource Azure Stack Edge** : ressource du portail Azure, qui vous permet de gérer un appareil Azure Stack Edge Pro avec GPU à partir d’une interface web accessible depuis divers emplacements géographiques. Utilisez la ressource Azure Stack Edge pour créer et gérer des ressources, afficher et gérer les appareils et les alertes, et gérer les partages.  

    Pour plus d’informations, accédez à la section vous expliquant comment [créer une commande pour votre appareil Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Interface utilisateur web locale Azure Stack Edge Pro avec GPU** : interface utilisateur locale basée sur un navigateur sur votre appareil Azure Stack Edge Pro avec GPU, principalement conçue pour la configuration initiale de l’appareil. Utilisez également l’interface utilisateur web locale pour exécuter des diagnostics, arrêter et redémarrer l’appareil Azure Stack Edge Pro avec GPU, afficher les journaux d’activité de copie, et contactez le support Microsoft pour créer une demande de service.

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]
    
    Pour plus d’informations sur l’utilisation de l’interface utilisateur basée sur le web, consultez [Utiliser l’interface utilisateur basée sur le web pour gérer votre Azure Stack Edge Pro avec GPU](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilité des régions

L’appareil physique Azure Stack Edge Pro avec GPU, la ressource Azure et le compte de stockage cible vers lequel vous transférez des données n’ont pas besoin de se trouver dans la même région.

- **Disponibilité de la ressource** : pour cette version, la ressource est disponible dans les régions USA Est, Europe Ouest et Asie Sud-Est.

- **Disponibilité de l’appareil** : pour obtenir la liste de tous les pays/régions où l’appareil Azure Stack Edge Pro avec GPU est disponible, accédez à la section **Disponibilité** sous l’onglet **Azure Stack Edge Pro** des [tarifs d’Azure Stack Edge Pro avec GPU](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro).
    
- **Comptes de stockage de destination** : les comptes de stockage qui stockent les données sont disponibles dans toutes les régions Azure. Pour garantir des performances optimales, les régions où des comptes de stockage stockent les données Azure Stack Edge Pro avec GPU doivent se trouver à proximité de l’appareil. Un compte de stockage situé dans un emplacement éloigné entraîne des temps de latence longs et une baisse des performances.

Le service Azure Stack Edge est un service non régional. Pour plus d'informations, consultez [Régions et zones de disponibilité dans Azure](../availability-zones/az-overview.md). Le service Azure Stack Edge ne dépend pas d’une région Azure spécifique, ce qui le rend résilient aux pannes aux niveaux de la zone et de la région.

## <a name="next-steps"></a>Étapes suivantes

- Passer en revue la [configuration requise pour Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-system-requirements.md).

- Comprendre les [limites d’Azure Stack Edge Pro avec GPU](azure-stack-edge-limits.md).
- Déployer [Azure Stack Edge Pro avec GPU](azure-stack-edge-gpu-deploy-prep.md) dans le portail Azure.
