---
title: Dépannage – Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article contient des conseils de dépannage concernant Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 35d494702673d59290a0073c55135138f533b8bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956694"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guide de rési=olution des problèmes Azure Disk Encryption

Ce guide s’adresse aux informaticiens professionnels, aux analystes de la sécurité des informations et aux administrateurs de cloud dont les organisations utilisent Azure Disk Encryption. Cet article est destiné à vous aider à résoudre les problèmes liés au chiffrement des disques.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Résolution des problèmes de chiffrement de disque de système d’exploitation Linux

Pour chiffrer le disque du système d’exploitation (SE) Linux, il faut démonter le lecteur du système d’exploitation avant l’exécution du processus de chiffrement de disque complet. Si le service ne peut pas démonter le lecteur, un message d’erreur du type « Échec du démontage après... » apparaît généralement.

Cette erreur peut se produire lors d’une tentative de chiffrement de disque dans un environnement de machines virtuelles cible, quand l’image de ce dernier a été modifiée par rapport à celle de la galerie d’images stock (officielles) prises en charge. Les écarts de l’image prise en charge peuvent interférer avec la capacité de l’extension à démonter le lecteur du système d’exploitation. Les exemples d’écarts peuvent inclure les éléments suivants :
- Images personnalisées qui ne correspondent plus au système de fichiers ou au schéma de partitionnement pris en charge.
- Les applications volumineuses, telles que SAP, MongoDB, Apache Cassandra et Docker, ne sont pas prises en charge quand elles sont installées et exécutées dans le système d’exploitation avant le chiffrement. Azure Disk Encryption ne peut pas arrêter en toute sécurité ces processus, comme cela est nécessaire pour la préparation du lecteur du système d’exploitation pour le chiffrement du disque. S’il existe toujours des processus actifs détenant des handles de fichier ouverts sur le lecteur du système d’exploitation, ce lecteur ne peut pas être démonté, ce qui entraîne l’échec du chiffrement du lecteur du système d’exploitation. 
- Les scripts personnalisés qui s’exécutent au moment de l’activation du chiffrement ou en cas de modification apportée sur la machine virtuelle pendant le processus de chiffrement. Ce conflit peut se produire lorsqu’un modèle Azure Resource Manager définit plusieurs extensions à exécuter simultanément ou lorsqu’une extension de script personnalisé ou une autre action est exécutée en même temps que le chiffrement de disque. L’exécution de ces étapes de manière sérialisée et isolée peut résoudre le problème.
- Comme Security Enhanced Linux (SELinux) n’a pas été désactivé avant l’activation du chiffrement, l’étape de démontage échoue. SELinux peut être réactivé à la fin du chiffrement.
- Le disque du système d’exploitation utilise un schéma de Gestionnaire de volume logique (LVM). Même si une prise en charge limitée des disques de données LVM existe, ce n’est pas le cas pour le disque du système d’exploitation LVM.
- Les configurations requises en matière de capacité de mémoire minimale ne sont pas remplies (7 Go sont recommandés pour le chiffrement de disque de système d’exploitation).
- Les lecteurs de données ont été montés de manière récursive sous le répertoire /mnt/ ou les uns sous les autres (par exemple /mnt/data1, /mnt/data2, /data3 + /data3/data4, etc.).
- D’autres [conditions préalables](azure-security-disk-encryption-prerequisites.md) pour Azure Disk Encryption pour Linux ne sont pas remplies.

## <a name="bkmk_Ubuntu14"></a> Mettre à jour le noyau par défaut pour Ubuntu 14.04 LTS

