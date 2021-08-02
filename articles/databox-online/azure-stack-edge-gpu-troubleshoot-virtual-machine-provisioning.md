---
title: Résolution des problèmes liés à l’approvisionnement de machines virtuelles dans le GPU Azure Stack Edge Pro | Microsoft Docs
description: Décrit comment résoudre les problèmes qui se produisent lors de l’approvisionnement d’une nouvelle machine virtuelle dans le GPU Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/04/2021
ms.author: alkohli
ms.openlocfilehash: 9913fc2e3780d9d6ab91be19913238f8002f281a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983270"
---
# <a name="troubleshoot-vm-deployment-in-azure-stack-edge-pro-gpu"></a>Résolution des problèmes liés au déploiement de machines virtuelles dans le GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment résoudre les erreurs courantes lors du déploiement de machines virtuelles sur un appareil GPU Azure Stack Edge Pro. L’article fournit une aide pour enquêter sur les causes les plus courantes qui entraînent des dépassements de délai et des problèmes d’approvisionnement de machines virtuelles lors de la création de l’interface réseau et des machines virtuelles.

Pour diagnostiquer tout échec d’approvisionnement de machines virtuelles, vous devez examiner les journaux d’invités de la machine virtuelle défaillante. <!--For steps to collect VM guest logs and include them in a Support package, see [Collect guest logs for VMs on Azure Stack Edge Pro](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).-->

Pour obtenir de l’aide sur les problèmes qui empêchent le chargement d’une image de machine virtuelle avant le déploiement de votre machine virtuelle, consultez [Dépanner les chargements d’images de machine virtuelle dans le GPU Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).


## <a name="vm-provisioning-timeout"></a>Dépassement du délai d’approvisionnement de machines virtuelles

Cette section décrit la résolution des causes les plus courantes d’un dépassement du délai d’approvisionnement de machines virtuelles.

Lorsque le délai d’approvisionnement de la machine virtuelle expire, l’erreur suivante s’affiche : 

![Capture d’écran de l’erreur affichée sur le portail Azure lorsque le délai d’approvisionnement de la machine virtuelle expire.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-provisioning-timeout-01.png) 

