---
title: Guide pratique pour obtenir les noms d’hôtes des nœuds de cluster Azure HDInsight
description: Découvrez comment obtenir les noms d’hôte et le nom de domaine complet des nœuds de cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111391"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Rechercher les noms d’hôte des nœuds de cluster

Un cluster HDInsight est créé avec le DNS public clustername.azurehdinsight.net. Quand vous vous connectez en mode SSH à des nœuds individuels ou que vous définissez la connexion à des nœuds de cluster dans le même réseau virtuel personnalisé, vous devez utiliser le nom d’hôte ou les noms de domaine complets (FQDN) des nœuds de cluster.

Dans cet article, vous allez apprendre à obtenir les noms d’hôte des nœuds de cluster. Vous pouvez le faire manuellement par le biais de l’interface utilisateur web d’Ambari ou automatiquement par l’intermédiaire de l’API REST d’Ambari.

> [!WARNING]
> Utilisez les approches recommandées suivantes pour récupérer les noms d’hôte des nœuds de cluster. Les nombres figurant dans le nom d’hôte ne sont pas garantis dans l’ordre et HDInsight peut changer le format du nom d’hôte pour l’aligner sur les machines virtuelles quand une actualisation de version a lieu. Ne dépendez d’aucune convention de nommage actuelle. 
>

Vous pouvez récupérer les noms d’hôte par le biais de l’interface utilisateur Ambari ou de l’API REST d’Ambari. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Récupérer les noms d’hôte à partir de l’interface utilisateur web d’Ambari
Vous pouvez utiliser l’interface utilisateur web d’Ambari pour récupérer les noms d’hôte quand vous vous connectez au nœud en mode SSH. La vue des hôtes dans l’interface utilisateur web d’Ambari est disponible sur votre cluster HDInsight à l’adresse `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts`, où `CLUSTERNAME` correspond au nom de votre cluster.

![Récupérer les noms d’hôte dans l’interface utilisateur d’Ambari](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Récupérer les noms d’hôte à partir de l’API REST d’Ambari
Lors de la création de scripts d’automatisation, vous pouvez utiliser l’API REST d’Ambari pour récupérer les noms d’hôte avant d’établir des connexions aux hôtes. Les nombres figurant dans le nom d’hôte ne sont pas garantis dans l’ordre et HDInsight peut changer le format du nom d’hôte pour l’aligner sur les machines virtuelles quand une actualisation de version a lieu. Ne dépendez d’aucune convention de nommage actuelle. 

Voici quelques exemples de récupération du nom de domaine complet pour les nœuds du cluster. Pour plus d’informations sur l’API REST d’Ambari, consultez [Gérer des clusters HDInsight à l’aide de l’API REST d’Apache Ambari](.\hdinsight-hadoop-manage-ambari-rest-api.md).

L’exemple suivant utilise [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) pour analyser le document de réponse JSON et afficher uniquement les noms d’hôte.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```