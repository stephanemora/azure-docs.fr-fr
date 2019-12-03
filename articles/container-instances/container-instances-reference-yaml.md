---
title: Référence YAML pour le groupe de conteneurs
description: Informations de référence pour le fichier YAML pris en charge par Azure Container Instances pour configurer un groupe de conteneurs
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 5603f2e0f63c4f83a6d3761feb540abb8b8b7d5c
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533488"
---
# <a name="yaml-reference-azure-container-instances"></a>Informations de référence YAML : Azure Container Instances

Cet article traite de la syntaxe et des propriétés du fichier YAML pris en charge par Azure Container Instances pour configurer un [groupe de conteneurs](container-instances-container-groups.md). Vous pouvez utiliser un fichier YAML pour entrer la configuration du groupe dans la commande [az container create][az-container-create] dans Azure CLI. 

Un fichier YAML est un moyen pratique de configurer un groupe de conteneurs pour des déploiements reproductibles. Plutôt que d’utiliser un [modèle Resource Manager](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) ou les kits SDK Azure Container Instances pour créer ou mettre à jour un groupe de conteneurs, il s’agit d’une solution de rechange succincte.

> [!NOTE]
> Ces informations de référence s’appliquent aux fichiers YAML pour l’API REST Azure Container Instances version `2018-10-01`.

## <a name="schema"></a>Schéma 

