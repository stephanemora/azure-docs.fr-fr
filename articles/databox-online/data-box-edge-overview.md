---
title: Présentation de Microsoft Azure Data Box Edge | Microsoft Docs
description: Décrit Azure Data Box Edge, une solution de stockage qui utilise un appareil physique pour le transfert vers Azure via le réseau.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305435"
---
# <a name="what-is-azure-data-box-edge"></a>Qu’est-ce qu’Azure Data Box Edge ? 

Azure Data Box Edge est un appareil informatique avec intelligence artificielle, doté de fonctionnalités de transfert de données via le réseau. Cet article vous fournit une vue d’ensemble de la solution Data Box Edge, ses avantages, ses principales fonctionnalités et les scénarios dans lesquels vous pouvez déployer cet appareil. 

Data Box Edge est une solution matérielle en tant que service. Microsoft fournit un appareil géré dans le cloud avec un FPGA (Field Programmable Gate Array) intégré, qui permet une inférence accélérée de l’IA et possède toutes les fonctionnalités d’une passerelle de stockage. 

## <a name="use-cases"></a>Cas d'utilisation

Voici différents scénarios d’utilisation de Data Box Edge pour l’inférence rapide de Machine Learning (ML) à la périphérie et le prétraitement des données avant leur envoi à Azure.

- **Inference avec Azure Machine Learning** : Data Box Edge vous permet d’exécuter des modèles Machine Learning (ML) pour obtenir des résultats rapides qui peuvent être traités avant l’envoi des données vers le cloud. Le jeu de données complet peut également être transféré pour continuer à reformer et améliorer vos modèles ML. Pour plus d’informations sur l’utilisation des modèles accélérés matériellement d’Azure ML sur un appareil Data Box Edge, consultez [Déployer des modèles accélérés matériellement Azure ML sur Data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Prétraiter les données** : transformez les données avant de les envoyer à Azure pour créer un jeu de données plus actionnable. Le prétraitement peut servir à : 

    - Agréger les données.
    - Modifiez les données, par exemple, pour supprimer des données personnelles.
    - Fractionnez les données pour optimiser le stockage et la bande passante, ainsi que pour les analyser de façon plus approfondie.
    - Analyser et réagir aux événements IoT. 

- **Transférer des données vers Azure via le réseau** : utilisez Data Box Edge pour transférer rapidement et facilement des données vers Azure pour le calcul et l’analyse à des fins d’archivage. 


## <a name="key-capabilities"></a>Fonctionnalités clés

Data Box Edge intègre les fonctionnalités suivantes :

|Fonctionnalité |Description  |
|---------|---------|
|Inférence accélérée avec l’intelligence artificielle| Activée par le FPGA intégré.|
|Informatique       |Permet l’analyse, le traitement et le filtrage des données.|
|Hautes performances | Transferts de données et calcul haute performance.|
|Accès aux données     | Accès direct aux données à partir d’objets blob Stockage Azure et Azure Files à l’aide des API cloud pour le traitement d’autres données dans le cloud. Un cache local sur l’appareil est utilisé pour accéder rapidement aux fichiers les plus récemment utilisés.|
|Géré par le cloud     |L’appareil et le service sont gérés via le portail Azure.  |
|Chargement hors connexion     | Un mode déconnecté prend en charge les scénarios de chargement hors connexion.|
|Protocoles pris en charge     | Prise en charge des protocoles SMB et NFS standard pour l’ingestion des données. <br> Pour plus d’informations sur les versions prises en charge, consultez [Conditions requises pour Data Box Edge](data-box-edge-system-requirements.md).|
|Actualisation des données     | Possibilité d’actualiser les fichiers locaux avec la dernière version issue du cloud.|
|Chiffrement    | Prise en charge de BitLocker pour chiffrer les données localement et transférer de façon sécurisée des données vers le cloud via *https*.|
|Limitation de bande passante| Limiter l’utilisation de la bande passante pendant les heures de pointe.|


## <a name="components"></a>Composants

La solution Data Box Edge se compose d’une ressource Data Box Edge, d’un appareil physique Data Box Edge et d’une interface utilisateur web locale.

* **Appareil physique Data Box Edge** : un serveur 1U monté en rack, fourni par Microsoft qui peut être configuré pour envoyer des données vers Azure. 
    
* **Ressource Data Box Edge** : ressource du portail Azure, qui vous permet de gérer un appareil Data Box Edge à partir d’une interface web accessible depuis divers emplacements géographiques. Utilisez la ressource Data Box Edge pour créer et gérer des ressources, afficher et gérer les appareils et les alertes, et gérer les partages.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Pour plus d’informations, accédez à la section vous expliquant comment [Créer une commande pour votre appareil Data Box Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Interface utilisateur web locale Data Box** : utilisez l’interface utilisateur web locale pour exécuter des diagnostics, arrêter et redémarrer l’appareil Data Box Edge, afficher les journaux d’activité de copie, et contactez le support Microsoft pour créer une demande de service.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Pour plus d’informations sur l’utilisation de l’interface utilisateur basée sur le web, voir [Utiliser l’interface utilisateur basée sur le web pour gérer votre Data Box](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Disponibilité des régions

L’appareil physique Data Box, la ressource Azure et le compte de stockage cible vers lequel vous transférez des données n’ont pas besoin de se trouver dans la même région.

- **Disponibilité des ressources** : pour obtenir la liste de toutes les régions où la ressource Data Box Edge est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Le déploiement de Data Box Edge est également possible dans Azure Government Cloud. Pour plus d’informations, consultez [Présentation d’Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Comptes de stockage de destination** : les comptes de stockage qui stockent les données sont disponibles dans toutes les régions Azure. Pour garantir des performances optimales, les régions où des comptes de stockage stockent les données Data Box Edge doivent se trouver à proximité de l’appareil. Un compte de stockage situé dans un emplacement éloigné entraîne des temps de latence longs et une baisse des performances. 


## <a name="next-steps"></a>Étapes suivantes

- Relire les [Conditions requises pour le système Data Box Edge](data-box-edge-system-requirements.md).
- Comprendre les [limites de Data Box Edge](data-box-edge-limits.md).
- Déployer [Azure Data Box Edge](data-box-edge-deploy-prep.md) sur le portail Azure.




