---
title: Créer un serveur Ubuntu NFS (Network File System) pour une utilisation par les pods d’Azure Kubernetes Service (AKS)
description: Découvrez comment créer manuellement un volume NFS Ubuntu Linux Server pour une utilisation avec les pods dans Azure Kubernetes Service (ACS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468498"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Créer manuellement et utiliser un volume NFS (Network File System) Linux Server avec Azure Kubernetes Service (AKS)
Partage des données entre les conteneurs est souvent un composant nécessaire des applications et services basés sur le conteneur. Vous devez généralement différents pods qui ont besoin d’accéder aux mêmes informations sur un volume persistant externe.    
Quand des fichiers Azure sont une option, la création d’un serveur NFS sur une machine virtuelle Azure est une autre forme d’un stockage partagé persistant. 

Cet article vous explique comment créer un serveur NFS sur une machine virtuelle Ubuntu. Et également donner votre accès de conteneurs AKS à ce système de fichiers partagé.

## <a name="before-you-begin"></a>Avant de commencer
Cet article suppose que vous avez un Cluster ACS existant. Si vous avez besoin d’un Cluster ACS, consultez le démarrage rapide d’ACS [à l’aide de l’interface CLI] [ aks-quickstart-cli] ou [à l’aide du portail Azure][aks-quickstart-portal].

Votre Cluster AKS devrez live dans les réseaux virtuels homologués ou de mêmes que le serveur NFS. Le cluster doit être créé dans un réseau virtuel existant, ce qui peut être le même réseau virtuel que votre machine virtuelle.

Les étapes de configuration avec un réseau virtuel existant sont décrites dans la documentation : [création de Cluster AKS dans le réseau virtuel existant] [ aks-virtual-network] et [connexion de réseaux virtuels avec homologation de réseau virtuel][peer-virtual-networks]

Il suppose également que vous avez créé une Machine de virtuelle Linux Ubuntu (par exemple, 18.04 LTS). Paramètres de taille peut être à votre guise et peuvent être déployées via Azure. Pour le Guide de démarrage rapide Linux, consultez [gestion des machines virtuelles linux][linux-create].

Si vous déployez tout d’abord votre Cluster AKS, Azure sera automatiquement remplir le champ de réseau virtuel lors du déploiement de votre ordinateur Ubuntu, ce qui les rend actif dans le même réseau virtuel. Mais si vous souhaitez travailler avec des réseaux homologués au lieu de cela, consultez la documentation ci-dessus.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Déploiement du serveur NFS sur une Machine virtuelle
Voici le script pour configurer un serveur NFS au sein de votre machine de virtuelle Ubuntu :
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Le serveur redémarre (en raison du script) et vous pouvez monter le serveur NFS sur AKS

>[!IMPORTANT]  
>Veillez à remplacer le **AKS_SUBNET** avec celui qui convient à partir de votre cluster ou « * » s’ouvre votre serveur NFS à tous les ports et les connexions.

Une fois que vous avez créé votre machine virtuelle, copiez le script ci-dessus dans un fichier. Ensuite, vous pouvez la déplacer à partir de votre ordinateur local, ou partout où le script est dans la machine virtuelle à l’aide de : 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Une fois votre script dans votre machine virtuelle, vous pouvez ssh à la machine virtuelle et exécutez à l’aide de la commande :
```console
sudo ./nfs-server-setup.sh
```
Si son exécution échoue en raison d’une autorisation refusée erreur, définissez l’autorisation d’exécution par le biais de la commande :
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Cluster AKS qui se connecte dans le serveur NFS
Nous pouvons nous connecter le serveur NFS à notre cluster en approvisionnant un volume persistant et la revendication de volume persistant qui spécifie la manière d’accéder au volume.  
Il n’est pas nécessaire de connecter les deux services dans les réseaux virtuels mêmes ou homologués. Instructions relatives à la configuration du cluster dans le même réseau virtuel : [création de Cluster AKS dans le réseau virtuel existant][aks-virtual-network]

Une fois qu’ils se trouvent dans le même réseau virtuel (ou homologué), vous devez configurer qu'un volume persistant et un volume persistant de revendication dans votre Cluster AKS. Les conteneurs peuvent puis montez le lecteur NFS dans leur répertoire local.

Voici un exemple de définition de kubernetes pour le volume persistant (cette définition suppose que votre cluster et la machine virtuelle se trouvent dans le même réseau virtuel) :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Remplacez **NFS_INTERNAL_IP**, **NFS_NAME** et **NFS_EXPORT_FILE_PATH** avec les informations de serveur NFS.

Vous aurez également besoin d’un fichier de revendication de volume persistant. Voici un exemple de ce qu’il faut inclure :

>[!IMPORTANT]  
>**« storageClassName »** doit rester vide chaîne ou la revendication ne fonctionnera pas.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous ne pouvez pas vous connecter au serveur à partir d’un cluster, un problème peut être le répertoire exporté ou à son parent, ne dispose pas des autorisations suffisantes pour accéder au serveur.

Vérifiez que votre répertoire d’exportation et de son répertoire parent ont les 777 autorisations.

Vous pouvez vérifier les autorisations en exécutant la commande ci-dessous et les répertoires doivent avoir *« sont « drwxrwxrwx* autorisations :
```console
ls -l
```

## <a name="more-information"></a>Plus d’informations
Pour obtenir une procédure pas à pas complète ou pour vous aider à déboguer votre programme d’installation du serveur NFS, Voici un didacticiel approfondi :
  - [Didacticiel NFS][nfs-tutorial]

## <a name="next-steps"></a>Étapes suivantes

Pour les recommandations associées, consultez [meilleures pratiques pour le stockage et les sauvegardes dans ACS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
