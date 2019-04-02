---
title: Surveiller et gérer Hadoop avec l’API REST Ambari - Azure HDInsight
description: Découvrez comment utiliser Ambari pour surveiller et gérer les clusters Hadoop dans Azure HDInsight. Dans ce document, vous allez apprendre à utiliser l'API REST d'Ambari incluse avec les clusters HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 37d4c4dbbe623d68edf1de5454d7752a81fc2bd2
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801881"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gérer des clusters HDInsight à l’aide de l’API REST d’Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Découvrez comment utiliser l’API REST d’Apache Ambari pour gérer et surveiller les clusters Apache Hadoop dans Azure HDInsight.

## <a id="whatis"></a>Qu’est-ce qu’Apache Ambari ?
[Apache Ambari](https://ambari.apache.org) simplifie la gestion et surveillance des clusters Hadoop en fournissant un web facile à utiliser l’interface utilisateur soutenu par son [API REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari est fourni par défaut avec les clusters HDInsight Linux.

## <a name="prerequisites"></a>Conditions préalables
* **Un cluster Hadoop sur HDInsight**. Consultez [prise en main HDInsight sous Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash sur Ubuntu sur Windows 10**.  Les exemples de cet article utilisent l’interpréteur de commandes Bash sur Windows 10. Consultez [sous-système Windows pour Linux Installation Guide pour Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) pour les étapes d’installation.  Autres [interpréteurs de commandes Unix](https://www.gnu.org/software/bash/) fonctionne également.  Les exemples, avec de légères modifications, peuvent travailler sur une invite de commandes de Windows.  Vous pouvez également utiliser Windows PowerShell.

* **jq**, un processeur JSON en ligne de commande.  Voir [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Vous pouvez également utiliser [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>URI de base pour l’API Rest Ambari

 L’URI de base pour l’API REST Ambari sur HDInsight est `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, où `CLUSTERNAME` est le nom de votre cluster.  Les noms de cluster dans les URI sont **respect de la casse**.  Bien que le nom du cluster dans la partie du nom de domaine complet (FQDN) de l’URI (CLUSTERNAME.azurehdinsight.net) ne respecte pas la casse, les autres occurrences dans l’URI respectent la casse.

## <a name="authentication"></a>Authentication

Une connexion à Ambari sur HDInsight requiert HTTPS. Utilisez le nom du compte Administrateur (la valeur par défaut est **admin**) et le mot de passe fournis lors de la création du cluster.

## <a name="examples"></a>Exemples

### <a name="setup-preserve-credentials"></a>Programme d’installation (informations d’identification Preserve)
Conserver vos informations d’identification pour éviter de devoir entrer de nouveau les pour chaque exemple.  Le nom du cluster est conservé dans une étape distincte.

**A. Bash**  
Modifiez le script ci-dessous en remplaçant `PASSWORD` avec votre mot de passe réel.  Puis entrez la commande.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifier le nom du cluster correctement casse
La casse réelle du nom du cluster peut être différente de celui prévu, en fonction de la façon dont le cluster a été créé.  Les étapes ici montrent la casse réelle et puis le stocker dans une variable pour tous les exemples suivants.

Modifier les scripts ci-dessous pour remplacer `CLUSTERNAME` avec le nom de votre cluster. Puis entrez la commande. (Le nom du cluster pour le nom de domaine complet ne respecte pas la casse).

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analyse des données JSON

L’exemple suivant utilise [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) pour analyser le document de réponse JSON et afficher uniquement les `health_report` plus d’informations à partir des résultats.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Obtenir le nom de domaine complet des nœuds de cluster

Lorsque vous travaillez avec HDInsight, vous pouvez avoir besoin de connaître le nom de domaine complet (FQDN) d'un nœud de cluster. Vous pouvez facilement récupérer le nom de domaine complet des différents nœuds du cluster à l’aide des exemples suivants :

**Tous les nœuds**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Nœuds principaux**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nœuds de travail**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nœuds Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Obtenir l’adresse IP interne des nœuds de cluster

Les adresses IP renvoyées par les exemples de cette section ne sont pas directement accessibles sur Internet. Elles sont uniquement accessibles au sein du réseau virtuel Azure qui contient le cluster HDInsight.

Pour plus d’informations sur l’utilisation de HDInsight et des réseaux virtuels, consultez [Étendre les fonctionnalités HDInsight à l’aide d’un réseau virtuel Azure personnalisé](hdinsight-extend-hadoop-virtual-network.md).

Pour rechercher l’adresse IP, vous devez connaître le nom de domaine complet interne des nœuds de cluster. Lorsque cela est fait, vous pouvez ensuite obtenir l’adresse IP de l’hôte. Les exemples suivants interrogent d’abord Ambari pour connaître le nom de domaine complet de tous les nœuds de l’hôte, puis l’adresse IP de chaque hôte.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Obtenir le stockage par défaut

Quand vous créez un cluster HDInsight, vous devez utiliser un compte Stockage Azure ou Data Lake Storage comme stockage par défaut pour le cluster. Vous pouvez utiliser Ambari pour récupérer ces informations après avoir créé le cluster. Par exemple, si vous voulez lire/écrire des données sur le conteneur en dehors de HDInsight.

Les exemples suivants récupèrent la configuration de stockage par défaut du cluster :

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Ces exemples renvoient la première configuration appliquée au serveur (`service_config_version=1`), qui contient ces informations. Si vous récupérez une valeur qui a été modifiée après la création du cluster, vous devrez peut-être lister les versions de configuration et récupérer la plus récente.

La valeur de retour est similaire à l’un des exemples suivants :

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - Cette valeur indique que le cluster utilise un compte de stockage Azure pour le stockage par défaut. La valeur `ACCOUNTNAME` est le nom du compte de stockage. La partie `CONTAINER` est le nom du conteneur d’objets blob dans le compte de stockage. Le conteneur est la racine du stockage compatible HDFS du cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - Cette valeur indique que le cluster utilise Azure Data Lake Storage Gen2 comme stockage par défaut. Les valeurs `ACCOUNTNAME` et `CONTAINER` ont la même signification que pour le stockage Azure dont il est question précédemment.

* `adl://home` - Cette valeur indique que le cluster utilise Azure Data Lake Storage Gen1 comme stockage par défaut.

    Pour rechercher le nom du compte Data Lake Storage, utilisez les exemples suivants :

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    La valeur de retour est similaire à `ACCOUNTNAME.azuredatalakestore.net`, où `ACCOUNTNAME` est le nom du compte Data Lake Storage.

    Pour rechercher le répertoire dans Data Lake Storage qui contient le stockage du cluster, utilisez les exemples suivants :

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    La valeur de retour est similaire à `/clusters/CLUSTERNAME/`. Cette valeur est un chemin au sein du compte Data Lake Storage. Ce chemin d’accès est la racine du système de fichiers compatible HDFS du cluster.  

> [!NOTE]  
> Le [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) applet de commande fourni par [Azure PowerShell](/powershell/azure/overview) retourne également les informations de stockage pour le cluster.


### <a name="get-all-configurations"></a> Obtenir toutes les configurations

Récupérez les configurations disponibles pour votre cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Cet exemple renvoie un document JSON avec la configuration actuelle (identifiée par la valeur *tag* ) pour les composants installés sur le cluster. L’exemple suivant est un extrait des données renvoyées à partir d’un type de cluster Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Obtenir la configuration de composant spécifique

Récupérez la configuration du composant qui vous intéresse. Dans l’exemple suivant, remplacez `INITIAL` par la valeur de balise retournée à partir de la requête précédente.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Cet exemple renvoie un document JSON qui contient la configuration actuelle pour le composant `livy2-conf`.

### <a name="update-configuration"></a>Mettre à jour la configuration

1. Créez `newconfig.json`.  
   Modifier, puis entrez les commandes ci-dessous :

   * Remplacez `livy2-conf` avec le composant souhaité.
   * Remplacez `INITIAL` avec la valeur réelle récupérée pour `tag` de [obtenir toutes les configurations](#get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **Powershell b.**  
     Le script PowerShell utilise [jq](https://stedolan.github.io/jq/).  Modifier `C:\HD\jq\jq-win64` ci-dessous pour refléter votre chemin d’accès réel et la version de [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq est utilisé pour convertir les données récupérées à partir de HDInsight dans un nouveau modèle de configuration. Plus précisément, ces exemples permettent d’effectuer les actions suivantes :

   * Il crée une valeur unique qui contient la chaîne « version » et la date, qui est stockée dans `newtag`.

   * Il crée un document racine pour la nouvelle configuration souhaitée.

   * Il récupère le contenu du tableau `.items[]` et l’ajoute à l’élément **desired_config**.

   * Il supprime les éléments `href`, `version` et `Config`, car ils ne sont pas nécessaires à l’envoi d’une nouvelle configuration.

   * Ajoute un élément `tag` avec une valeur de `version#################`. La partie numérique est basée sur la date actuelle. Chaque configuration doit avoir une balise unique.

     Enfin, les données sont enregistrées dans le document `newconfig.json`. La structure du document doit se présenter comme suit :

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Modifier `newconfig.json`.  
   Ouvrez le document `newconfig.json` et modifiez/ajoutez des valeurs dans l’objet `properties`. L’exemple suivant modifie la valeur de `"livy.server.csrf_protection.enabled"` de `"true"` en `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Enregistrez le fichier une fois que vous avez apporté des modifications.

3. Envoyer `newconfig.json`.  
   Utilisez les commandes suivantes pour envoyer la configuration mise à jour à Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Ces commandes envoient le contenu du fichier **newconfig.json** au cluster en tant que nouvelle configuration souhaitée. La demande renvoie un document JSON. L’élément **versionTag** de ce document doit correspondre à la version que vous avez envoyée et l’objet **configs** contient les modifications de configuration que vous avez demandées.

### <a name="restart-a-service-component"></a>redémarrer un composant de service

À ce stade, si vous regardez l’interface web d’Ambari, le service Spark indique qu’il doit être redémarré pour que la nouvelle configuration prenne effet. Procédez comme suit pour redémarrer le service.

1. Utilisez ce qui suit pour activer le mode de maintenance pour le service Spark2 :

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Vérifier le mode de maintenance  

    Ces commandes envoient un document JSON au serveur qui active le mode de maintenance. Vous pouvez vérifier que le service est désormais en mode maintenance à l’aide de la demande suivante :

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    La valeur de retour est `ON`.

3. Ensuite, utilisez ce qui suit pour désactiver le service Spark2 :

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    La réponse ressemble à ce qui suit :

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > La valeur `href` retournée par cet URI à l’aide de l’adresse IP interne du nœud du cluster. Pour l’utiliser depuis l’extérieur du cluster, remplacez la partie « 10.0.0.18:8080 » par le nom de domaine complet du cluster.  

4. Vérifiez la demande.  
    Modifiez la commande ci-dessous en remplaçant `29` avec la valeur réelle pour `id` retourné à partir de l’étape précédente.  Les commandes suivantes récupèrent l’état de la demande :

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Une réponse de `COMPLETED` indique que la demande est terminée.

5. Une fois la demande précédente terminée, utilisez ce qui suit pour démarrer le service Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    Le service utilise désormais la nouvelle configuration.

6. Enfin, utilisez les éléments suivants pour désactiver le mode de maintenance.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une référence complète de l’API REST, consultez la page [Référence V1 de l’API d’Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

