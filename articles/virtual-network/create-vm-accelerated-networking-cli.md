---
title: Créer une machine virtuelle Azure avec mise en réseau accélérée| Documents Microsoft
description: Découvrez comment créer une machine virtuelle Linux avec la mise en réseau accélérée activée.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 8c913d618313a72f6fb05ea45847a220f6070d42
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765736"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Créer une machine virtuelle Linux avec mise en réseau accélérée

Ce didacticiel explique comment créer une machine virtuelle Linux avec mise en réseau accélérée. Pour créer une machine virtuelle Windows avec accélération réseau, consultez [Créer une machine virtuelle Windows avec accélération réseau](create-vm-accelerated-networking-powershell.md). Une mise en réseau accélérée permet d’opérer une virtualisation d’E/S d’une racine unique (SR-IOV) sur une machine virtuelle, ce qui améliore considérablement les performances de mise en réseau. Cette voie hautement performante court-circuite l’hôte à partir du chemin d’accès aux données, réduisant ainsi la latence, l’instabilité et l’utilisation du processeur pour servir les charges de travail réseau les plus exigeantes sur les types de machines virtuelles pris en charge. L’illustration suivante montre la communication entre deux machines virtuelles avec ou sans mise en réseau accélérée :

![Opérateurs de comparaison](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sans mise en réseau accélérée, tout le trafic réseau en direction et en provenance de la machine virtuelle doit transiter par l’hôte et le commutateur virtuel. Le commutateur virtuel fournit au trafic réseau toutes les stratégies, telles que les groupes de sécurité réseau, les listes de contrôle d’accès, l’isolation et d’autres services de réseau virtualisé. Pour plus d’informations sur les commutateurs virtuels, voir [Vue d’ensemble de la virtualisation de réseau Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Dans le cas d'une mise en réseau accélérée, le trafic réseau parvient à la carte réseau de la machine virtuelle avant d'être transféré vers celle-ci. Toutes les stratégies réseau que le commutateur virtuel applique sont déchargées et appliquées dans le matériel. L’application de la stratégie au niveau du matériel permet à la carte réseau de transférer le trafic directement à la machine virtuelle, en ignorant l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu’il a appliquées dans l’hôte.

Les avantages d’une mise en réseau accélérée s’appliquent uniquement à la machine virtuelle activée. Pour des résultats optimaux, il convient d'activer cette fonctionnalité sur au moins deux machines virtuelles connectées au même réseau virtuel Azure. Lors de la communication entre réseaux virtuels ou d’une connexion locale, cette fonctionnalité a une incidence minimale sur la latence globale.

## <a name="benefits"></a>Avantages
* **Latence plus faible/Nombre supérieur de paquets par seconde (pps) :** la suppression du commutateur virtuel du chemin de données évite que les paquets liés au traitement des stratégies séjournent sur l'hôte, ce qui augmente le nombre de paquets pouvant être traités dans la machine virtuelle.
* **Instabilité réduite :** le traitement du commutateur virtuel dépend de la stratégie à appliquer et de la charge de travail du processeur qui effectue le traitement. Le déchargement des stratégies sur le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, en supprimant l’hôte dans la communication de la machine virtuelle et toutes les interruptions de logiciel et les changements de contexte.
* **Utilisation réduite du processeur :** en contournant le commutateur virtuel de l'hôte, le processeur utilise moins de ressources pour traiter le trafic réseau.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les distributions suivantes sont prises en charge sans configuration supplémentaire à partir de la galerie Azure : 
* **Ubuntu 16.04+** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian « Stretch » with backports kernel**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Limitations et restrictions

### <a name="supported-vm-instances"></a>Instances de machines virtuelles prises en charge
La mise en réseau accélérée est prise en charge dans la plupart des instances d’usage général et optimisées pour le calcul (2 processeurs virtuels ou plus).  Séries prises en charge : D/DSv2 et F/Fs

Dans des instances qui acceptent l’hyperthreading, la mise en réseau accélérée est prise en charge dans des instances de machine virtuelle comptant au minimum 4 processeurs virtuels. Séries prises en charge : D/DSv3, E/ESv3, Fsv2 et Ms/Mms.

Pour plus d’informations sur les instances de machine virtuelle, consultez la section [Tailles des machines virtuelles Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Régions
Disponible dans toutes les régions Azure publiques ainsi que dans les clouds Azure Government.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Activation de la mise en réseau accélérée sur une machine virtuelle en cours d’exécution
Cette fonctionnalité peut être activée sur une machine virtuelle sans mise en réseau accélérée uniquement si la machine virtuelle a une taille prise en charge et si elle est arrêtée/libérée.  
### <a name="deployment-through-azure-resource-manager"></a>Déploiement par le biais d’Azure Resource Manager
Aucun déploiement des machines virtuelles (classiques) n’est possible avec la mise en réseau accélérée.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Créer une machine virtuelle Linux avec mise en réseau accélérée Azure

Bien que cet article fournit des étapes pour créer une machine virtuelle avec mise en réseau accélérée à l’aide de l’interface CLI d’Azure, vous pouvez également [Créer une machine virtuelle avec mise en réseau accélérée via le portail Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Au moment de la création d’une machine virtuelle dans le portail, sous **Paramètres**, sélectionnez **Activé** sous **Mise en réseau accélérée**. L’option permettant d’activer la mise en réseau accélérée n’apparaît pas dans le portail, sauf si vous avez sélectionné un [système d’exploitation pris en charge](#supported-operating-systems) et une [taille de machine virtuelle](#supported-vm-instances). Une fois la machine virtuelle créée, vous devez suivre les instructions de [Confirmer l’activation de la mise en réseau accélérée](#confirm-that-accelerated-networking-is-enabled).

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Installez la dernière version [d’Azure CLI](/cli/azure/install-azure-cli) et connectez-vous à un compte Azure avec [az login](/cli/azure/reference-index). Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myNic* et *myVm*.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *centralus* :

```azurecli
az group create --name myResourceGroup --location centralus
```

Sélectionnez une région Linux prise en charge et répertoriée dans [Mise en réseau accélérée Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau
Créez un groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg). L’exemple suivant crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Le groupe de sécurité réseau contient plusieurs règles par défaut, dont l’une désactive tous les accès entrants à partir d’Internet. Ouvrez un port pour autoriser l’accès SSH à la machine virtuelle avec [az network nsg rule create](/cli/azure/network/nsg/rule) :

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Créer une interface réseau avec mise en réseau accélérée

Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip). Une adresse IP publique n’est pas nécessaire si vous ne prévoyez pas d’accéder à l’ordinateur virtuel depuis Internet. Par contre, elle l’est pour procéder selon les étapes mentionnées dans cet article.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Créez une interface réseau avec [az network nic create](/cli/azure/network/nic), puis activez la mise en réseau accélérée. L’exemple suivant permet de créer une interface réseau nommée *myNic* dans le sous-réseau *mySubnet* du réseau virtuel *myVnet* et de lui associer le groupe de sécurité réseau *myNetworkSecurityGroup* :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Créer une machine virtuelle et attacher la carte réseau
Lorsque vous créez la machine virtuelle, spécifiez la carte réseau que vous avez générée avec `--nics`. Sélectionnez une taille et une distribution répertoriées dans [Mise en réseau accélérée Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm). L’exemple suivant crée une machine virtuelle nommée *myVM* avec l’image UbuntuLTS et une taille qui prend en charge la mise en réseau accélérée (*Standard_DS4_v2*) :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Pour obtenir la liste de toutes les tailles de machine virtuelle et leurs caractéristiques, consultez [Tailles de machines virtuelles Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Une fois que la machine virtuelle est créée, une sortie similaire à la sortie suivante est renvoyée. Veuillez noter **publicIpAddress**. Cette adresse sera utilisée pour accéder à la machine virtuelle dans les étapes suivantes.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Confirmer l’activation de la mise en réseau accélérée

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Remplacez `<your-public-ip-address>` par l’adresse IP publique affectée à la machine virtuelle que vous avez créée, puis remplacez *azureuser* si vous avez utilisé une valeur différente pour `--admin-username` au moment de la création de la machine virtuelle.

```bash
ssh azureuser@<your-public-ip-address>
```

À partir de l’interpréteur de commandes Bash, entrez `uname -r`, puis confirmez que la version du noyau est bien l’une des versions suivantes ou une version supérieure :

* **Ubuntu 16.04** : 4.11.0-1013
* **SLES SP3** : 4.4.92-6.18
* **RHEL** : 7.4.2017120423
* **CentOS** : 7.4.20171206


Utilisez la commande `lspci` pour confirmer que l’appareil Mellanox VF est exposé à la machine virtuelle. Le résultat renvoyé ressemble à la sortie suivante :

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Utilisez la commande `ethtool -S eth0 | grep vf_` pour rechercher l’activité sur VF (fonction virtuelle). Si vous recevez une sortie semblable à l’exemple suivant, la mise en réseau accélérée est donc activée et elle fonctionne.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
La mise en réseau accélérée est maintenant activée pour votre machine virtuelle.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Activer l’accélération réseau sur des machines virtuelles existantes
Si vous avez créé une machine virtuelle sans mise en réseau accélérée, vous pouvez activer cette fonctionnalité sur une machine virtuelle existante.  La machine virtuelle doit prendre en charge la mise en réseau accélérée et remplir les prérequis suivants (ces prérequis ont déjà été décrits plus haut) :

* La machine virtuelle doit avoir une taille prise en charge pour la mise en réseau accélérée
* La machine virtuelle doit être une image de la galerie Azure prise en charge (et une version de noyau prise en charge pour Linux)
* Toutes les machines virtuelles membres d’un groupe à haute disponibilité ou d’un groupe de machines virtuelles identiques doivent être arrêtées ou libérées avant l’activation de la mise en réseau accélérée sur une carte réseau

### <a name="individual-vms--vms-in-an-availability-set"></a>Machines virtuelles individuelles et machines virtuelles d’un groupe à haute disponibilité
Tout d’abord, arrêtez/libérez la machine virtuelle individuelle ou, dans le cas d’un groupe à haute disponibilité, toutes les machines virtuelles du groupe :

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Important : Si la machine virtuelle a été créée individuellement, en dehors d’un groupe à haute disponibilité, arrêtez/libérez simplement la machine virtuelle individuelle pour activer la mise en réseau accélérée.  Si la machine virtuelle a été créée dans un groupe à haute disponibilité, vous devez arrêter/libérer toutes les machines virtuelles membres de ce groupe avant d’activer la mise en réseau accélérée sur les cartes réseau. 

Après avoir arrêté la machine virtuelle, activez la mise en réseau accélérée sur la carte réseau de la machine virtuelle :

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Redémarrez la machine virtuelle ou, dans le cas d’un groupe à haute disponibilité, toutes les machines virtuelles du groupe, puis vérifiez que la mise en réseau accélérée est bien activée : 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>Groupe de machines virtuelles identiques (VMSS)
Un groupe de machines virtuelles identiques fonctionne un peu différemment, mais suit le même workflow.  Tout d’abord, arrêtez les machines virtuelles :

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Une fois que les machines virtuelles sont arrêtées, mettez à jour la propriété Accelerated Networking sur l’interface réseau :

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Notez que, dans un groupe de machines virtuelles identiques, les machines virtuelles sont mises à niveau selon trois méthodes de mise à jour différentes : manuelle, automatique et propagée.  Dans ces instructions, la stratégie est définie sur automatique afin que le groupe de machines virtuelles identiques récupère les mises à jour aussitôt après le redémarrage.  Pour définir la stratégie sur automatique afin que les mises à jour soient immédiatement récupérées : 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Enfin, redémarrez le groupe de machines virtuelles identiques :

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Après redémarrage, attendez que les mises à niveau se terminent, mais une fois cela fait, la fonction virtuelle s’affiche à l’intérieur de la machine virtuelle.  (Vérifiez que vous utilisez un système d’exploitation et des tailles de machine virtuelle pris en charge.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Redimensionnement des machines virtuelles existantes avec mise en réseau accélérée

Les machines virtuelles avec mise en réseau accélérée peuvent être redimensionnées uniquement en machines virtuelles qui prennent en charge la mise en réseau accélérée.  

Une machine virtuelle avec mise en réseau accélérée ne peut pas être redimensionnée en une instance de machine virtuelle qui ne prend pas en charge la mise en réseau accélérée.  Pour redimensionner l’une de ces machines virtuelles, vous devez effectuer ces opérations : 

* Arrêtez/libérez la machine virtuelle ou, dans le cas d’un groupe à haute disponibilité ou d’un groupe de machines virtuelles identiques, arrêtez/libérez toutes les machines virtuelles du groupe.
* Désactivez la mise en réseau accélérée sur la carte réseau de la machine virtuelle ou, dans le cas d’un groupe à haute disponibilité ou d’un groupe de machines virtuelles identiques, de toutes les machines virtuelles du groupe.
* Une fois que la mise en réseau accélérée est désactivée, redimensionnez la machine virtuelle, ou toutes les machines virtuelles du groupe à haute disponibilité ou du groupe de machines virtuelles identiques, à une nouvelle taille qui ne prend pas en charge la mise en réseau accélérée, et redémarrez la ou les machines.  

