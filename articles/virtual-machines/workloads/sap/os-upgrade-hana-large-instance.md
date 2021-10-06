---
title: Mise à niveau du système d’exploitation pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Apprenez à effectuer la mise à niveau du système d’exploitation pour SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e980417c2c48455e43143fb2f870a63d6395f94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546203"
---
# <a name="operating-system-upgrade"></a>Mise à niveau du système d’exploitation
Cet article décrit en détail les mises à niveau du système d’exploitation sur les grandes instances HANA (HLI), également appelées infrastructure complète (BareMetal).

>[!NOTE]
>La mise à niveau du système d’exploitation est sous votre responsabilité. Le support des opérations Microsoft peut vous guider dans les domaines clés de la mise à niveau, mais consultez également votre fournisseur de système d’exploitation lorsque vous planifiez une mise à niveau.

Pendant l’approvisionnement de l’unité HLI, l’équipe des opérations Microsoft installe le système d’exploitation.
Vous devez mettre à jour le système d’exploitation. Par exemple, vous devez effectuer la mise à jour corrective, le réglage, la mise à niveau, etc., sur le HLI. Avant d’apporter des modifications majeures au système d’exploitation, par exemple, une mise à niveau de SP1 vers SP2, contactez l’équipe des opérations de Microsoft en ouvrant un ticket de support. Ils vous consulteront. Nous vous recommandons d’ouvrir ce ticket au moins une semaine avant la mise à niveau. 

Veuillez inclure dans votre ticket :

* Votre ID d’abonnement HLI.
* Le nom de votre serveur.
* Le niveau de correctif que vous envisagez d'appliquer.
* La date à laquelle vous envisagez cette modification. 

Pour consulter la matrice de prise en charge des différentes versions SAP HANA avec les différentes versions Linux, reportez-vous à [Remarque SAP n° 2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Problèmes connus

Il existe quelques problèmes connus avec la mise à niveau :
- Dans la référence SKU de classe II du type de référence SKU, le logiciel SFS est supprimé pendant la mise à niveau du système d’exploitation. Vous devrez réinstaller le logiciel SFS compatible une fois la mise à niveau du système d’exploitation terminée.
- Des pilotes de cartes Ethernet (ENIC et FNIC) sont restaurés vers une version plus ancienne. Vous devez réinstaller la version compatible des pilotes après la mise à niveau.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Configuration recommandée pour les grandes instances SAP HANA (type I)

La configuration du système d’exploitation peut dériver des paramètres recommandés au fil du temps. Cette dérive peut être due à des mises à jour correctives, des mises à niveau du système et d’autres modifications que vous pouvez apporter. Microsoft identifie les mises à jour nécessaires pour garantir que les grandes instances HANA sont configurés de manière optimale pour offrir les meilleurs niveaux de performance et de résilience possibles. Les instructions suivantes décrivent les suggestions relatives au niveau de performance réseau, à la stabilité du système et au niveau de performance optimal de HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Versions compatibles des pilotes eNIC/fNIC
  Pour bénéficier d’un bon niveau de performance réseau et d’une stabilité du système adéquate, il est recommandé de vérifier que la version propre au système d’exploitation des pilotes eNIC et fNIC est installée comme indiqué dans la table de compatibilité suivante. Les serveurs sont fournis aux clients avec des versions compatibles. Toutefois, les pilotes peuvent être restaurés vers les versions par défaut lors de la mise à jour corrective du système d’exploitation/noyau. Vérifiez que la version du pilote appropriée exécute des opérations de mise à jour corrective post système d’exploitation/noyau.
       
      
  |  Fournisseur du système d’exploitation    |  Version du package du système d’exploitation     |  Version du microprogramme  |  Pilote eNIC |  Pilote fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP4            |   4.1.1b           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Commandes de mise à niveau des pilotes et de nettoyage des anciens packages rpm

#### <a name="command-to-check-existing-installed-drivers"></a>Commande pour vérifier les pilotes installés existants
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Supprimer le package rpm eNIC/fNIC existant
```
rpm -e <old-rpm-package>
```
#### <a name="install-recommended-enicfnic-driver-packages"></a>Installer les packages de pilotes eNIC/fNIC recommandés
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-installation"></a>Commandes pour confirmer l’installation
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Étapes pour l’installation de pilotes eNIC/fNIC pendant la mise à niveau du système d’exploitation

* Mise à niveau de la version du système d’exploitation
* Supprimer les anciens packages rpm
* Installer les pilotes eNIC/fNIC compatibles conformément à la version installée du système d’exploitation
* Relancer le système
* Après le redémarrage, vérifiez la version d’eNIC/fNIC


### <a name="suse-hlis-grub-update-failure"></a>Échec de la mise à jour de GRUB pour les HLI SuSE
Les grandes instances SAP HANA sur Azure (type I) peuvent être dans un état qui les empêchent de démarrer après une mise à niveau. La procédure ci-dessous corrige ce problème.

#### <a name="execution-steps"></a>Étapes d’exécution

-   Exécutez la commande `multipath -ll`.
-   Récupérez l’ID du numéro d’unité logique (LUN) ou utilisez la commande : `fdisk -l | grep mapper`
-   Mettez à jour le fichier `/etc/default/grub_installdevice` avec la ligne `/dev/mapper/<LUN ID>`. Exemple :/dev/mapper/3600a09803830372f483f495242534a56

>[!NOTE]
>L’ID du numéro d’unité logique varie d’un serveur à l’autre.


### <a name="disable-error-detection-and-correction"></a>Désactiver la détection et la correction des erreurs 
   Les modules EDAC (détection et correction des erreurs) aident à détecter et à corriger les erreurs de mémoire. Toutefois, le matériel de type I HLI sous-jacent détecte et corrige déjà les erreurs de mémoire. Le fait que la même fonctionnalité soit activée au niveau du matériel et du système d’exploitation peut occasionner des conflits et entraîner des arrêts non planifiés du serveur. Nous vous recommandons de désactiver les modules EDAC à partir du système d’exploitation.

#### <a name="execution-steps"></a>Étapes d’exécution

- Vérifiez si les modules EDAC sont activés. Si une sortie est retournée à partir de la commande suivante, les modules sont activés.

```
lsmod | grep -i edac 
```
- Désactivez les modules en ajoutant les lignes suivantes au fichier `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Un redémarrage est nécessaire pour que les modifications prennent effet. Après le redémarrage, réexécutez la commande `lsmod` et vérifiez que les modules ne sont pas activés.

### <a name="kernel-parameters"></a>Paramètres de noyau
Vérifiez que les paramètres pour `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` et `intel_idle.max_cstate` sont correctement appliqués.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>Étapes d’exécution

- Ajoutez ces paramètres à la ligne `GRB_CMDLINE_LINUX` du fichier `/etc/default/grub` :

```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
- Créez un nouveau fichier grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
- Redémarrer votre système.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer le serveur SMT pour SUSE Linux.

> [!div class="nextstepaction"]
> [Configuration de serveur SMT pour SUSE Linux](hana-setup-smt.md)