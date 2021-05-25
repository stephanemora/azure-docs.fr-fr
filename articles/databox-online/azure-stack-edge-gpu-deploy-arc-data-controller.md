---
title: Déployer un contrôleur de données Azure Arc sur votre appareil Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit comment déployer un contrôleur de données Azure Arc et Azure Data Services sur votre appareil GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: ebccfdad883242411b4f45b717553ebd25ee69a9
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633516"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Déployer Azure Data Services sur votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article décrit le processus de création d’un contrôleur de données Azure Arc, puis de déploiement d’Azure Data Services sur votre appareil Azure Stack Edge Pro avec GPU. 

Le contrôleur de données Azure Arc est le plan de contrôle local qui permet d’utiliser Azure Data Services dans des environnements gérés par le client. Une fois que vous avez créé le contrôleur de données Azure Arc sur le cluster Kubernetes qui s’exécute sur votre appareil GPU Azure Stack Edge Pro, vous pouvez déployer des services Azure Data Services, tels que SQL Managed Instance, sur ce contrôleur de données.

La procédure de création d’un contrôleur de données et de déploiement d’une SQL Managed Instance implique l’utilisation de PowerShell et `kubectl`, un outil natif qui fournit un accès par ligne de commande au cluster Kubernetes sur l’appareil.


## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez accès à un appareil GPU Azure Stack Edge Pro et vous avez activé votre appareil comme décrit dans [Activer Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Vous avez activé le rôle de calcul sur l’appareil. Un cluster Kubernetes a également été créé sur l’appareil quand vous avez configuré le calcul sur l’appareil conformément aux instructions données dans [Configurer le calcul sur votre appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Vous avez accès au point de terminaison de l’API Kubernetes depuis la page **Appareil** de votre interface utilisateur web locale. Pour plus d’informations, consultez les instructions données dans [Obtenir le point de terminaison de l’API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Vous avez accès à un client qui se connecte à votre appareil. 
    1. Cet article utilise un système client Windows exécutant PowerShell 5.0 ou une version ultérieure pour accéder à l’appareil. Vous pouvez utiliser un autre client avec un [système d’exploitation pris en charge](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). 
    1. Installez `kubectl` sur votre client. Pour la version client :
        1. Identifiez la version du serveur Kubernetes installée sur l’appareil. Dans l'interface utilisateur locale de l'appareil, accédez à la page **Mises à jour logicielles**. Notez la **version du serveur Kubernetes** sur cette page.
        1. Téléchargez un client dont la version ne diffère pas de plus d’une version mineure par rapport à la version du nœud principal. La version du client peut être supérieure d’une seule version mineure à celle du nœud principal. Par exemple, un nœud principal v1.3 peut fonctionner avec des nœuds v1.1, v1.2 et v.1.3 et avec des clients v1.2, v1.3 et v1.4. Pour plus d’informations sur la version du client Kubernetes, consultez [Stratégie de prise en charge des versions et des différences de version Kubernetes](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Vous pouvez également [installer les outils clients pour le déploiement et la gestion des services de données avec Azure Arc](../azure-arc/data/install-client-tools.md). Ces outils sont non obligatoires, mais recommandés.  
1. Assurez-vous de disposer de suffisamment de ressources disponibles sur votre appareil pour approvisionner un contrôleur de données et une SQL Managed Instance. Pour le contrôleur de données et une SQL Managed Instance, vous aurez besoin d’un minimum de 16 Go de RAM et de 4 cœurs de processeur. Pour obtenir des instructions détaillées, accédez à [Configuration minimale requise pour le déploiement des services de données avec Azure Arc](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Configurez les IP des services externes Kubernetes

1. Accédez à l’interface utilisateur web locale de l’appareil, puis accédez à **Calcul**.
1. Sélectionnez le réseau activé pour Calcul. 

    ![Page Calcul de l'interface utilisateur web locale 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Veillez à fournir trois adresses IP de service externe Kubernetes supplémentaires (en plus des adresses IP que vous avez déjà configurées pour d’autres services externes ou conteneurs). Le contrôleur de données utilise deux adresses IP de service et la troisième adresse IP est utilisée lorsque vous créez une SQL Managed Instance. Vous aurez besoin d’une adresse IP pour chaque service de données supplémentaire que vous allez déployer. 

    ![Page Calcul dans l'interface utilisateur web locale 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Appliquez les paramètres et ces nouvelles adresses IP prendront effet immédiatement sur un cluster Kubernetes déjà existant. 


## <a name="deploy-azure-arc-data-controller"></a>Déployer un contrôleur de données Azure Arc

Avant de déployer un contrôleur de données, vous devez créer un espace de noms.

### <a name="create-namespace"></a>Créer un espace de noms 

Créez un espace de noms dédié dans lequel vous allez déployer le contrôleur de données. Vous allez également créer un utilisateur, puis accorder à l’utilisateur l’accès à l’espace de noms que vous avez créé. 

> [!NOTE]
> Pour les utilisateurs et les espaces de noms, les [conventions de nommage de sous-domaines DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) s’appliquent.

1. [Connectez-vous à l’interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Créez un espace de noms. Tapez :

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Créez un utilisateur. Tapez : 

    `New-HcsKubernetesUser -UserName <User name>`

1. Un fichier config s’affiche en texte brut. Copiez ce fichier et enregistrez-le en tant que fichier *config*. 

    > [!IMPORTANT]
    > N’enregistrez pas le fichier config au format *.txt*, mais plutôt sans aucune extension de fichier.

1. Le fichier config doit résider dans le dossier `.kube` de votre profil utilisateur sur l’ordinateur local. Copiez le fichier dans ce dossier dans votre profil utilisateur.

    ![Emplacement du fichier config sur le client](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Accordez à l’utilisateur l’accès à l’espace de noms que vous avez créés. Tapez : 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Voici un exemple de sortie des commandes précédentes. Dans cet exemple, nous créons un espace de noms `myadstest`, un utilisateur `myadsuser` et accordons à l’utilisateur l’accès à l’espace de noms.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Ajoutez une entrée DNS au fichier hosts sur votre système. 

    1. Exécutez le Bloc-notes en tant qu’administrateur et ouvrez le fichier `hosts` situé à l’emplacement `C:\windows\system32\drivers\etc\hosts`. 
    2. Utilisez les informations que vous avez enregistrées à partir de la page **Appareil** dans l’interface utilisateur locale (conditions préalables) pour créer l’entrée dans le fichier hosts. 

        Par exemple, copiez ce point de terminaison `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` pour créer l’entrée suivante avec l’adresse IP et le domaine DNS de l’appareil : 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Pour vérifier que vous pouvez vous connecter aux pods Kubernetes, démarrez une invite de commande ou une session PowerShell. Tapez :
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Vous pouvez maintenant déployer vos applications du contrôleur de données et des services de données dans l’espace de noms, puis afficher ces applications et leurs journaux.

### <a name="create-data-controller"></a>Créer un contrôleur de données

Le contrôleur de données est une collection de pods déployés sur votre cluster Kubernetes pour fournir une API, le service de contrôleur, le programme d’amorçage et les bases de données de surveillance et les tableaux de bord. Procédez comme suit pour créer un contrôleur de données sur le cluster Kubernetes qui existe sur votre appareil Azure Stack Edge dans l’espace de noms que vous avez créé précédemment.   

1. Rassemblez les informations suivantes dont vous aurez besoin pour créer un contrôleur de données :

    
    |Column1  |Column2  |
    |---------|---------|
    |Nom du contrôleur de données     |Un nom descriptif pour votre contrôleur de données. Par exemple : `arctestdatacontroller`.         |
    |Nom d’utilisateur du contrôleur de données     |Tout nom d’utilisateur pour l’administrateur du contrôleur de données. Le nom d’utilisateur et le mot de passe du contrôleur de données permettent de s’authentifier auprès de l’API du contrôleur de données pour exécuter des fonctions d’administration.          |
    |Mot de passe du contrôleur de données     |Tout mot de passe pour l’administrateur du contrôleur de données. Choisissez un mot de passe sécurisé et partagez-le uniquement avec ceux qui doivent disposer de privilèges d’administrateur de cluster.         |
    |Nommer votre espace de noms Kubernetes     |Le nom de l'espace de noms Kubernetes dans lequel vous souhaitez créer le contrôleur de données.         |
    |ID d’abonnement Azure     |Le GUID de l'abonnement Azure dans lequel vous souhaitez que soit créée la ressource de contrôleur de données dans Azure.         |
    |Nom du groupe de ressources Azure     |Le nom du groupe de ressources dans lequel vous souhaitez que soit créée la ressource de contrôleur de données dans Azure.         |
    |Emplacement Azure     |L’ emplacement Azure où les métadonnées de la ressource de contrôleur de données seront stockées. Pour obtenir la liste des régions disponibles, consultez Infrastructure globale Azure / Produits par région.|


1. Connectez-vous à l’interface PowerShell. Pour créer le contrôleur de données, entrez : 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Voici un exemple de sortie des commandes précédentes.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Le déploiement peut prendre environ 5 minutes.

    > [!NOTE]
    > Le contrôleur de données créé sur le cluster Kubernetes sur votre appareil GPU Azure Stack Edge Pro fonctionne uniquement en mode déconnecté dans la version actuelle. Le mode déconnecté est destiné au contrôleur de données et non à votre appareil.

### <a name="monitor-data-creation-status"></a>Surveiller l’état de création des données

1. Ouvrez une autre fenêtre PowerShell.
1. Utilisez la commande `kubectl` suivante pour surveiller l’état de création du contrôleur de données. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Lorsque le contrôleur est créé, l’état doit être `Ready`.
    Voici un exemple de sortie de la commande précédente :

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Pour identifier les adresses IP attribuées aux services externes qui s’exécutent sur le contrôleur de données, utilisez la commande `kubectl get svc -n <namespace>` . Voici un exemple de sortie :

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Déployer une instance SQL gérée

Une fois que vous avez correctement créé le contrôleur de données, vous pouvez utiliser un modèle pour déployer une instance SQL gérée sur le contrôleur de données.

### <a name="deployment-template"></a>Modèle de déploiement

Utilisez le modèle de déploiement suivant pour déployer une instance SQL gérée sur le contrôleur de données sur votre appareil.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Nom des métadonnées
    
Le nom de la métadonnée est le nom de l’instance SQL gérée. Le pod associée dans le `deployment.yaml` précédent sera nommé `sqlex-n` (`n` est le nombre de pods associés à l’application). 
    
#### <a name="password-and-username-data"></a>Données de nom d’utilisateur et de mot de passe

Le nom d’utilisateur et le mot de passe du contrôleur de données permettent de s’authentifier auprès de l’API du contrôleur de données pour exécuter des fonctions d’administration. Le secret Kubernetes pour le nom d’utilisateur et le mot de passe du contrôleur de données dans le modèle de déploiement sont des chaînes encodées en base64. 

Vous pouvez utiliser un outil en ligne pour coder en base64 le nom d’utilisateur et le mot de passe souhaités, ou vous pouvez utiliser des outils d’interface de ligne de commande intégrés en fonction de votre plateforme. Lorsque vous utilisez un outil de codage en base64 en ligne, fournissez les chaînes de nom d’utilisateur et de mot de passe (que vous avez entrées lors de la création du contrôleur de données) dans l’outil. L’outil générera alors les chaînes encodées en base64 correspondantes.
    
#### <a name="service-type"></a>Type de service

Le type de service doit être défini sur `LoadBalancer`.
    
#### <a name="storage-class-name"></a>Nom de la classe de stockage

Vous pouvez identifier la classe de stockage sur votre appareil Azure Stack Edge que le déploiement utilisera pour les données, les sauvegardes, les journaux de données et les journaux. Utilisez la commande `kubectl get storageclass` pour faire en sorte que la classe de stockage soit déployée sur votre appareil.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Dans l’exemple de sortie précédent, la classe de stockage `ase-node-local` sur votre appareil doit être spécifiée dans le modèle.
 
#### <a name="spec"></a>Spec

Pour créer une instance SQL gérée sur votre appareil Azure Stack Edge, vous pouvez spécifier vos exigences en matière de mémoire et de processeur dans la section spécifique du `deployment.yaml`. Chaque instance SQL managée doit demander au moins 2 Go de mémoire et 1 cœur de processeur comme indiqué dans l’exemple suivant. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Pour des conseils détaillés sur le dimensionnement des contrôleurs de données et d’une instance SQL gérée, consultez les [détails de dimensionnement des instances SQL gérées](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Exécuter le modèle de déploiement

Exécutez le `deployment.yaml` à l’aide de la commande suivante :

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Voici un exemple de sortie de la commande suivante :

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

Le pod `sqlex-0`dans l’exemple de sortie indique l’état de l’instance SQL gérée.

## <a name="remove-data-controller"></a>Supprimer un contrôleur de données

Pour supprimer le contrôleur de données, supprimez l’espace de noms dédié dans lequel vous l’avez déployé.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Étapes suivantes

- [Déployez une application sans état sur votre Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
