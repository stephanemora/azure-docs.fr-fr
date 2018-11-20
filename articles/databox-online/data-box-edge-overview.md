---
title: Présentation de Microsoft Azure Data Box Edge | Microsoft Docs
description: Décrit Azure Data Box Edge, une solution de stockage qui utilise un appareil physique pour le transfert vers Azure via le réseau.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/13/2018
ms.author: alkohli
ms.openlocfilehash: 60186ee6aa614c83691c9ce4ab9d8124fd5925fd
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685954"
---
# <a name="what-is-azure-data-box-edge-preview"></a>Qu’est-ce qu’Azure Data Box Edge (préversion) ? 

Azure Data Box Edge est une solution de stockage qui vous permet de traiter les données et de les envoyer vers Azure via le réseau. Cet article vous fournit une vue d’ensemble de la solution Data Box Edge, ses avantages, ses principales fonctionnalités et les scénarios dans lesquels vous pouvez déployer cet appareil. 

Data Box Edge utilise un appareil physique fourni par Microsoft pour accélérer le transfert sécurisé des données. L’appareil physique réside dans votre environnement local et vous écrivez des données à l’aide des protocoles NFS et SMB. 

Data Box Edge possède toutes les fonctionnalités de passerelle de Data Box Gateway. Par ailleurs, Data Box intègre des fonctionnalités informatiques avec intelligence artificielle, qui facilitent l’analyse, le traitement ou le filtrage des données en les déplaçant vers un objet blob de blocs Azure, un objet blob de pages ou Azure Files.  

