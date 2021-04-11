---
title: Afficher les journaux et les métriques à l’aide de Kibana et Grafana
description: Afficher les journaux et les métriques à l’aide de Kibana et Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669999"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Afficher les journaux et les métriques à l’aide de Kibana et Grafana

Les tableaux de bord web Kibana et Grafana sont fournis pour apporter des informations et plus clarté aux espaces de noms Kubernetes utilisés par les services de données Azure Arc activés.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Suivre les instances managées Azure SQL sur Azure Arc

Pour accéder aux journaux et aux tableaux de bord de surveillance pour la SQL Managed Instance avec Arc activé, exécutez la commande CLI `azdata` suivante

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
Les tableaux de bord Grafana sont les suivants :

* « Métriques Azure SQL Managed Instance »
* « Métriques de nœud hôte »
* « Métriques des pods hôtes »


> [!NOTE]
>  Lorsque vous êtes invité à entrer un nom d’utilisateur et un mot de passe, entrez le nom d’utilisateur et le mot de passe que vous avez fournis au moment où vous avez créé le contrôleur de données Azure Arc.

> [!NOTE]
>  Un avertissement de certificat s’affiche, car les certificats utilisés dans la préversion sont des certificats auto-signés.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Surveiller Azure Database pour PostgreSQL Hyperscale sur Azure Arc

Pour accéder aux journaux et aux tableaux de bord de surveillance pour PostgreSQL Hyperscale, exécutez la commande CLI `azdata` suivante

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

Les tableaux de bord PostgreSQL pertinents sont les suivants :

* « Métriques Postgres »
* « Métriques de table Postgres »
* « Métriques de nœud hôte »
* « Métriques des pods hôtes »


## <a name="additional-firewall-configuration"></a>Configuration complémentaire du pare-feu

En fonction l’emplacement de déploiement du contrôleur de données, il se peut que vous deviez ouvrir des ports sur votre pare-feu pour accéder aux points de terminaison Kibana et Grafana.

Voici un exemple montrant comment faire cela pour une machine virtuelle Azure. Vous devrez effectuer cette opération si vous avez déployé Kubernetes à l’aide du script.

Les étapes ci-dessous expliquent comment créer une règle de groupe de sécurité réseau pour les points de terminaison Kibana et Grafana :

### <a name="find-the-name-of-the-nsg"></a>Recherchez le nom du groupe de sécurité réseau

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Ajoutez la règle du groupe de sécurité réseau

Une fois que vous avez le nom du groupe de sécurité réseau, vous pouvez ajouter une règle à l’aide de la commande suivante :

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Étapes suivantes
- Essayez [Charger les métriques sur Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- En savoir plus sur Grafana :
   - [Bien démarrer](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Fondamentaux de Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Tutoriels Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- En savoir plus sur Kibana
   - [Introduction](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guide Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Présentation des explorations du tableau de bord avec des visualisations de données dans Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Comment créer des tableaux de bord Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

