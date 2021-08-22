---
title: Limiter le trafic de sortie dans un cluster Azure Red Hat OpenShift (ARO)
description: Découvrez les ports et adresses requis pour contrôler le trafic de sortie dans Azure Red Hat OpenShift (ARO).
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 53efeff1024d104f4dae2d70fc6f00c73d3d8fed
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291819"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Contrôler le trafic de sortie pour votre cluster Azure Red Hat OpenShift (ARO) [préversion]

Cet article donne les informations nécessaires pour sécuriser le trafic sortant d’Azure Red Hat OpenShift (ARO). Il contient les exigences du cluster pour un déploiement ARO de base, ainsi que des exigences supplémentaires pour les composants facultatifs Red Hat et tiers. Un [exemple](#private-aro-cluster-setup) de configuration de ces exigences avec Pare-feu Azure est fourni à la fin. N’oubliez pas que vous pouvez appliquer ces informations à Pare-feu Azure ou à toute autre méthode ou appliance de restriction des flux sortants.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous créez un cluster. Si vous avez besoin d’un cluster ARO de base, consultez le [démarrage rapide ARO](./tutorial-create-cluster.md).

> [!IMPORTANT]
> Les fonctionnalités d’évaluation ARO sont disponibles en libre-service en y adhérant. Les préversions sont fournies « en l’état » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service et de la garantie limitée. Les préversions ARO sont, dans la mesure du possible, partiellement couvertes par le service clientèle.

## <a name="minimum-required-fqdn--application-rules"></a>Noms de domaine complets/règles d’application requis au minimum

Cette liste est basée sur la liste des noms de domaine complets (FQDN) figurant ici dans les documents OpenShift : https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html.

Les noms de domaine complets/règles d’application suivantes sont requis :

| Nom FQDN de destination | Port | Utilisation |
| ----------- | ----------- | ------------- |
| **`*.quay.io`** | **HTTPS:443** | Obligatoire pour l’installation, utilisé par le cluster. Il est utilisé par le cluster pour télécharger les images conteneur de la plateforme. |
| **`registry.redhat.io`** | **HTTPS:443** | Obligatoire pour les modules complémentaires principaux. Ceci est utilisé par le cluster pour télécharger des composants de base tels que les outils de développement, les modules complémentaires basés sur un opérateur et les images conteneur fournies par Red Hat.
| **`mirror.openshift.com`** | **HTTPS:443** | Ceci est nécessaire dans l’environnement VDI ou votre ordinateur portable pour accéder au contenu et aux images de l’installation en miroir. Il est nécessaire dans le cluster pour télécharger les signatures des versions de la plateforme afin de savoir quelles images extraire de quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | Requis par le cluster pour vérifier si des mises à jour sont disponibles avant de télécharger les signatures d’image. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Registre privé interne pour les opérateurs ARO.  Obligatoire si vous n’autorisez pas les points de terminaison de service Microsoft.ContainerRegistry sur vos sous-réseaux. |
| **`management.azure.com`** | **HTTPS:443** | Ceci est utilisé par le cluster pour accéder aux API Azure. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Ceci est utilisé par le cluster pour l’authentification auprès d’Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client. |

> [!NOTE] 
> Pour de nombreux clients, l’exposition de *.blob, de *.table et d’autres grands espaces d’adressage crée un problème potentiel d’exfiltration des données. Vous pouvez envisager d’utiliser le [pare-feu de sortie d’OpenShift](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) pour protéger les applications déployées dans le cluster afin qu’elles n’atteignent pas ces destinations et utiliser Azure Private Link pour les besoins d’applications spécifiques.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Liste complète des noms de domaine complets obligatoires et facultatifs

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>PREMIER GROUPE : INSTALLATION ET TÉLÉCHARGEMENT DE PACKAGES ET D’OUTILS

- **`quay.io`**  : Obligatoire pour l’installation, utilisé par le cluster. Il est utilisé par le cluster pour télécharger les images conteneur de la plateforme.
- **`registry.redhat.io`**  : Obligatoire pour les modules complémentaires principaux. Il est utilisé par le cluster pour télécharger des composants de base tels que les outils de développement, les modules complémentaires basés sur un opérateur ou les images conteneur fournies par Red Hat, telles que notre intergiciel, l’image de base universelle, etc.
- **`sso.redhat.com`**  : Celui-ci est requis dans l’environnement VDI ou votre ordinateur portable pour se connecter à cloud.redhat.com. Il s’agit du site sur lequel nous pouvons télécharger le secret de tirage (pull secret) et utiliser certaines des solutions SaaS que nous proposons dans Red Hat pour faciliter la surveillance de vos abonnements, l’inventaire des clusters, les rapports de refacturation, entre autres choses.
- **`openshift.org`**  : Celui-ci est obligatoire dans l’environnement VDI ou votre ordinateur portable pour se connecter afin de télécharger des images RH CoreOS. Toutefois, dans Azure, ces images étant sélectionnées sur le marketplace, il n’est pas nécessaire de télécharger les images du système d’exploitation.

---

### <a name="second-group-telemetry"></a>DEUXIÈME GROUPE : TÉLÉMÉTRIE

Toute cette section peut être supprimée, mais, avant de savoir comment procéder, vérifiez ce dont il s’agit : https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html.
- **`cert-api.access.redhat.com`**  : À utiliser dans votre environnement VDI ou portable.
- **`api.access.redhat.com`**  : À utiliser dans votre environnement VDI ou portable.
- **`infogw.api.openshift.com`**  : À utiliser dans votre environnement VDI ou portable.
- **`https://cloud.redhat.com/api/ingress`**  : À utiliser dans le cluster pour l’opérateur Insights qui s’intègre à l’aaS Red Hat Insights.
Dans OpenShift Container Platform, les clients peuvent choisir de ne pas signaler les informations relatives à l’intégrité et à l’utilisation. Toutefois, les clusters connectés permettent à Red Hat de réagir plus rapidement aux problèmes et de mieux prendre en charge les clients, ainsi que de mieux comprendre la façon dont le produit met à niveau les clusters. Vérifiez les détails ici : https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html.

---

### <a name="third-group-cloud-apis"></a>TROISIÈME GROUPE : API CLOUD

- **`management.azure.com`**  : Ceci est utilisé par le cluster pour accéder aux API Azure.

---

### <a name="fourth-group-other-openshift-requirements"></a>QUATRIÈME GROUPE : AUTRES EXIGENCES D’OPENSHIFT

- **`mirror.openshift.com`**  : Celui-ci est requis dans l’environnement VDI ou sur votre ordinateur portable pour accéder au contenu et aux images de l’installation en miroir et requis dans le cluster pour télécharger les signatures des versions de la plateforme, utilisées par le cluster pour savoir quelles images extraire de quay.io.
- **`storage.googleapis.com/openshift-release`**  : Site alternatif pour télécharger les signatures des versions de la plateforme, utilisées par le cluster pour savoir quelles images extraire de quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (OU URL ARO EQUIVALENTE) : Lors de l’ajout de domaines à une liste d’autorisation, il est utilisé dans votre réseau d’entreprise pour atteindre les applications déployées dans OpenShift ou pour accéder à la console OpenShift.
- **`api.openshift.com`**  : Requis par le cluster pour vérifier si des mises à jour sont disponibles avant de télécharger les signatures d’image.
- **`registry.access.redhat.com`**  : L’accès au registre est requis dans votre environnement VDI ou portable pour télécharger des images de développement lors de l’utilisation de l’outil CLI ODO. (Cet outil CLI est un autre outil CLI pour les développeurs qui ne sont pas familiarisés avec kubernetes.) https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>CINQUIÈME GROUPE : SERVICE DE SUPERVISION ARO DE MICROSOFT ET RED HAT

- **`login.microsoftonline.com`**  : Il est utilisé par le cluster pour l’authentification auprès d’Azure.
- **`gcs.prod.monitoring.core.windows.net`**  : Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client.
- **`*.blob.core.windows.net`**  : Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client.
- **`*.servicebus.windows.net`**  : Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client.
- **`*.table.core.windows.net`**  : Il est utilisé pour Microsoft Geneva Monitoring afin que l’équipe ARO puisse surveiller le(s) cluster(s) du client.

## <a name="aro-integrations"></a>Intégrations ARO

### <a name="azure-monitor-for-containers"></a>Azure Monitor pour des conteneurs

Il existe deux moyens de fournir l’accès à Azure Monitor pour les conteneurs : autoriser la [balise de service](../virtual-network/service-tags-overview.md#available-service-tags) Azure Monitor **ou** fournir l’accès aux règles de nom FQDN/d’application requises.  Voici les [instructions](../azure-monitor/containers/container-insights-azure-redhat4-setup.md) à suivre pour ajouter Azure Monitor à votre cluster ARO.

#### <a name="required-network-rules"></a>Règles de réseau requises

Les noms de domaine complets/règles d’application suivantes sont requis :

| Point de terminaison de destination                                                             | Protocol | Port    | Utilisation  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [Balise de service](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureMonitor:443`**  | TCP           | 443      | Ce point de terminaison est utilisé pour envoyer les données de métriques et des journaux à Azure Monitor et à Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Règles de nom FQDN/d’application requises

Les noms de domaine complets/règles d’application suivants sont requis pour les clusters ARO avec Azure Monitor pour conteneurs :

| FQDN                                    | Port      | Utilisation      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Ce point de terminaison est utilisé pour les métriques et le monitoring des données de télémétrie à l’aide d’Azure Monitor. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Ce point de terminaison est utilisé par Azure Monitor pour l’ingestion des données Log Analytics. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Ce point de terminaison est utilisé par omsagent, qui sert à authentifier le service Log Analytics. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Ce point de terminaison est utilisé pour envoyer des données de métriques à Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Configuration d’un cluster ARO privé
L’objectif est de sécuriser un cluster ARO en acheminant le trafic de sortie via un pare-feu Azure.
### <a name="before"></a>Avant :
![Avant](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Après :
![After](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Créer un cluster ARO privé

### <a name="set-up-vars-for-your-environment"></a>Configurer VARS pour votre environnement
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Ajouter deux sous-réseaux vides à votre réseau virtuel
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Désactivez les stratégies réseau pour le service Liaison privée sur votre réseau virtuel et vos sous-réseaux. Cela est indispensable pour que le service ARO puisse accéder au cluster et le gérer.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Créer un sous-réseau de pare-feu
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Créer une machine virtuelle de type hôte de saut
### <a name="create-a-jump-subnet"></a>Créer un sous-réseau de saut
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Créer une machine virtuelle de type hôte de saut
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Créer un cluster Azure Red Hat OpenShift
### <a name="get-a-red-hat-pull-secret-optional"></a>Récupération d’un secret d’extraction Red Hat (facultatif)

Un secret d’extraction Red Hat permet au cluster d’accéder à des registres de conteneurs Red Hat ainsi qu’à d’autres contenus. Cette étape est facultative mais recommandée.

1. **[Accédez à votre portail de gestion des clusters Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) et connectez-vous.**

   Vous devrez vous connecter à votre compte Red Hat existant, ou créer un compte Red Hat avec votre adresse e-mail professionnelle et accepter les conditions générales.

2. **Cliquez sur Download pull secret (Télécharger le secret d’extraction)** .

Conservez le fichier `pull-secret.txt` enregistré à un emplacement sûr, car vous en aurez besoin lors de la création de chaque cluster.

Lors de l’exécution de la commande `az aro create`, vous pouvez référencer votre secret d’extraction à l’aide du paramètre `--pull-secret @pull-secret.txt`. Exécutez `az aro create` à partir du répertoire où vous avez stocké votre fichier `pull-secret.txt`. Sinon, remplacez `@pull-secret.txt` par `@<path-to-my-pull-secret-file`.

Si vous copiez votre secret d’extraction, ou que vous le référencez dans d’autres scripts, il doit se présenter sous la forme d’une chaîne JSON valide.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Créer un pare-feu Azure

### <a name="create-a-public-ip-address"></a>Créer une IP publique
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Mettre à jour l’installation de l’extension Pare-feu Azure
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Créer un pare-feu Azure et configurer la configuration IP
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Capturer les adresses IP de Pare-feu Azure pour une utilisation ultérieure
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Créer un UDR et une table de routage pour Pare-feu Azure
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Ajouter des règles d’application pour Pare-feu Azure
Règle pour qu’OpenShift fonctionne en fonction de cette [liste](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall) :
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Règles facultatives pour les images Docker :
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>Associer des sous-réseaux ARO au pare-feu
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Tester la configuration à partir du serveur de rebond
Ces étapes ne fonctionnent que si vous avez ajouté des règles pour les images Docker. 
### <a name="configure-the-jumpbox"></a>Configurer le serveur de rebond
Connectez-vous à une machine virtuelle de serveur de rebond et installez les utilitaires `azure-cli`, `oc-cli` et `jq`. Pour l’installation d’openshift-cli, consultez le portail client de Red Hat.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Se connecter au cluster ARO
Lister les informations d’identification du cluster :
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Obtenir un point de terminaison de serveur d’API :
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Télécharger l’interface CLI oc dans le serveur de rebond
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Connexion en utilisant `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Exécuter CentOS pour tester la connectivité extérieure
Créer un pod
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Une fois que le pod est en marche, exécutez-le et testez la connectivité extérieure.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Accéder à la console Web du cluster privé

### <a name="set-up-ssh-forwards-commands"></a>Configurer les commandes de transfert SSH

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Modifier le fichier etc.hosts sur votre ordinateur local
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Utiliser sshuttle comme autre option

[sshuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Nettoyer les ressources

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```