> [!IMPORTANT]
> - Data Box Edge est en préversion. [Inscrivez-vous](#sign-up) à ce service !
> - Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution.


## <a name="use-cases"></a>Cas d'utilisation

Azure Data Box Edge est un appareil informatique avec intelligence artificielle, doté de fonctionnalités de transfert de données via le réseau. Voici les différents scénarios où Data Box Edge peut être utilisé pour le transfert de données.

- **Prétraiter les données** : analysez les données locales ou les appareils IoT pour accéder rapidement au résultat tout en restant proche du lieu où les données sont générées. Data Box Edge transfère le jeu complet de données vers le cloud pour effectuer des traitements plus avancés ou des analyses plus approfondies.  Le prétraitement peut servir à : 

    - Agréger les données.
    - Modifier les données, par exemple pour supprimer des informations d’identification personnelle (PII).
    - Créer des sous-ensembles et transférer les données nécessaires à une analyse plus approfondie dans le cloud.
    - Analyser et réagir aux événements IoT. 

- **Inference Azure Machine Learning** : Data Box Edge vous permet d’exécuter des modèles Machine Learning (ML) pour obtenir des résultats rapides qui peuvent être traitées avant l’envoi des données vers le cloud. Le jeu de données complet est transféré pour continuer à reformer et améliorer vos modèles ML.

- **Transférer des données vers Azure via le réseau** : utilisez Data Box Edge pour transférer rapidement et facilement des données vers Azure pour le calcul et l’analyse à des fins d’archivage. 

## <a name="benefits"></a>Avantages

Data Box Edge offre les avantages suivants :

- **Transfert de données facile** : transférez des données vers/depuis Stockage Azure aussi facilement qu’avec un partage réseau local.  
- **Hautes performances** : permet des transferts hautes performances vers et depuis Azure. 
- **Accès rapide** : met en cache les fichiers les plus récents pour un accès rapide aux fichiers locaux.  
- **Utilisation de la bande passante limitée** : les données peuvent être écrites dans Azure même lorsque le réseau est restreint afin de limiter l’utilisation pendant les heures de pointe.  
- **Transformer des données** : permet l’analyse, le traitement ou de filtrage des données lors de leur déplacement vers Azure.

## <a name="key-capabilities"></a>Fonctionnalités clés

Data Box Edge intègre les fonctionnalités suivantes :

|Fonctionnalité |Description  |
|---------|---------|
|Hautes performances     | Transfert de données et bande passante entièrement automatisés et hautement optimisés.|
|Protocoles pris en charge     | Prise en charge des protocoles SMB et NFS standard pour l’ingestion des données. <br> Pour plus d’informations sur les versions prises en charge, consultez [Conditions requises pour Data Box Edge](https://aka.ms/dbe-docs).|
|Informatique       |Permet l’analyse, le traitement et le filtrage des données.|
|Accès aux données     | Accès direct aux données à partir d’objets blob Stockage Azure et Azure Files à l’aide des API cloud pour le traitement d’autres données dans le cloud.|
|Accès rapide     | Cache local sur l’appareil pour un accès rapide aux derniers fichiers utilisés.|
|Chargement hors connexion     | Un mode déconnecté prend en charge les scénarios de chargement hors connexion.|
|Actualisation des données     | Possibilité d’actualiser les fichiers locaux avec la dernière version issue du cloud.|
|Chiffrement    | Prise en charge de BitLocker pour chiffrer les données localement et transférer de façon sécurisée des données vers le cloud via *https*.       |
|Résilience     | Résilience réseau intégrée.        |


## <a name="features-and-specifications"></a>Fonctionnalités et spécifications

L’appareil physique Data Box Edge affiche les caractéristiques suivantes :

| Fonctionnalités/spécifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Dimensions   | Largeur : 17,25” Profondeur : 27,25” Hauteur : 1,75”<br>(sans les kits de montage et les poignées de l’unité d’alimentation PSU)  |            
| Espace en rack|1U lorsque l’appareil est placé dans le rack|
| Câbles| 2 câbles d’alimentation<br>2 câbles RJ45 1 Gbit/s<br>2 câbles en cuivre SFP+ 10 Gbit/s|
| Composants|2 unités d’alimentation (PSU) intégrées|
| UC|2 processeurs Intel Xeon avec 10 cœurs chacun  |
| Mémoire| 64 Go de RAM|
| Disques| 8 disques SSD NVMe, d’une capacité de 1,6 To chacun <br> Le système échoue si un disque SSD NVMe est défaillant. |
| Capacité de stockage local| Capacité totale de 12,8 To|
| Interfaces réseau| 2 interfaces 1 GbE : une pour la gestion, non configurable par l’utilisateur, utilisée pour l’installation initiale. L’autre interface peut être configurée par l’utilisateur, utilisée pour le transfert de données et est définie en mode DHCP par défaut. <br>2 interfaces 25 GbE : peuvent également fonctionner comme interfaces 10 GbE. Ces interfaces de données peuvent être configurées par l’utilisateur en mode statique ou DHCP (par défaut). <br> 2 interfaces 25 GbE : ces interfaces de données peuvent être configurées par l’utilisateur en mode statique ou DHCP (par défaut).|

## <a name="components"></a>Composants

La solution Data Box Edge se compose d’une ressource Data Box Edge, d’un appareil physique Data Box Edge et d’une interface utilisateur web locale.

* **Appareil physique Data Box Edge** : un serveur 1U monté en rack, fourni par Microsoft qui peut être configuré pour envoyer des données vers Azure. 
    
* **Ressource Data Box Edge** : ressource du portail Azure, qui vous permet de gérer un appareil Data Box Edge à partir d’une interface web accessible depuis divers emplacements géographiques. Utilisez la ressource Data Box Edge pour créer et gérer des ressources, afficher et gérer les appareils et les alertes, et gérer les partages.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Pour plus d’informations, consultez [Utiliser le service Data Box Edge pour gérer votre appareil Data Box Edge](https://aka.ms/dbe-docs).

* **Interface utilisateur web locale Data Box** : utilisez l’interface utilisateur web locale pour exécuter des diagnostics, arrêter et redémarrer l’appareil Data Box Edge, afficher les journaux de copie, et contactez le support Microsoft pour créer une demande de service.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Pour plus d’informations sur l’utilisation de l’interface utilisateur basée sur le web, voir [Utiliser l’interface utilisateur basée sur le web pour gérer votre Data Box](https://aka.ms/dbe-docs).


## <a name="region-availability"></a>Disponibilité des régions

L’appareil physique Data Box, la ressource Azure et le compte de stockage cible vers lequel vous transférez des données n’ont pas besoin de se trouver dans la même région.

- **Disponibilité des ressources** : pour cette version, la ressource Data Box Edge est disponible dans les régions suivantes :
    - **United States** : Ouest des États-Unis 2 et Est des États-Unis
    - **Union européenne** : Europe de l’Ouest
    - **Asie-Pacifique** : Sud-est de l’Asie

- **Comptes de stockage de destination** : les comptes de stockage qui stockent les données sont disponibles dans toutes les régions Azure. 

    Régions où les comptes de stockage qui stockent les données Data Box doivent se trouver à proximité de l’appareil pour garantir des performances optimales. Un compte de stockage situé dans un emplacement éloigné entraîne des temps de latence longs et une baisse des performances. 


## <a name="sign-up"></a>Inscription

Data Box Edge est en préversion et vous devez vous inscrire. Procédez comme suit pour vous inscrire à Data Box Gateway :

1. Connectez-vous au portail Azure à l’adresse : [https://aka.ms/databox-edge](https://aka.ms/databox-edge).

2. Sélectionnez l’abonnement que vous souhaitez utiliser pour la préversion de Data Box Edge. Sélectionnez la région dans laquelle vous souhaitez déployer la ressource Data Box Edge. Dans l’option Data Box Edge, cliquez sur **S’inscrire**.

    ![Inscription à Data Box Edge 3](media/data-box-edge-overview/data-box-edge-sign-up3.png)

3.  Répondez aux questions concernant le pays de résidence des données, le laps de temps, le service Azure cible pour le transfert des données, la bande passante réseau et la fréquence de transfert des données. Consultez le texte **Confidentialité et conditions d'utilisation** et sélectionnez la case à cocher autorisant **Microsoft à utiliser votre adresse de messagerie pour vous contacter**.

    ![Inscription à Data Box Edge 4](media/data-box-edge-overview/data-box-edge-sign-up4.png)

4. Une fois que vous êtes inscrit et autorisé à utiliser la préversion, vous pouvez commander un Data Box Edge.

## <a name="next-steps"></a>Étapes suivantes

- Relire les [Conditions requises pour le système Data Box Edge](https://aka.ms/dbe-docs).
- Comprendre les [limites de Data Box Edge](https://aka.ms/dbe-docs).
- Déployer [Azure Data Box Edge](https://aka.ms/dbe-docs) sur le portail Azure.