Le schéma du fichier YAML vous est présenté ci-dessous. Il comporte des commentaires pour mettre en avant les principales propriétés. Pour obtenir une description des propriétés de ce schéma, consultez la section [Valeurs de propriétés](#property-values).

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Valeurs de propriétés

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objet Microsoft.ContainerInstance/containerGroups

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Nom | string | OUI | Nom du groupe de conteneurs. |
|  apiVersion | enum | OUI | 2018-10-01 |
|  location | string | Non | Emplacement de la ressource. |
|  tags | object | Non | Étiquettes de la ressource. |
|  identité | object | Non | Identité du groupe de conteneurs, si elle est configurée. - [Objet ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | object | OUI | [Objet ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objet ContainerGroupIdentity

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Type | enum | Non | Type d’identité utilisé pour le groupe de conteneurs. Le type « SystemAssigned, UserAssigned » comprend à la fois une identité créée implicitement et un ensemble d’identités attribuées par l’utilisateur. Le type « None » supprime les identités du groupe de conteneurs. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | Non | Liste d’identités utilisateur associées au groupe de conteneurs. Les références clés du dictionnaire d’identités utilisateur seront les ID de ressource Azure Resource Manager sous la forme : '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objet ContainerGroupProperties

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  containers | array | OUI | Conteneurs présents dans le groupe de conteneurs. - [Objet Container](#Container) |
|  imageRegistryCredentials | array | Non | Informations d’identification du registre d’images à partir desquelles le groupe de conteneurs est créé. - [Objet ImageRegistryCredential](#ImageRegistryCredential) |
|  restartPolicy | enum | Non | Stratégie de redémarrage pour tous les conteneurs présents dans le groupe de conteneurs. - `Always` Toujours redémarrer – `OnFailure` Redémarrer en cas d’échec – `Never` Ne jamais redémarrer. - Always, OnFailure, Never |
|  ipAddress | object | Non | Type d’adresse IP du groupe de conteneurs. - [Objet IpAddress](#IpAddress) |
|  osType | enum | OUI | Type de système d’exploitation exigé par les conteneurs présents dans le groupe de conteneurs. - Windows ou Linux |
|  volumes | array | Non | Liste des volumes qui peuvent être montés par les conteneurs présents dans ce groupe de conteneurs. - [Objet Volume](#Volume) |
|  diagnostics | object | Non | Informations de diagnostic pour un groupe de conteneurs. - [Objet ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  networkProfile | object | Non | Informations de profil réseau pour un groupe de conteneurs. - [Objet ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Non | Informations de configuration DNS pour un groupe de conteneurs. - [Objet DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objet Container

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Nom | string | OUI | Nom fourni par l’utilisateur de l’instance de conteneur. |
|  properties | object | OUI | Propriétés de l’instance de conteneur. - [Objet ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objet ImageRegistryCredential

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  server | string | OUI | Serveur de registre d’images Docker sans protocole comme « http » ou « https ». |
|  username | string | OUI | Nom d’utilisateur du registre privé. |
|  password | string | Non | Mot de passe du registre privé. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Objet IpAddress

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  ports | array | OUI | Liste des ports exposés dans le groupe de conteneurs. - [Objet Port](#Port) |
|  Type | enum | OUI | Indique si l’adresse IP est exposée à l’Internet public ou à un réseau virtuel privé. - Public ou Private |
|  ip | string | Non | Adresse IP exposée à l’Internet public. |
|  dnsNameLabel | string | Non | Étiquette du nom DNS de l’adresse IP. |


<a id="Volume" />

### <a name="volume-object"></a>Objet Volume

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Nom | string | OUI | Nom du volume. |
|  azureFile | object | Non | Volume de fichier Azure. - [Objet AzureFileVolume](#AzureFileVolume) |
|  emptyDir | object | Non | Volume de répertoire vide. |
|  secret | object | Non | Volume de secret. |
|  gitRepo | object | Non | Volume de dépôt git. - [Objet GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objet ContainerGroupDiagnostics

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Non | Informations analytiques des journaux du groupe de conteneurs. - [Objet logAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objet ContainerGroupNetworkProfile

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  id | string | OUI | Identificateur d’un profil réseau. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Objet DnsConfiguration

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | OUI | Serveurs DNS du groupe de conteneurs. - string |
|  searchDomains | string | Non | Domaines de recherche DNS pour la recherche de nom d’hôte dans le groupe de conteneurs. |
|  options | string | Non | Options DNS du groupe de conteneurs. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objet ContainerProperties

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  image | string | OUI | Nom de l’image utilisée pour créer l’instance de conteneur. |
|  command | array | Non | Commandes à exécuter dans l’instance de conteneur dans l’exec form. - string |
|  ports | array | Non | Port exposé dans l’instance de conteneur. - [Objet ContainerPort](#ContainerPort) |
|  environmentVariables | array | Non | Variable d’environnement à définir dans l’instance de conteneur. - [Objet EnvironmentVariable](#EnvironmentVariable) |
|  les ressources | object | OUI | Besoins en ressources de l’instance de conteneur. - [Objet ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | array | Non | Montages de volume accessibles à l’instance de conteneur. - [Objet VolumeMount](#VolumeMount) |
|  livenessProbe | object | Non | Probe liveness. - [Objet ContainerProbe](#ContainerProbe) |
|  readinessProbe | object | Non | Probe readiness. - [Objet ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Objet Port

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Non | Protocole associé au port. - TCP ou UDP |
|  port | integer | OUI | Numéro de port. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objet AzureFileVolume

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  shareName | string | OUI | Nom du partage de fichiers Azure à monter en tant que volume. |
|  readOnly | boolean | Non | Indicateur précisant si le partage de fichiers Azure monté en tant que volume est en lecture seule. |
|  storageAccountName | string | OUI | Nom du compte de stockage qui contient le partage de fichiers Azure. |
|  storageAccountKey | string | Non | Clé d’accès du compte de stockage utilisée pour accéder au partage de fichiers Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objet GitRepoVolume

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Répertoire | string | Non | Nom du répertoire cible. Ne doit pas contenir ou commencer par « .. ».  Si « . » est spécifié, le répertoire du volume est le dépôt git.  Sinon, s’il est spécifié, le volume contient le dépôt git dans le sous-répertoire avec le nom donné. |
|  repository | string | OUI | URL du dépôt |
|  revision | string | Non | Hachage de validation pour la révision spécifiée. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objet LogAnalytics

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | OUI | ID d’espace de travail pour l’analytique des journaux. |
|  workspaceKey | string | OUI | Clé d’espace de travail pour l’analytique des journaux. |
|  logType | enum | Non | Type de journal à utiliser. - ContainerInsights ou ContainerInstanceLogs |
|  metadata | object | Non | Métadonnées pour l’analytique des journaux. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objet ContainerPort

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Non | Protocole associé au port. - TCP ou UDP |
|  port | integer | OUI | Numéro de port exposé dans le groupe de conteneurs. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objet EnvironmentVariable

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Nom | string | OUI | Nom de la variable d’environnement. |
|  value | string | Non | Valeur de la variable d’environnement. |
|  secureValue | string | Non | Valeur de la variable d’environnement sécurisée. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objet ResourceRequirements

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  requêtes | object | OUI | Demandes en ressources de cette instance de conteneur. - [Objet ResourceRequests](#ResourceRequests) |
|  limites | object | Non | Limites en ressources de cette instance de conteneur. - [Objet ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objet VolumeMount

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  Nom | string | OUI | Nom du montage de volume. |
|  mountPath | string | OUI | Chemin de l’emplacement dans le conteneur où le volume doit être monté. Ne doit pas contenir de signe deux-points (:). |
|  readOnly | boolean | Non | Indicateur précisant si le montage du volume est en lecture seule. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objet ContainerProbe

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  exec | object | Non | Commande d’exécution à sonder – [Objet ContainerExec](#ContainerExec) |
|  httpGet | object | Non | Paramètres Http Get à sonder – [Objet ContainerHttpGet](#ContainerHttpGet) |
|  initialDelaySeconds | integer | Non | Retard initial en secondes. |
|  periodSeconds | integer | Non | Période en secondes. |
|  failureThreshold | integer | Non | Seuil de défaillance. |
|  successThreshold | integer | Non | Seuil de réussite. |
|  timeoutSeconds | integer | Non | Délai d’attente en secondes. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objet ResourceRequests

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | OUI | Demande de mémoire en Go de cette instance de conteneur. |
|  cpu | number | OUI | Demande de processeur de cette instance de conteneur. |
|  gpu | object | Non | Demande GPU de cette instance de conteneur. - [Objet GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objet ResourceLimits

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Non | Limite de mémoire en Go de cette instance de conteneur. |
|  cpu | number | Non | Limite processeur de cette instance de conteneur. |
|  gpu | object | Non | Limite GPU de cette instance de conteneur. - [Objet GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objet ContainerExec

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  command | array | Non | Commandes à exécuter dans le conteneur. - string |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Objet ContainerHttpGet

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  path | string | Non | Chemin de la sonde. |
|  port | integer | OUI | Numéro de port vers la sonde. |
|  scheme | enum | Non | Schéma. - http ou https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objet GpuResource

|  Nom | Type | Obligatoire | Valeur |
|  ---- | ---- | ---- | ---- |
|  count | integer | OUI | Nombre de la ressource GPU. |
|  sku | enum | OUI | Référence SKU de la ressource GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Étapes suivantes

Consultez le tutoriel [Déployer un groupe multiconteneur à l’aide d’un fichier YAML](container-instances-multi-container-yaml.md).

Consultez des exemples d’utilisation d’un fichier YAML pour déployer des groupes de conteneurs dans un [réseau virtuel](container-instances-vnet.md) ou pour [monter un volume externe](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

