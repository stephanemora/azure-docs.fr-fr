---
title: Fournisseurs Azure Monitor pour solutions SAP | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées sur Azure Monitor pour fournisseurs de solutions SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 5061ee2131ee708c38a68dca02c18a21085b5430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616856"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Fournisseurs Azure Monitor pour SAP Solutions (préversion)

## <a name="overview"></a>Vue d'ensemble

Cet article décrit les différents fournisseurs actuellement disponibles pour Azure Monitor pour SAP Solutions.

Dans le contexte de Azure Monitor pour solutions SAP, un *type fournisseur*  fait référence à un *fournisseur* spécifique. Par exemple *SAP HANA*, qui est configuré pour un composant spécifique au sein du paysage SAP, comme la base de données SAP HANA. Un fournisseur contient les informations de connexion pour le composant correspondant et permet de collecter des données de télémétrie à partir de ce composant. Une ressource Azure Monitor pour solutions SAP (également appelée ressource du moniteur SAP) peut être configurée avec plusieurs fournisseurs du même type fournisseur ou plusieurs fournisseurs de plusieurs types fournisseur.
   
Vous pouvez choisir de configurer différents types fournisseur pour activer la collecte des données à partir du composant correspondant dans leur paysage SAP. Par exemple, vous pouvez configurer un fournisseur pour le type fournisseur SAP HANA, un autre fournisseur pour le type fournisseur de cluster haute disponibilité, etc.  

Vous pouvez également choisir de configurer plusieurs fournisseurs d’un type fournisseur spécifique pour réutiliser la même ressource du moniteur SAP et le groupe géré associé. Pour plus d’informations sur les groupes de ressources managés, consultez [Gérer des groupes de ressources Azure Resource Manager au moyen du Portail Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md).

Pour la préversion publique, les types fournisseur suivants sont pris en charge :   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Cluster haute disponibilité
- Système d’exploitation (OS)

