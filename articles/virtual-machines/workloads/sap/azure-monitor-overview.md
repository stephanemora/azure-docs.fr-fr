---
title: Présentation et architecture d’Azure Monitor pour solutions SAP | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées sur Azure Monitor pour solutions SAP
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 4b5bcb4aecab0247f47aede883e1bfe771262026
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532574"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor pour solutions SAP (préversion)

## <a name="overview"></a>Vue d'ensemble

Cet article donne une vue d’ensemble du fonctionnement d’Azure Monitor pour la Solution SAP et ses fonctionnalités.

Azure Monitor pour les solutions SAP est un produit de surveillance Azure natif pour toute personne exécutant ses paysages SAP sur Azure. Le produit fonctionne avec [SAP sur des machines virtuelles Azure](./hana-get-started.md) et [SAP sur des instances volumineuses Azure](./hana-overview-architecture.md).

Azure Monitor pour SAP Solutions vous permet de collecter des données de télémétrie à partir de l’infrastructure Azure et des bases de données dans un emplacement central et d’établir une corrélation visuelle entre les données de télémétrie afin d’accélérer la résolution des problèmes.

Les solutions Azure Monitor pour SAP sont proposées via la place de marché Azure. Elles offrent une expérience d’installation simple et intuitive et il suffit de quelques clics pour déployer la ressource pour Azure Monitor pour les solutions SAP (appelée **Ressources d’analyse SAP**).

Vous pouvez superviser différents composants d’un paysage SAP, tels que les machines virtuelles Azure, le cluster à haute disponibilité, la base de données SAP HANA, SAP NetWeaver et ainsi de suite, en ajoutant le **fournisseur** de ce composant.

Infrastructure prise en charge :

- Machine virtuelle Azure
- Instance volumineuse Azure

Bases de données prises en charge :
- Base de données SAP HANA
- Microsoft SQL Server

Azure Monitor pour SAP Solutions utilise la puissance des capacités actuelles d’[Azure Monitor](../../../azure-monitor/overview.md), telles qu’Analytique des journaux d’activité et [Classeurs](../../../azure-monitor/visualize/workbooks-overview.md), pour fournir des capacités de surveillance supplémentaires. Vous pouvez créer des [visualisations personnalisées](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) en modifiant les classeurs par défaut fournis par Azure Monitor pour SAP Solutions. Écrivez [requêtes personnalisées](../../../azure-monitor/logs/log-analytics-tutorial.md) et créez des [alertes personnalisées](../../../azure-monitor/alerts/alerts-log.md) en utilisant l’espace de travail Azure Log Analytics. Tirez parti de la [période de rétention flexible](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) et connectez des données de surveillance à votre système de création des tickets.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Quelles sont les données collectées par Azure Monitor pour SAP Solutions ?

La collecte de données dans Azure Monitor pour SAP Solutions varie en fonction des fournisseurs que vous configurez. La préversion publique collecte les données suivantes.