L’image Ubuntu 14.04 LTS est fournie avec une version de noyau par défaut de 4.4. Cette version de noyau présente un problème connu : le mécanisme Out of Memory Killer met fin de façon inappropriée à la commande dd lors du processus de chiffrement du système d’exploitation. Ce bogue a été corrigé dans le noyau Linux réglé sur Azure le plus récent. Pour éviter cette erreur, avant d’activer le chiffrement sur l’image, effectuez une mise à jour sur le [noyau réglé sur Azure 4.15](https://packages.ubuntu.com/trusty/linux-azure) ou version ultérieure à l’aide des commandes suivantes :

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Après le redémarrage de la machine virtuelle dans le nouveau noyau, la nouvelle version du noyau peut être confirmée à l’aide de :

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Mettre à jour l’agent de machine virtuelle Azure et les versions d’extension

Les opérations Azure Disk Encryption peuvent échouer sur les images de machine virtuelle avec des versions non prises en charge de l’agent de machine virtuelle Azure. Les images Linux avec des versions d’agent antérieures à la version 2.2.38 doivent être mises à jour avant l’activation du chiffrement. Pour plus d’informations, consultez [Guide pratique pour mettre à jour l’agent Linux Azure sur une machine virtuelle](../virtual-machines/extensions/update-linux-agent.md) et [Prise en charge de version minimale pour les agents de machine virtuelle dans Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

La version correcte de l’extension de l’agent invité Microsoft.Azure.Security.AzureDiskEncryption ou Microsoft.Azure.Security.AzureDiskEncryptionForLinux est également requise. Les versions d’extension sont conservées et mises à jour automatiquement par la plateforme lorsque la configuration requise de l’agent de machine virtuelle Azure est satisfaite et qu’une version prise en charge de l’agent de machine virtuelle est utilisée.

L’extension Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux est déconseillée et n’est plus prise en charge.  

## <a name="unable-to-encrypt-linux-disks"></a>Impossible de chiffrer des disques Linux

Dans certains cas, le chiffrement de disque Linux semble être bloqué à l’étape « OS disk encryption started » (le chiffrement du disque du système d’exploitation a démarré) et SSH est désactivé. Ce processus de chiffrement peut prendre entre 3 et 16 heures pour se terminer sur une image de galerie de stock. Si des disques de données de plusieurs To sont ajoutés, le processus peut prendre des jours.

La séquence de chiffrement de disque du système d’exploitation Linux démonte le lecteur du système d’exploitation temporairement. Il effectue ensuite un chiffrement bloc par bloc de la totalité du disque du système d’exploitation avant de le remonter dans son état chiffré. Contrairement à Azure Disk Encryption sur Windows, le chiffrement de disque Linux n’autorise pas l’utilisation simultanée de la machine virtuelle pendant le chiffrement. Les caractéristiques de performances de la machine virtuelle peuvent faire la différence de façon significative dans le temps nécessaire pour exécuter le chiffrement. Ces caractéristiques incluent la taille du disque et si le compte de stockage est standard ou premium (SSD).

Pour vérifier l’état du chiffrement, vous pouvez interroger le champ **ProgressMessage** renvoyé à partir de la commande [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus). Pendant le chiffrement du lecteur du système d’exploitation, la machine virtuelle passe en état de maintenance et SSH est également désactivé pour éviter toute interruption du processus en cours. Les rapports de message **EncryptionInProgress** pour la majorité du temps d’exécution du chiffrement. Plusieurs heures plus tard, un message **VMRestartPending** vous invite à redémarrer la machine virtuelle. Par exemple :


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Une fois que vous êtes invité à redémarrer la machine virtuelle et après le redémarrage de la machine virtuelle, vous devez attendre 2 à 3 minutes pour le redémarrage et l’exécution des étapes finales sur la cible. Le message d’état est modifié lorsque le chiffrement est enfin terminé. Lorsque ce message s’affiche, le lecteur de système d’exploitation chiffré est prêt à être utilisé et la machine virtuelle est de nouveau disponible.

Dans les cas suivants, nous vous recommandons de restaurer la machine virtuelle à la capture instantanée ou à la sauvegarde effectuée immédiatement avant le chiffrement :
   - Si la séquence de redémarrage décrite précédemment ne se produit pas.
   - Si les informations de démarrage, message de progression ou autres indicateurs d’erreur signalent que le chiffrement du système d’exploitation a échoué au milieu de ce processus. Le message peut-être par exemple l’erreur « Échec du démontage » qui est décrite dans ce guide.

Avant d’effectuer une autre tentative, il est recommandé de réévaluer les caractéristiques de la machine virtuelle et de vous assurer que toutes les conditions préalables sont remplies.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Résolution des problèmes Azure Disk Encryption derrière un pare-feu
Lorsque la connectivité est limitée par un pare-feu, une exigence de proxy ou des paramètres de groupe de sécurité réseau (NSG), cela peut interrompre la capacité de l’extension à effectuer les tâches nécessaires. Cette interruption peut entraîner les messages d’état de type « État de l’extension non disponible sur la machine virtuelle ». Dans les scénarios prévus, le processus de chiffrement échoue. Les sections qui suivent décrivent certains problèmes courants au niveau du pare-feu qui valent la peine d’être examinés.

### <a name="network-security-groups"></a>Groupes de sécurité réseau
Les paramètres de groupe de sécurité réseau appliqués doivent permettre au point de terminaison de remplir les [conditions requises](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) de configuration réseau indiquées pour le chiffrement de disque.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault derrière un pare-feu

Lorsque le chiffrement est activé avec des [informations d’identification Azure AD](azure-security-disk-encryption-prerequisites-aad.md), la machine virtuelle cible doit autoriser la connectivité aux points de terminaison Azure Active Directory et à ceux de Key Vault. Les points de terminaison d’authentification Azure Active Directory actuels sont gérés dans les sections 56 et 59 de la documentation [URL et plages d’adresses IP Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges). Des instructions relatives à Key Vault sont fournies dans la documentation sur l’[accès à Azure Key Vault derrière un pare-feu](../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Service de métadonnées d’instance Azure 
La machine virtuelle doit pouvoir accéder au point de terminaison [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) qui utilise une adresse IP non routable bien connue (`169.254.169.254`), accessible uniquement à partir de la machine virtuelle.  Les configurations de proxy qui modifient le trafic HTTP local à cette adresse (par exemple, en ajoutant un en-tête X-Forwarded-For) ne sont pas prises en charge.

### <a name="linux-package-management-behind-a-firewall"></a>Gestion des packages Linux derrière un pare-feu

Au moment de l’exécution, Azure Disk Encryption pour Linux s’appuie sur le système de gestion des packages de la distribution cible pour installer les composants prérequis nécessaires avant l’activation du chiffrement. Si les paramètres de pare-feu empêchent la machine virtuelle de télécharger et d’installer ces composants, des échecs sont à prévoir. Les étapes de configuration de ce système de gestion des packages peuvent varier selon la distribution. Sous Red Hat, lorsqu’un proxy est requis, vous devez absolument vous assurer que le gestionnaire d’abonnements et yum sont configurés correctement. Pour plus d’informations, consultez [Guide pratique pour résoudre les problèmes du gestionnaire d’abonnement et yum](https://access.redhat.com/solutions/189533).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Résolution des problèmes Windows Server 2016 Server Core

Sur Windows Server 2016 Server Core, le composant bdehdcfg n’est pas disponible par défaut. Ce composant est requis par Azure Disk Encryption. Il est utilisé pour séparer le volume système du volume du système d’exploitation, ce qui est effectué une seule fois pendant toute la durée de vie de la machine virtuelle. Ces fichiers binaires ne sont pas nécessaires lors des opérations de chiffrement ultérieures.

Pour contourner ce problème, copiez les quatre fichiers suivants à partir d’une machine virtuelle Windows Server 2016 Data Center vers le même emplacement sur Server Core :

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Entrez la commande suivante :

   ```
   bdehdcfg.exe -target default
   ```

1. Cette commande crée une partition de système de 550 Mo. Redémarrez le système.

1. Utilisez DiskPart pour vérifier les volumes, avant de continuer.  

Par exemple :

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>Résolution des problèmes de l’état de chiffrement 

Le portail peut afficher un disque sous forme chiffrée, même après qu’il a été déchiffré au sein de la machine virtuelle.  Cela peut se produire lorsque des commandes de bas niveau sont utilisées pour déchiffrer directement le disque à partir de la machine virtuelle, au lieu des commandes de gestion Azure Disk Encryption de niveau supérieur.  Les commandes de niveau supérieur déchiffrent le disque à partir de la machine virtuelle. Toutefois, en dehors de la machine virtuelle, elles mettent aussi à jour les paramètres importants de chiffrement et d’extension au niveau de la plateforme, qui sont associés à la machine virtuelle.  S’ils ne sont pas alignés, la plateforme ne sera pas en mesure de rapporter l’état de chiffrement ou de configurer la machine virtuelle.   

Pour désactiver Azure Disk Encryption avec PowerShell, utilisez [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), puis [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). L’exécution de Remove-AzVMDiskEncryptionExtension en avant la désactivation du chiffrement échouera.

Pour désactiver Azure Disk Encryption avec l’interface CLI, utilisez [az vm encryption disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a fait découvrir certains problèmes couramment rencontrés dans Azure Disk Encryption et en a décrit la résolution. Pour plus d’informations sur ce service et ses fonctionnalités, consultez les articles suivants :

- [Appliquer le chiffrement de disque dans Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Chiffrement des données au repos Azure](azure-security-encryption-atrest.md)