![Fournisseurs Azure Monitor pour solutions SAP](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

Nous vous recommandons de configurer au moins un fournisseur à partir des types fournisseur disponibles lors du déploiement de la ressource du moniteur SAP. En configurant un fournisseur, vous lancez la collecte de données à partir du composant correspondant pour lequel le fournisseur est configuré.   

Si vous ne configurez aucun fournisseur au moment du déploiement de la ressource du moniteur SAP, bien que la ressource du moniteur SAP sera déployée avec succès, aucune donnée de télémétrie ne sera collectée. Vous pouvez ajouter des fournisseurs après le déploiement par le biais de la ressource de moniteur SAP au sein du Portail Azure. Vous pouvez ajouter ou supprimer des fournisseurs de la ressource du moniteur SAP à tout moment.

## <a name="provider-type-sap-netweaver"></a>Type de fournisseur : SAP NetWeaver

Vous pouvez configurer un ou plusieurs fournisseurs de type fournisseur SAP NetWeaver pour activer la collecte des données à partir de la couche SAP NetWeaver. Le fournisseur AMS NetWeaver utilise l’interface de [service web SAPControl](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) existante pour récupérer les informations de télémétrie appropriées.

Pour la version actuelle, les méthodes web SOAP suivantes sont les méthodes prêtes à l’emploi standard appelées par AMS.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

En préversion publique, vous pouvez vous attendre à voir les données suivantes avec le fournisseur SAP NetWeaver : 
- Disponibilité du système et des instances
- Utilisation du processus de travail
- Utilisation de la file d’attente
- Statistiques de verrous empilés

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Type de fournisseur : SAP HANA

Vous pouvez configurer un ou plusieurs fournisseurs de type fournisseur *SAP HANA* pour activer la collecte des données à partir de la base de données SAP HANA. Le fournisseur SAP HANA se connecte à la base de données SAP HANA sur le port SQL, extrait les données de télémétrie de la base de données et les envoie (push) à l’espace de travail Log Analytics de votre abonnement. Le fournisseur SAP HANA collecte des données toutes les minutes depuis la base de données SAP HANA.  

En préversion publique, vous pouvez vous attendre à voir les données suivantes avec le fournisseur SAP HANA :
- Utilisation de l’infrastructure sous-jacente
- État de l’hôte SAP HANA
- Réplication de système SAP HANA
- Des données de télémétrie de sauvegarde SAP HANA. 

La configuration du fournisseur SAP HANA requiert les éléments suivants :
- l’adresse IP de l’hôte ; 
- le numéro de port HANA SQL ;
- le nom d’utilisateur et le mot de passe SYSTEMDB.

Nous vous recommandons de configurer le fournisseur SAP HANA par rapport à SYSTEMDB. Toutefois, d’autres fournisseurs peuvent être configurés pour d’autres locataires de base de données.

![Fournisseurs Azure Monitor pour solutions SAP : SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Type de fournisseur : Microsoft SQL Server

Vous pouvez configurer un ou plusieurs fournisseurs de type fournisseur *Microsoft SQL Server* pour activer la collecte des données à partir de [SQL Server sur les machines virtuelles](https://azure.microsoft.com/services/virtual-machines/sql-server/). Le fournisseur SQL Server se connecte à Microsoft SQL Server sur le port SQL. Il extrait ensuite les données de télémétrie de la base de données et les transmet à l’espace de travail Log Analytics de votre abonnement. Configurez SQL Server pour l’authentification SQL et la connexion avec le nom d’utilisateur et le mot de passe SQL Server. Définissez la base de données SAP comme base de données par défaut pour le fournisseur. Le fournisseur SQL Server collecte des données de toutes les 60 secondes à toutes les heures, à partir de SQL Server.  

En préversion publique, vous pouvez vous attendre à voir les données suivantes avec le fournisseur SQL Server :
- Utilisation de l’infrastructure sous-jacente
- Instructions Top SQL
- Table la plus grande supérieure
- Problèmes enregistrés dans le journal des erreurs SQL Server
- Processus bloquant et autres  

La configuration du fournisseur Microsoft SQL Server requiert :
- l’ID du système SAP ;
- l’adresse IP de l’hôte ;
- le numéro de port SQL Server ;
- le nom d’utilisateur et le mot de passe SQL Server.

![Fournisseurs Azure Monitor pour solutions SAP : SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Type de fournisseur : cluster de haute disponibilité
Vous pouvez configurer un ou plusieurs fournisseurs de type fournisseur de *cluster haute disponibilité* pour activer la collecte de données à partir du cluster Pacemaker dans le paysage SAP. Le fournisseur de cluster haute disponibilité se connecte à Pacemaker au moyen du [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) pour les clusters **SUSE** et en utilisant [Performance co-pilot](https://access.redhat.com/articles/6139852) pour les clusters **RHEL**. AMS extrait ensuite les données de télémétrie de la base de données et les transmet à l’espace de travail Log Analytics de votre abonnement. Le fournisseur de cluster haute disponibilité collecte les données toutes les 60 secondes depuis Pacemaker.  

En préversion publique, vous pouvez vous attendre à voir les données suivantes avec le fournisseur de cluster haute disponibilité :   
 - État du cluster représenté sous la forme d’un regroupement du nœud et de l’état de la ressource 
 - Contraintes d’emplacement
 - Trends
 - [autres](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Fournisseurs Azure Monitor pour solutions SAP : cluster haute disponibilité](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Pour configurer un fournisseur de cluster haute disponibilité, deux étapes principales sont nécessaires :

1. Installer [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) dans *chaque* nœud dans le cluster Pacemaker.

   Vous avez deux options pour installer ha_cluster_exporter :
   
   - Utilisez des scripts Azure Automation pour déployer un cluster haute disponibilité. Les scripts installent [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) sur chaque nœud de cluster.  
   - Effectuez une [installation manuelle](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Configurez un fournisseur de cluster haute disponibilité dans *chaque* nœud dans le cluster Pacemaker.

   Pour configurer le fournisseur de cluster haute disponibilité, vous devez fournir les informations suivantes :
   
   - **Nom**. Nom de ce fournisseur. Il doit être unique pour cette instance de solutions Azure Monitor pour SAP.
   - **Point de terminaison Prometheus**. `http://<servername or ip address>:9664/metrics`.
   - **SID**. Pour les systèmes SAP, utilisez le SID SAP. Pour les autres systèmes (par exemple, les clusters NFS), utilisez un nom à trois caractères pour le cluster. Le SID doit être différent des autres clusters qui sont surveillés.   
   - **Nom du cluster**. Nom du rôle utilisé lors de la création du cluster. Le nom du cluster se trouve dans la propriété du cluster `cluster-name`.
   - **Hostname**. Nom d’hôte Linux de la machine virtuelle.  

## <a name="provider-type-os-linux"></a>Type de fournisseur : système d’exploitation (Linux)
Vous pouvez configurer un ou plusieurs fournisseurs de système d’exploitation de type fournisseur (Linux) pour activer la collecte de données à partir d’un nœud BareMetal ou VM. Le fournisseur de système d’exploitation (Linux) se connecte aux nœuds BareMetal ou VM au moyen du point de terminaison [Node_Exporter](https://github.com/prometheus/node_exporter) . Il extrait ensuite les données de télémétrie des nœuds et les transmet à l’espace de travail Log Analytics de votre abonnement. Le fournisseur de système d’exploitation (Linux) collecte les données toutes les 60 secondes pour la plupart des métriques à partir des nœuds. 

En préversion publique, vous pouvez vous attendre à voir les données suivantes avec le fournisseur de système d’exploitation (Linux) : 
   - Utilisation du processeur, utilisation du processeur par processus 
   - Utilisation du disque, lecture et écriture E/S 
   - Distribution de la mémoire, utilisation de la mémoire, utilisation de la mémoire d’échange 
   - Utilisation du réseau, informations sur le trafic réseau entrant et sortant 

La configuration d’un fournisseur de système d’exploitation (Linux) passe par deux étapes principales :
1. Installez  [Node_Exporter](https://github.com/prometheus/node_exporter)  sur chaque nœud BareMetal ou VM.
   Il existe deux options pour l’installation de [Node_Exporter](https://github.com/prometheus/node_exporter) : 
      - Pour une installation automatisée avec Ansible, utilisez [Node_Exporter](https://github.com/prometheus/node_exporter) sur chaque nœud BareMetal ou VM pour installer le fournisseur de système d’exploitation (Linux).  
      - Effectuez une [installation manuelle](https://prometheus.io/docs/guides/node-exporter/).

2. Configurez un fournisseur de système d’exploitation (Linux) pour chaque nœud BareMetal ou VM dans votre environnement. 
   Les informations suivantes sont nécessaires pour configurer le fournisseur de système d’exploitation (Linux) : 
      - Nom. Nom de ce fournisseur. Il doit être unique pour cette instance Azure Monitor pour SAP Solutions. 
      - Point de terminaison de l’exportateur de nœuds. Habituellement `http://<servername or ip address>:9100/metrics`. 

> [!NOTE]
> 9100 est un port exposé au point de terminaison Node_Exporter.

> [!Warning]
> Vérifiez que Node_Exporter continue à s’exécuter après le redémarrage du nœud. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment déployer Azure Monitor pour SAP Solutions à partir du Portail Azure.

> [!div class="nextstepaction"]
> [Déploiement d’Azure Monitor pour SAP Solutions avec le Portail Azure](./azure-monitor-sap-quickstart.md)
