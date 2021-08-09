---
title: Résoudre les problèmes d’extension GPU des machines virtuelles GPU sur Azure Stack Edge Pro avec GPU
description: Décrit comment résoudre les problèmes d’installation de l’extension GPU pour les machines virtuelles GPU sur Azure Stack Edge Pro avec GPU.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/02/2021
ms.author: alkohli
ms.openlocfilehash: 256902ef26328050b4ed52053f465571974ffefe
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111422153"
---
# <a name="troubleshoot-gpu-extension-issues-for-gpu-vms-on-azure-stack-edge-pro-gpu"></a>Résoudre les problèmes d’extension GPU des machines virtuelles GPU sur Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-gpu-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Cet article fournit des conseils permettant de résoudre les problèmes les plus courants qui se soldent par l’échec de l’installation de l’extension GPU sur une machine virtuelle GPU d’un appareil Azure Stack Edge Pro avec GPU.

Pour connaître les étapes d’installation, consultez [Installer l’extension GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).

## <a name="vm-size-is-not-gpu-vm-size"></a>La taille de la machine virtuelle n’est pas une taille de machine virtuelle GPU

**Description de l’erreur :** la taille d’une machine virtuelle GPU doit être Standard_NC4as_T4_v3 ou Standard_NC8as_T4_v3. Si une autre taille de machine virtuelle est utilisée, l’attachement de l’extension GPU échoue.

**Solution suggérée :** créez une machine virtuelle de la taille Standard_NC4as_T4_v3 ou Standard_NC8as_T4_v3. Pour plus d’informations, consultez [Tailles de machine virtuelle prises en charge pour les machines virtuelles GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). Pour plus d’informations sur la spécification de la taille, consultez [Créer des machines virtuelles GPU](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).


## <a name="image-os-is-not-supported"></a>Le système d’exploitation de l’image n’est pas pris en charge

**Description de l’erreur :** l’extension GPU ne prend pas en charge le système d’exploitation installé sur l’image de machine virtuelle. 

**Solution suggérée :** préparez une nouvelle image de machine virtuelle dotée d’un système d’exploitation pris en charge par l’extension GPU. 

* Pour obtenir la liste des systèmes d’exploitation pris en charge, consultez [Système d’exploitation et pilotes GPU pris en charge pour les machines virtuelles GPU](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#supported-os-and-gpu-drivers).

* Pour connaître les conditions requises de la préparation de l’image d’une machine virtuelle GPU, consultez [Créer des machines virtuelles GPU](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).


## <a name="extension-parameter-is-incorrect"></a>Le paramètre de l’extension est incorrect

**Description de l’erreur :** des paramètres d’extension incorrects ont été utilisés lors du déploiement de l’extension GPU sur une machine virtuelle Linux. 

**Solution suggérée :** modifiez le fichier de paramètres avant de déployer l’extension GPU. Pour plus d’informations, consultez [Installer l’extension GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).


## <a name="vm-extension-installation-failed-in-downloading-package"></a>Échec de l’installation de l’extension de machine virtuelle lors du téléchargement du package

**Description de l’erreur :** échec du provisionnement de l’extension lors de l’installation de l’extension ou pendant l’état d’activation.

1. Recherchez l’erreur associée dans le journal de l’invité. <!--To collect the guest logs, see [Collect guest logs for VMs on an Azure Stack Edge Pro](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).-->

   Sur une machine virtuelle Linux :
   * Regardez dans `/var/log/waagent.log` ou `/var/log/azure/nvidia-vmext-status`.

   Sur une machine virtuelle Windows :
   * Déterminez l’état d’erreur dans `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`.
   * Examinez le journal d’exécution complet : `C:\WindowsAzure\Logs\WaAppAgent.txt`.

   Si l’installation a échoué lors du téléchargement du package, cette erreur indique que la machine virtuelle n’a pas pu accéder au réseau public pour télécharger le pilote.

**Solution suggérée :**

1.  Activez le calcul sur un port connecté à Internet. Pour obtenir de l’aide, consultez [Créer des machines virtuelles GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).

1.  Libérez la machine virtuelle en arrêtant la machine virtuelle dans le portail. Pour arrêter la machine virtuelle, accédez à **Machines virtuelles** > **Vue d’ensemble**, puis sélectionnez la machine virtuelle. Ensuite, dans la page Propriétés de la machine virtuelle, sélectionnez **Arrêter**.<!--Follow-up (formatting): Create an include file for stopping a VM. Use it here and in prerequisites for "Use the Azure portal to manage network interfaces on the VMs" (https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal#prerequisites).-->
 
1.  Créez une machine virtuelle.


## <a name="vm-extension-failed-with-error-dpkg-is-usedyum-lock-is-used-linux-vm"></a>Échec de l’extension de machine virtuelle avec une erreur `dpkg is used/yum lock is used` (machine virtuelle Linux)

**Description de l’erreur :** échec du déploiement de l’extension GPU sur une machine virtuelle Linux du fait qu’un autre processus a utilisé `dpkg` ou qu’un autre processus a créé un `yum lock`. 

**Solution suggérée :** pour résoudre le problème, suivez ces étapes :

1.  Pour savoir quel processus applique le verrou, recherchez dans le journal \var\log\azure\nvidia-vmext-status une erreur, telle que « dpkg est utilisé par un autre processus » ou « une autre application détient yum Lock ».

1. Attendez que le processus se termine ou mettez fin au processus.

1.  [Installez l’extension GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux) à nouveau.

1.  Si le déploiement de l’extension échoue à nouveau, créez une machine virtuelle et assurez-vous que le verrou n’est pas présent avant d’installer l’extension GPU.


## <a name="next-steps"></a>Étapes suivantes

- [Installer l’extension GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)<!--Temporary link until next one can be restored.-->
<!-- Remove link while cmdlet is fixed. - [Collect guest logs, and create a Support package](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)-->
