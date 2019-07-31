---
title: Préparer des machines virtuelles VMware exécutant Linux pour la migration vers Azure avec la migration de serveur Azure Migrate | Microsoft Docs
description: Décrit comment préparer une machine virtuelle Linux pour la migration vers Azure avec la migration de serveur Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810216"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Préparer les machines virtuelles VMware pour les migrer vers Azure 

Cet article explique comment préparer des machines virtuelles VMware exécutant Linux lorsque vous souhaitez les migrer vers Azure à l’aide de [Azure Migrate](migrate-overview.md). 

> [!NOTE]
> La migration de serveur Azure Migrate est actuellement en préversion publique. Vous pouvez utiliser la version mise à la disposition générale existante de Azure Migrate pour découvrir et évaluer des machines virtuelles pour la migration, mais la migration réelle n’est pas prise en charge par cette version.

Préparez les machines Linux comme suit :

1. Installez le service d’intégration pour Hyper-V Linux. (Les versions les plus récentes des distributions Linux peuvent l’avoir installé par défaut).
2. Régénérer l’image de l’initialisation de Linux pour qu’elle contienne les pilotes Hyper-V nécessaires et que la machine virtuelle démarre dans Azure (nécessaire pour certaines distributions).
3. Activez la journalisation de la console série pour la résolution des problèmes. [Plus d’informations](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)
4. Mettez à jour le fichier de mappage des appareils avec le nom de l’appareil pour les associations de volume afin d’utiliser des identificateurs d’appareil permanents.
5. Mettez à jour les entrées fstab pour utiliser des identificateurs de volume persistant.
6. Supprimez toutes les règles udev qui réservent des noms d’interface basés sur l’adresse MAC, etc.
7. Mettez à jour les interfaces réseau pour recevoir des adresses IP DHCP.
8. Assurez-vous que le protocole ssh est activé. Vérifiez que le service sshd est configuré pour démarrer automatiquement au redémarrage.
9. Vérifiez que les requêtes de connexion ssh entrantes ne sont pas bloquées par le pare-feu du système d’exploitation ou par les règles de table IP.

[En savoir plus](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) sur ces changements sur les distributions Linux les plus populaires.

## <a name="sample-script"></a>Exemple de script

Ce script (prepare-for-azure.sh) fournit un exemple pour la préparation des machines Linux. Le script peut ne pas fonctionner pour l’ensemble des distributions et des environnements, mais c’est un point de départ utile.

Le script montre comment : 

- Régénérer l’image de l’initialisation de Linux avec les pilotes nécessaires, si besoin.
- Mettre à jour les entrées fstab pour utiliser des identificateurs de volume persistant.
- Rediriger les journaux de la console vers le port série.
- Activer la console d’accès série Azure.
- Supprimer les règles udev net.
- Injecter une exécution sur le script de démarrage qui s’exécute au démarrage de la machine virtuelle. Il vérifie si l’ordinateur est en cours d’exécution dans Azure. Si c’est le cas, il met à jour la configuration réseau sur la machine virtuelle et définit la première interface Ethernet pour qu’elle utilise DHCP pour obtenir une adresse IP.

### <a name="before-you-start"></a>Avant de commencer

- L’exemple de script contient des exemples d’étapes. Il ne doit pas être exécuté sur des systèmes de production. Il peut endommager ou corrompre la machine virtuelle sur laquelle elle s’exécute.
- Nous vous recommandons de l’exécuter sur une machine virtuelle de test. Avant de commencer, effectuez une copie de sauvegarde ou une capture instantanée de machine virtuelle afin de pouvoir restaurer la machine virtuelle si nécessaire. 
- Le script fonctionne lorsque la machine virtuelle exécute l’une des distributions Linux suivantes :
    - Red Hat Enterprise Linux 6.5+, 7.1+
    - Cent OS 6.5+, 7.1+
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Exécutez le script

1. Copiez le script sur la machine virtuelle de test Linux à l’aide de sftp ou d’un client scp comme FileZilla ou WinScp.
2. Utilisez SSH sur la machine Linux à l’aide d’un compte d’administrateur.
3. Accédez au répertoire de scripts.
4. Pour créer le script dans un fichier exécutable, exécutez **sudo chmod 777 prepare-for-azure.sh**.
5. Exécutez le script avec **./prepare-for-azure.sh**.

Voici comment le script s’exécute :

![Linux script](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script6.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script11.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script16.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script21.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser le [mappage de dépendances de machine](how-to-create-group-machine-dependencies.md) pour créer des groupes à haute fiabilité.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
