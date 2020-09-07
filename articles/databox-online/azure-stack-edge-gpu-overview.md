---
title: Présentation de Microsoft Azure Stack Edge avec GPU | Microsoft Docs
description: Décrit Azure Stack Edge avec GPU, solution de stockage qui utilise un appareil physique pour le transfert vers Azure via le réseau.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: a8e1c83573de53962b3646304389023d91ab6dd3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256240"
---
# <a name="what-is-azure-stack-edge-with-gpu-preview"></a>Qu’est-ce qu’Azure Stack Edge avec GPU (préversion) ?

Azure Stack Edge avec GPU est un appareil de computing en périphérie qui prend en charge l’IA et qui présente des fonctionnalités de transfert de données via le réseau. Cet article vous fournit une vue d’ensemble de la solution Azure Stack Edge, ses avantages, ses principales fonctionnalités et les scénarios dans lesquels vous pouvez déployer cet appareil.

Azure Stack Edge avec GPU est une solution de matériel en tant que service. Microsoft fournit un appareil managé dans le cloud qui fait office de passerelle de stockage réseau et intègre un processeur graphique (GPU) permettant une inférence accélérée de l’IA. 

> [!IMPORTANT]
> Azure Stack Edge avec GPU est actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Cas d'utilisation

Voici différents scénarios d’utilisation d’Azure Stack Edge pour l’inférence rapide de Machine Learning (ML) à la périphérie et le prétraitement des données avant leur envoi à Azure.

- **Inférence avec Azure Machine Learning** : Azure Stack Edge vous permet d’exécuter des modèles Machine Learning (ML) pour obtenir des résultats rapides qui peuvent être traités avant l’envoi des données vers le cloud. Le jeu de données complet peut également être transféré pour continuer à reformer et améliorer vos modèles ML. Pour plus d’informations sur l’utilisation des modèles accélérés matériellement d’Azure ML sur un appareil Azure Stack Edge, consultez [Déployer des modèles accélérés matériellement Azure ML sur Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Prétraiter les données** : transformez les données avant de les envoyer à Azure pour créer un jeu de données plus actionnable. Le prétraitement peut servir à : 

    - Agréger les données.
    - Modifiez les données, par exemple, pour supprimer des données personnelles.
    - Fractionnez les données pour optimiser le stockage et la bande passante, ainsi que pour les analyser de façon plus approfondie.
    - Analyser et réagir aux événements IoT. 

- **Transférer des données vers Azure via le réseau** : utilisez Azure Stack Edge pour transférer rapidement et facilement des données vers Azure pour le calcul et l’analyse à des fins d’archivage. 

## <a name="key-capabilities"></a>Fonctionnalités clés

Azure Stack Edge intègre les fonctionnalités suivantes :

|Fonctionnalité |Description  |
|---------|---------|
|Inférence accélérée avec l’intelligence artificielle| Activé par le GPU intégré (un ou deux selon le modèle).|
|Edge Computing      |Permet l’analyse, le traitement et le filtrage des données. Prend en charge les machines virtuelles et les clusters Kubernetes.|
|Hautes performances | Transferts de données et calcul haute performance.|
|Accès aux données     | Accès direct aux données à partir d’objets blob Stockage Azure et Azure Files à l’aide des API cloud pour le traitement d’autres données dans le cloud. Un cache local sur l’appareil est utilisé pour accéder rapidement aux fichiers les plus récemment utilisés.|
|Géré par le cloud     |L’appareil et le service sont gérés via le portail Azure.  |
|Chargement hors connexion     | Un mode déconnecté prend en charge les scénarios de chargement hors connexion.|
|Protocoles pris en charge     | Prise en charge des protocoles SMB, NFS et REST standard pour l’ingestion des données. <br> Pour plus d’informations sur les versions prises en charge, consultez [Conditions requises pour Azure Stack Edge](azure-stack-edge-system-requirements.md).|
|Actualisation des données     | Possibilité d’actualiser les fichiers locaux avec la dernière version issue du cloud.|
|Chiffrement    | Prise en charge de BitLocker pour chiffrer les données localement et transférer de façon sécurisée des données vers le cloud via *https*.|
|Limitation de bande passante| Limiter l’utilisation de la bande passante pendant les heures de pointe.|
|ExpressRoute | Sécurité accrue avec ExpressRoute. Utilisez la configuration de peering avec laquelle le trafic des appareils locaux vers les points de terminaison de stockage cloud transite par ExpressRoute. Pour plus d’informations, voir [Appairage ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Components

La solution Azure Stack Edge se compose d’une ressource Azure Stack Edge, d’un appareil physique Azure Stack Edge et d’une interface utilisateur web locale.

* **Appareil physique Azure Stack Edge** : un serveur 1U monté en rack, fourni par Microsoft qui peut être configuré pour envoyer des données vers Azure.
    
* **Ressource Azure Stack Edge** : ressource du portail Azure, qui vous permet de gérer un appareil Azure Stack Edge à partir d’une interface web accessible depuis divers emplacements géographiques. Utilisez la ressource Azure Stack Edge pour créer et gérer des ressources, afficher et gérer les appareils et les alertes, et gérer les partages.  

    Pour plus d’informations, accédez à la section vous expliquant comment [Créer une commande pour votre appareil Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Interface utilisateur web locale Azure Stack Edge** : utilisez l’interface utilisateur web locale pour exécuter des diagnostics, arrêter et redémarrer l’appareil Azure Stack Edge, afficher les journaux d’activité de copie, et contactez le support Microsoft pour créer une demande de service.

    Pour plus d’informations sur l’utilisation de l’interface utilisateur basée sur le web, voir [Utiliser l’interface utilisateur basée sur le web pour gérer votre Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilité des régions

L’appareil physique Azure Stack Edge, la ressource Azure et le compte de stockage cible vers lequel vous transférez des données n’ont pas besoin de se trouver dans la même région.

- **Disponibilité de la ressource** : pour cette préversion, la ressource est disponible dans les régions USA Est, Europe Ouest et Asie Sud-Est.
    
- **Comptes de stockage de destination** : les comptes de stockage qui stockent les données sont disponibles dans toutes les régions Azure. Pour garantir des performances optimales, les régions où des comptes de stockage stockent les données Azure Stack Edge doivent se trouver à proximité de l’appareil. Un compte de stockage situé dans un emplacement éloigné entraîne des temps de latence longs et une baisse des performances.

## <a name="next-steps"></a>Étapes suivantes

- Relire les [Conditions requises pour le système Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).
- Comprenez les [limites de Azure Stack Edge](azure-stack-edge-limits.md).
- Déployez [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) sur le portail Azure.