Les problèmes suivants sont les causes principales des dépassements du délai d’approvisionnement des machines virtuelles :
- L’adresse IP que vous avez attribuée à la machine virtuelle est déjà utilisée. [En savoir plus](#vm-provisioning-timeout)
- L’image de machine virtuelle que vous avez utilisée pour déployer la machine virtuelle n’a pas été préparée correctement. [En savoir plus](#vm-image-not-prepared-correctly)
- Impossible d’atteindre la passerelle et le serveur DNS par défaut à partir de la machine virtuelle d’invité. [En savoir plus](#gateway-dns-server-couldnt-be-reached-from-guest-vm)
- Pendant une installation `cloud init`, `cloud init` ne s’est pas exécuté ou a rencontré des problèmes lors de son exécution. (Machines virtuelles Linux uniquement) [En savoir plus](#cloud-init-issues-linux-vms)
- Pour une machine virtuelle Linux déployée à l’aide d’une image de machine virtuelle personnalisée, les indicateurs Approvisionnement dans le fichier /etc/waagent.conf ne sont pas corrects. (Machines virtuelles Linux uniquement) [En savoir plus](#provisioning-flags-set-incorrectly-linux-vms)

### <a name="ip-assigned-to-the-vm-is-already-in-use"></a>L’adresse IP attribuée à la machine virtuelle est déjà utilisée

**Description de l’erreur :** Une adresse IP statique qui est déjà utilisée a été attribuée à la machine virtuelle et l’approvisionnement de la machine virtuelle a échoué. Cette erreur se produit lorsque l’adresse IP est utilisée dans le sous-réseau sur lequel la machine virtuelle est déployée. Lorsque vous déployez une machine virtuelle via le portail Azure, le processus vérifie l’existence d’une adresse IP au sein de votre appareil, mais ne peut pas vérifier les adresses IP d’autres services ou machines virtuelles qui pourraient également se trouver sur votre sous-réseau. 

**Solution suggérée :** Utilisez une adresse IP statique qui n’est pas utilisée, ou utilisez une adresse IP dynamique fournie par le serveur DHCP.

Pour vérifier la présence d’une adresse IP en double :

- Exécutez les commandes `ping` et Test-NetConnection suivantes (`tnc`) à partir de n’importe quelle appliance du même réseau :

  ```
  ping <IP address>
  tnc <IP address>
  tnc <IP address> -CommonTCPPort “RDP”
  ```

Si vous recevez une réponse, l’adresse IP que vous avez attribuée à la nouvelle machine virtuelle est déjà utilisée.

### <a name="vm-image-not-prepared-correctly"></a>L’image de la machine virtuelle n’est pas préparée correctement

**Description de l’erreur :** Pour préparer une image de machine virtuelle afin de l’utiliser sur un appareil GPU Azure Stack Edge Pro, vous devez suivre un flux de travail spécifique. Vous devez créer une machine virtuelle de 1re génération dans Azure, personnaliser la machine virtuelle, généraliser le disque dur virtuel, puis télécharger le disque dur virtuel du système d’exploitation pour cette machine virtuelle. L’image préparée doit être un disque dur virtuel de 1re génération avec le type fixe et l’extension de nom de fichier « vhd ».

Pour obtenir une vue d’ensemble des spécifications, consultez [Créer des images de machine virtuelle personnalisées pour un appareil GPU Azure Stack Edge Pro](azure-stack-edge-gpu-create-virtual-machine-image.md). Pour obtenir de l’aide concernant la résolution des problèmes liés aux images de machine virtuelle, consultez [Résoudre les problèmes liés aux chargements d’images de machine virtuelle dans le GPU Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).  

**Solution suggérée :** Terminez le flux de travail permettant de préparer votre image de machine virtuelle. Pour obtenir de l’aide, consultez l’un des articles suivants :

* [Flux de travail d’image de machine virtuelle personnalisée pour les machines virtuelles Windows et Linux](azure-stack-edge-gpu-create-virtual-machine-image.md)
* [Préparer une image généralisée à partir d’un disque dur virtuel Windows](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
* [Préparer une image généralisée à l’aide d’un fichier ISO](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
* [Utiliser une image spécialisée pour déployer des machines virtuelles](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)


### <a name="gateway-dns-server-couldnt-be-reached-from-guest-vm"></a>Impossible d’atteindre la passerelle et le serveur DNS à partir de la machine virtuelle d’invité

**Description de l’erreur :** Si la passerelle et le serveur DNS par défaut sont impossibles à atteindre pendant le déploiement de la machine virtuelle, le délai d’approvisionnement de la machine virtuelle expirera et le déploiement de la machine virtuelle échouera.

**Solution suggérée :** Vérifiez que la passerelle et le serveur DNS par défaut sont accessibles à partir de la machine virtuelle. Ensuite, répétez le déploiement de la machine virtuelle.

Pour vérifier que la passerelle et le serveur DNS par défaut sont accessibles à partir de la machine virtuelle, procédez comme suit :
1. [Connectez-vous à la machine virtuelle](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm).
2. Exécutez les commandes suivantes :

   ```
   ping <default gateway IP address>
   ping <DNS server IP address>
   ```

   Pour connaître les adresses IP de la passerelle et des serveurs DNS par défaut, accédez à l’interface utilisateur locale de votre appareil. Sélectionnez le port qui vous intéresse, puis affichez les paramètres réseau.

   ![Capture d’écran des paramètres de la passerelle et du serveur DNS par défaut pour un port sur un appareil GPU Azure Stack Edge Pro.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gateway-dns-server-settings-01.png) 


### <a name="cloud-init-issues-linux-vms"></a>Problèmes `cloud init` (machines virtuelles Linux)

**Description de l’erreur :** `cloud init` ne s’est pas exécuté ou des problèmes sont survenus pendant l’exécution de `cloud init`. `cloud-init` est utilisé pour personnaliser une machine virtuelle Linux lors de son premier démarrage. Pour plus d’informations, consultez [Prise en charge cloud-init pour les machines virtuelles dans Azure](../virtual-machines/linux/using-cloud-init.md).

**Solutions suggérées :** Pour trouver les problèmes qui sont survenus pendant l’exécution de `cloud init` :
1. [Connectez-vous à la machine virtuelle](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm).
1. Recherchez les erreurs `cloud init` dans les fichiers journaux suivants :

   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent/log 

Pour vérifier certains des problèmes les plus courants qui empêchent `cloud init` de s’exécuter correctement, procédez comme suit :

1. Assurez-vous que l’image de machine virtuelle est basée sur `cloud init`. Exécutez la commande suivante :

   `cloud-init --version`

   La commande doit renvoyer le numéro de version de cloud init. Si l’image n’est pas basée sur `cloud init`, la commande ne renverra pas d’informations sur la version.

   Pour obtenir de l’aide sur les options de `cloud init`, exécutez la commande suivante :

   `cloud-init --help` 

2. Assurez-vous que l’instance de `cloud init` peut s’exécuter correctement avec la source de données définie sur *Azure*. 

   Lorsque la source de données est définie sur *Azure*, l’entrée dans les journaux de *cloud init* est semblable à celle qui suit.

   ![Image d’une entrée de journal de cloud-init pour une image de machine virtuelle dont la source de données est définie sur Azure.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/cloud-init-log-entry-01.png) 

   Si la source de données n’est pas définie sur Azure, vous devrez peut-être modifier votre script `cloud init`. Pour plus d’informations, consultez [Exploration approfondie de cloud-init](../virtual-machines/linux/cloud-init-deep-dive.md).


### <a name="provisioning-flags-set-incorrectly-linux-vms"></a>Les indicateurs Approvisionnement ne sont pas définis correctement (machines virtuelles Linux)

**Description de l’erreur :** Pour déployer correctement une machine virtuelle Linux dans Azure, l’approvisionnement doit être désactivé sur l’image et l’approvisionnement à l’aide de `cloud init` doit être activé. Les indicateurs Approvisionnement qui définissent ces valeurs sont configurés correctement pour les images de machine virtuelle standard. Si vous utilisez une image de machine virtuelle personnalisée, vous devez vous assurer qu’elles sont correctes. 

**Solution suggérée :** Assurez-vous que les indicateurs Approvisionnement du fichier */etc/waagent.conf* ont les valeurs suivantes :<!--Move details to "Create a custom VM image" when the 2 active PRs against that article have been merged. Not before Friday release.-->

   | Fonctionnalité                      | Valeur requise                |
   |---------------------------------|-------------------------------|
   | Activer l’approvisionnement             | `Provisioning.Enabled=n`      |
   | S’appuyer sur cloud-init pour l’approvisionnement | `Provisioning.UseCloudInit=y` |


##  <a name="network-interface-creation-issues"></a>Problèmes de création d’interfaces réseau

Cette section fournit une aide concernant les problèmes susceptibles de faire échouer la création d’une interface réseau lors du déploiement d’une machine virtuelle.


### <a name="nic-creation-timeout"></a>Dépassement du délai de création de la carte réseau

**Description de l’erreur :** La création de l’interface réseau sur la machine virtuelle ne s’est pas terminée dans le délai imparti. Cet échec peut être dû à des problèmes de serveur DHCP dans votre environnement. 

Pour vérifier si l’interface réseau a bien été créée, procédez comme suit :

1. Dans le portail Azure, accédez à la ressource Azure Stack Edge de votre appareil (accédez à **Services Edge** > **Machines virtuelles**). Sélectionnez ensuite **Déploiements**, puis accédez au déploiement de la machine virtuelle. 

1. Si une interface réseau n’a pas été créée, l’erreur suivante s’affiche.

   ![Capture d’écran de l’erreur affichée dans le portail lorsque la création d’une interface réseau échoue.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/nic-creation-failed-01.png)

**Solution suggérée :** Recréez la machine virtuelle et attribuez-lui une adresse IP statique.


## <a name="vm-creation-issues"></a>Problèmes de création de machines virtuelles

Cette section traite des problèmes courants qui surviennent lors de la création d’une machine virtuelle.

### <a name="not-enough-memory-to-create-the-vm"></a>Mémoire insuffisante pour créer la machine virtuelle

**Description de l’erreur :** Lorsque la création d’une machine virtuelle échoue en raison d’une mémoire insuffisante, l’erreur suivante s’affiche.
 
![Capture d’écran de l’erreur affichée dans le portail lorsque la création d’une machine virtuelle échoue.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-creation-failed-01.png)

**Solution suggérée :** Vérifiez la mémoire disponible sur l’appareil, puis choisissez la taille de la machine virtuelle en conséquence. Pour plus d’informations, consultez [Tailles de machines virtuelles prises en charge sur Azure Stack Edge](azure-stack-edge-gpu-virtual-machine-sizes.md).

La mémoire disponible pour le déploiement d’une machine virtuelle est restreinte par plusieurs facteurs :

- La quantité de mémoire disponible sur l’appareil. Pour plus d’informations, consultez les spécifications de calcul et de mémoire dans [Spécifications techniques du GPU Azure Stack Edge Pro](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications) et [Spécifications techniques de l’appareil Azure Stack Edge Mini R](azure-stack-edge-mini-r-technical-specifications-compliance.md#compute-memory).

- Si Kubernetes est activé, la mémoire de calcul requise pour Kubernetes et les applications sur le cluster Kubernetes.
- La surcharge de chaque machine virtuelle dans Hyper-V.

**Solutions suggérées :**

- Utilisez une taille de machine virtuelle qui nécessite moins de mémoire.
- Arrêtez toutes les machines virtuelles qui ne sont pas utilisées à partir du portail avant de déployer la nouvelle machine virtuelle.
- Supprimez toutes les machines virtuelles qui ne sont plus utilisées.


### <a name="insufficient-number-of-gpus-to-create-gpu-vm"></a>Nombre insuffisant de GPU pour créer une machine virtuelle de GPU

Si vous essayez de déployer une machine virtuelle sur un appareil GPU sur lequel Kubernetes est déjà activé, aucune GPU ne sera disponible et l’approvisionnement de la machine virtuelle échouera avec l’erreur suivante :

![Capture d’écran de l’erreur affichée dans le portail lorsque la création d’une machine virtuelle de GPU échoue en raison de l’absence de GPU disponibles.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gpu-vm-creation-failed-01.png)

**Causes possibles :** Si Kubernetes est activé avant la création de la machine virtuelle, Kubernetes utilise tous les GPU disponibles et vous ne pourrez pas créer de machines virtuelles de taille GPU. Vous pouvez créer autant de machines virtuelles de taille GPU que le nombre de GPU disponibles. Votre appareil Azure Stack Edge peut être équipé d’un ou de deux GPU.

**Solution suggérée :** Pour connaître les options de déploiement de machines virtuelles sur un appareil à un ou deux GPU avec Kubernetes configuré, consultez [Machines virtuelles GPU et Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="next-steps"></a>Étapes suivantes

<!-- Remove link while cmdlet issue is fixed. - * [Collect a Support package that includes guest logs for a failed VM](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)-->
* [Résoudre les problèmes liés à l’échec de l’installation d’une extension GPU](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
* [Résoudre les problèmes liés à Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)