Télémétrie de cluster Pacemaker haute disponibilité :
- État du nœud, de la ressource et de l’appareil de traitement par blocs STONITH
- Contraintes d’emplacement de Pacemaker
- État des voix et des anneaux du quorum
- [Autres](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Télémétrie SAP HANA :
- Processeur, mémoire, disque et utilisation réseau
- Réplication du système HANA (HSR)
- Sauvegarde HANA
- État de l’hôte HANA
- Rôles de serveur d’index et de serveur de noms

Télémétrie du serveur Microsoft SQL :
- Processeur, mémoire, utilisation du disque
- Nom d’hôte, nom de l’instance SQL, ID du système SAP
- Requêtes de lots, compilations et espérance de vie d’une page dans le temps
- Les 10 instructions SQL les plus onéreuses dans le temps
- Les 12 plus grandes tables dans le système SAP
- Problèmes enregistrés dans le journal des erreurs SQL Server
- Processus de blocage et statistiques d’attente SQL dans le temps

Télémétrie du système d’exploitation (Linux) 
- Utilisation de l’UC, nombre de fourches, processus en cours d’exécution et bloqués 
- Utilisation de la mémoire et distribution entre utilisée, mise en cache et mise en mémoire tampon 
- Permuter l’utilisation, la pagination et le taux d’échange 
- Utilisation des systèmes de fichiers, nombre d’octets lus et écrits par périphérique de traitement par blocs 
- Latence de lecture/écriture par périphérique de traitement par blocs 
- Nombre d’E/S en cours, octets de lecture/écriture PMEM 
- Paquets réseau en entrée/sortie, octets réseau en entrée/sortie 

Télémétrie SAP NetWeaver :

- Disponibilité du serveur d’applications et du système SAP, notamment la disponibilité du processus d’instance pour Dispatcher, ICM, Gateway, Message Server, Enqueue Server, IGS Watchdog
- Tendances et statistiques d’utilisation des processus de travail
- Tendances et statistiques des verrous de mise en file d’attente
- Tendances et statistiques d’utilisation des files d’attente

## <a name="data-sharing-with-microsoft"></a>Partage de données avec Microsoft

Azure Monitor pour solutions SAP collecte les métadonnées système pour offrir une meilleure prise en charge de SAP. Aucune information d’identification personnelle (PII)/information d’identification de l’utilisateur final (EUII) n’est collectée.

Vous pouvez activer le partage de données avec Microsoft lorsque vous créez la ressource Azure Monitor pour solutions SAP en choisissant *Partager* dans la liste déroulante. Nous vous recommandons d’activer le partage de données. Le partage de données offre aux équipes de support et d’ingénierie de Microsoft des informations sur votre environnement, ce qui nous permet d’offrir une meilleure prise en charge pour votre solution SAP sur Azure stratégique.

## <a name="architecture-overview"></a>Vue d’ensemble de l’architecture

Le diagramme suivant montre, à un niveau élevé, comment Azure Monitor pour SAP Solutions collecte les données de télémétrie à partir de la base de données SAP HANA. L’architecture est identique, que SAP HANA soit déployée sur des machines virtuelles Azure ou sur des instances volumineuses Azure.

![Architecture d’Azure Monitor pour solutions SAP](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Les composants clés de l’architecture sont les suivants :
- Portail Azure : votre point de départ. Vous pouvez accéder à la place de marché au sein du portail Azure et découvrir Azure Monitor pour solutions SAP.
- Azure Monitor pour SAP Solutions : un emplacement d’arrivée vous permettant d’examiner la télémétrie de supervision.
- Groupe de ressources managées : déployé automatiquement dans le cadre d’Azure Monitor pour le déploiement de ressources pour solutions SAP. Les ressources déployées dans le groupe de ressources managées aident à collecter des données de télémétrie. Les ressources clés déployées et leurs objectifs sont les suivantes :
   - Machine virtuelle Azure : également appelée *machine virtuelle de collecteur*, il s’agit d’une machine virtuelle Standard_B2ms. L’objectif principal de cette machine virtuelle est d’héberger la *charge utile de surveillance*. La charge utile de surveillance fait référence à la logique de la télémétrie de collecte des données à partir des systèmes sources et du transfert des données vers l’infrastructure de surveillance. Dans le diagramme ci-dessus, la charge utile de surveillance contient la logique permettant la connexion à la base de données SAP HANA via le port SQL.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md) : cette ressource est déployée pour conserver en toute sécurité les informations d’identification de la base de données SAP HANA et pour stocker des informations sur les [fournisseurs](./azure-monitor-providers.md).
   - Espace de travail Log Analytics : destination où sont stockées les données de télémétrie.
      - La visualisation est basée sur la télémétrie dans Log Analytics à l’aide de [classeurs Azure](../../../azure-monitor/visualize/workbooks-overview.md). Vous pouvez personnaliser la visualisation. Vous pouvez également épingler vos classeurs ou une visualisation spécifique au sein des classeurs au tableau de bord Azure afin de permettre une actualisation automatique. La fréquence maximale d’actualisation est de 30 minutes.
      - Vous pouvez utiliser votre espace de travail existant dans le même abonnement que la ressource de surveillance SAP en choisissant cette option au moment du déploiement.
      - Vous pouvez utiliser le langage de requête Kusto (KQL) pour exécuter des [requêtes](../../../azure-monitor/logs/log-query-overview.md) par rapport aux tables brutes dans l’espace de travail Log Analytics. Examinons les *journaux d’activité personnalisés*.

> [!Note]
> Vous êtes responsable de la mise à jour corrective et de la maintenance de la machine virtuelle déployée dans le groupe de ressources managées.

> [!Tip]
> Vous pouvez choisir d’utiliser un espace de travail Log Analytics existant pour la collecte de données de télémétrie s’ils sont déployés dans le même abonnement Azure que la ressource pour Azure Monitor pour solutions SAP.

### <a name="architecture-highlights"></a>Points forts de l’architecture

Voici les principales caractéristiques de l’architecture :
 - **Multi-instance** : vous pouvez créer des analyses pour plusieurs instances d’un type de composant donné (par exemple, base de données HANA, cluster à haute disponibilité, Microsoft SQL Server, SAP NetWeaver) sur plusieurs SID SAP au sein d’un réseau virtuel avec une seule ressource d’Azure Monitor pour SAP Solutions.
 - **Multifournisseur** : le diagramme d’architecture ci-dessus montre le fournisseur SAP HANA à titre d’exemple. De même, vous pouvez configurer des fournisseurs supplémentaires pour les composants correspondants (par exemple, base de données HANA, cluster HA, Microsoft SQL Server, SAP NetWeaver) afin de collecter des données à partir de ces composants.
 - **Open source** : le code source d’Azure Monitor pour solutions SAP est disponible dans [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Vous pouvez vous référer au code du fournisseur et en apprendre plus sur le produit, contribuer ou partager des commentaires.
 - **Requêtes de framework extensibles** : les requêtes SQL pour la collecte des données de télémétrie sont écrites en [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Des requêtes SQL supplémentaires pour collecter plus de données de télémétrie peuvent facilement être ajoutées. Vous pouvez demander l’ajout de données de télémétrie spécifiques à Azure Monitor pour SAP Solutions en laissant un commentaire en utilisant le lien fourni à la fin de cet article. Vous pouvez également laisser un commentaire en contactant votre équipe de compte.

## <a name="pricing"></a>Tarifs
Azure Monitor pour solutions SAP est un produit gratuit (pas de frais de licence). Vous êtes responsable du paiement du coût des composants sous-jacents dans le groupe de ressources managé et des coûts de consommation associés à l’ingestion de données et à la rétention des données. Consultez les documents sur la tarification Azure standard pour plus d’informations [Tarification des machines virtuelles Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), [Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/), [Tarification du compte de stockage Azure](https://azure.microsoft.com/pricing/details/storage/queues/), [Azure Log Analytics et tarification des alertes](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fournisseurs Azure Monitor pour SAP Solutions.

> [!div class="nextstepaction"]
> [Fournisseurs Azure Monitor pour SAP Solutions](azure-monitor-providers.md)
