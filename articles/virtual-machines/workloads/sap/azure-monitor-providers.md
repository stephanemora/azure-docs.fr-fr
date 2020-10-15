---
title: Fournisseurs Azure Monitor pour solutions SAP | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées sur Azure Monitor pour fournisseurs de solutions SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 235572cc4d697e7488765c464b12f9349c1e012b
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994176"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Fournisseurs Azure Monitor pour solutions SAP (préversion)

## <a name="overview"></a>Vue d’ensemble  

Dans le contexte de Azure Monitor pour solutions SAP, un *type fournisseur*  fait référence à un *fournisseur* spécifique. Par exemple *SAP HANA*, qui est configuré pour un composant spécifique au sein du paysage SAP, comme la base de données SAP HANA. Un fournisseur contient les informations de connexion pour le composant correspondant et permet de collecter des données de télémétrie à partir de ce composant. Une ressource Azure Monitor pour solutions SAP (également appelée ressource du moniteur SAP) peut être configurée avec plusieurs fournisseurs du même type fournisseur ou plusieurs fournisseurs de plusieurs types fournisseur.
   
Les clients peuvent choisir de configurer différents types fournisseur pour activer la collecte des données à partir du composant correspondant dans leur paysage SAP. Par exemple, les clients peuvent configurer un fournisseur pour le type fournisseur SAP HANA, un autre fournisseur pour le type fournisseur de cluster haute disponibilité, etc.  

Les clients peuvent également choisir de configurer plusieurs fournisseurs d’un type fournisseur spécifique pour réutiliser la même ressource du moniteur SAP et le groupe géré associé. Apprenez-en davantage sur le groupe de ressources managé. Pour la préversion publique, les types fournisseur suivants sont pris en charge :   
- SAP HANA
- Cluster haute disponibilité
- Microsoft SQL Server

![Fournisseurs Azure Monitor pour solutions SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Il est recommandé aux clients de configurer au moins un fournisseur à partir des types fournisseur disponibles au moment du déploiement de la ressource du moniteur SAP. En configurant un fournisseur, les clients lancent la collecte de données à partir du composant correspondant pour lequel le fournisseur est configuré.   

Si les clients ne configurent aucun fournisseur au moment du déploiement de la ressource du moniteur SAP, bien que la ressource du moniteur SAP soit déployée avec succès, aucune donnée de télémétrie n’est collectée. Les clients ont la possibilité d’ajouter des fournisseurs après le déploiement via la ressource du moniteur SAP au sein de Portail Azure. Les clients peuvent ajouter ou supprimer des fournisseurs de la ressource du moniteur SAP à tout moment.

> [!Tip]
> Si vous souhaitez que Microsoft implémente un fournisseur spécifique, laissez vos commentaires via le lien à la fin de ce document ou contactez votre équipe de compte.  

## <a name="provider-type-sap-hana"></a>Type fournisseur SAP HANA

Les clients peuvent configurer un ou plusieurs fournisseurs de type fournisseur *SAP HANA* pour activer la collecte des données à partir de la base de données SAP HANA. Le fournisseur SAP HANA se connecte à la base de données SAP HANA sur le port SQL, extrait les données de télémétrie de la base de données et les envoie (push) à l’espace de travail Log Analytics de l’abonnement client. Le fournisseur SAP HANA collecte des données toutes les minutes depuis la base de données SAP HANA.  

En préversion publique, les clients peuvent s’attendre à voir les données suivantes avec le fournisseur SAP HANA : Utilisation de l’infrastructure sous-jacente, état de l’hôte SAP HANA, réplication du système SAP HANA et données de télémétrie de Sauvegarde Microsoft Azure SAP HANA. Pour configurer le fournisseur SAP HANA, l’adresse IP de l’hôte, le numéro de port SQL HANA et le nom d’utilisateur et le mot de passe SYSTEMDB sont requis. Il est recommandé aux clients de configurer le fournisseur SAP HANA par rapport à SYSTEMDB. Toutefois, des fournisseurs supplémentaires peuvent être configurés pour d’autres locataires de base de données.

![Fournisseurs Azure Monitor pour solutions SAP : SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Type fournisseur de cluster haute disponibilité
Les clients peuvent configurer un ou plusieurs fournisseurs de type fournisseur de *cluster haute disponibilité* pour activer la collecte de données à partir du cluster Pacemaker dans le paysage SAP. Le fournisseur de cluster haute disponibilité se connecte à Pacemaker, à l’aide du point de terminaison [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter), extrait les données de télémétrie de la base de données et les envoie (push) à l’espace de travail Log Analytics de l’abonnement client. Le fournisseur de cluster haute disponibilité collecte les données toutes les 60 secondes depuis Pacemaker.  

En préversion publique, les clients peuvent s’attendre à voir les données suivantes avec le fournisseur de cluster :   
 - État du cluster représenté sous la forme d’un regroupement du nœud et de l’état de la ressource 
 - [autres](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Fournisseurs Azure Monitor pour solutions SAP : cluster haute disponibilité](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Pour configurer le fournisseur de cluster haute disponibilité, deux étapes principales sont nécessaires : 
1. Installer [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) dans *chaque nœud* dans le cluster Pacemaker 
    - Les clients peuvent utiliser des scripts Azure Automation pour déployer un cluster haute disponibilité. Les scripts installent [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) sur chaque nœud de cluster.  
    - ou les clients peuvent effectuer une installation manuelle, en suivant les étapes de [cette page](https://github.com/ClusterLabs/ha_cluster_exporter) 
2. Configurer le fournisseur de cluster haute disponibilité dans *chaque* nœud dans le cluster Pacemaker  
  Pour configurer le fournisseur de cluster haute disponibilité, l’URL Prometheus, le nom du cluster, le nom d’hôte et l’ID du système sont nécessaires.   
  Il est recommandé aux clients de configurer un fournisseur par nœud de cluster.   

## <a name="provider-type-microsoft-sql-server"></a>Type fournisseur Microsoft SQL Server

Les clients peuvent configurer un ou plusieurs fournisseurs de type fournisseur *Microsoft SQL Server* pour activer la collecte des données à partir de [SQL Server sur les machines virtuelles](https://azure.microsoft.com/services/virtual-machines/sql-server/). Le fournisseur SQL Server se connecte à Microsoft SQL Server sur le port SQL, extrait les données de télémétrie de la base de données et les envoie (push) à l’espace de travail Log Analytics de l’abonnement client. Le SQL Server doit être configuré pour l’authentification SQL et un compte de connexion SQL Server, avec la base de données SAP comme base de données par défaut du fournisseur, doit être créé. Le fournisseur SQL Server collecte des données entre toutes les 60 secondes à toutes les heures, à partir de SQL Server.  

En préversion publique, les clients peuvent s’attendre à voir les données suivantes avec le fournisseur SQL Server : utilisation de l’infrastructure sous-jacente, instructions SQL principales, table la plus grande, problèmes enregistrés dans les journaux d’erreurs SQL Server, processus bloquants, etc.  

Pour configurer le fournisseur Microsoft SQL Server, l’ID du système SAP, l’adresse IP de l’hôte, le numéro de port SQL Server ainsi que le nom et le mot de passe du compte de connexion SQL Server sont requis.

![Fournisseurs Azure Monitor pour solutions SAP : SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Étapes suivantes

- Créez votre première ressource Azure Monitor pour solutions SAP.
- Avez-vous des questions sur Azure Monitor pour solutions SAP ? Consultez la section [FAQ](./azure-monitor-faq.md)
