---
title: Démarrage rapide de la solution Azure VMware de CloudSimple - Utiliser des machines virtuelles VMware sur Azure
description: Dans ce guide de démarrage rapide, vous allez découvrir comment configurer et utiliser des machines virtuelles VMware à partir du portail Azure en utilisant la solution Azure VMware par CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393494"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Démarrage rapide : Utiliser des machines virtuelles VMware sur Azure

Pour créer une machine virtuelle dans le portail Azure, vous pouvez utiliser des modèles de machine virtuelle disponibles sur votre vCenter de cloud privé. Un administrateur vCenter peut créer des modèles supplémentaires sur le cloud privé.

## <a name="create-a-vm-template"></a>Créer un modèle de machine virtuelle

Créez d’abord une machine virtuelle sur votre cloud privé via l’interface utilisateur de vCenter. Pour créer un modèle, suivez les instructions de l’article VMware [Cloner une machine virtuelle en modèle dans vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Stockez le modèle de machine virtuelle sur votre vCenter de cloud privé.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Créer une machine virtuelle dans le portail Azure

1. Sélectionnez **Tous les services**.

2. Recherchez **Machines virtuelles CloudSimple**.

3. Sélectionnez **Ajouter**.

    ![Sélectionner Ajouter](media/create-cloudsimple-virtual-machine.png)

4. Entrez les informations suivantes sur les machines virtuelles, puis sélectionnez **Suivant : Taille**.

    ![Créer une machine virtuelle CloudSimple - Principes de base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Champ | Description |
    | ------------ | ------------- |
    | **Abonnement** | Abonnement Azure associé à votre cloud privé.  |
    | **Groupe de ressources** | Groupe de ressources auquel la machine virtuelle sera affectée. Vous pouvez sélectionner un groupe existant ou en créer un. |
    | **Nom** | Nom destiné à identifier la machine virtuelle.  |
    | **Lieu** | Région Azure où la machine virtuelle est hébergée.  |
    | **Cloud privé** | Cloud privé CloudSimple où vous voulez créer la machine virtuelle. |
    | **ResourcePool** | Pool de ressources mappé pour la machine virtuelle. Choisissez parmi les pools de ressources disponibles. |
    | **Modèle vSphere** | Modèle vSphere pour la machine virtuelle.  |
    | **Nom d'utilisateur** | Nom d’utilisateur de l’administrateur de la machine virtuelle (pour les modèles Windows).|
    | **Mot de passe** |  Mot de passe de l’administrateur de la machine virtuelle (pour les modèles Windows). |
    | **Confirmer le mot de passe** | Mot de passe que vous avez fourni dans le champ précédent. |

5. Sélectionnez le nombre de cœurs et la capacité mémoire pour la machine virtuelle. Sélectionnez **Exposer au système d’exploitation invité** si vous voulez exposer la virtualisation complète du processeur au système d’exploitation invité. Les applications nécessitant la virtualisation matérielle peuvent fonctionner sur des machines virtuelles sans traduction binaire ou paravirtualisation. Pour plus d’informations, consultez l’article VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Afficher la virtualisation assistée du matériel VMware</a>. Quand vous avez terminé, sélectionnez **Suivant : Configurations**.

    ![Créer une machine virtuelle CloudSimple - Taille](media/create-cloudsimple-virtual-machine-size.png)

6. Configurez les interfaces réseau et les disques comme décrit dans les tableaux suivants, puis sélectionnez **Vérifier + créer**.

    ![Créer une machine virtuelle CloudSimple - Configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Pour les interfaces réseau, sélectionnez **Ajouter une interface réseau**, puis configurez les paramètres suivants :
    
    | Paramètre | Description |
    | ------------ | ------------- |
    | **Nom** | Entrez un nom pour identifier l’interface.  |
    | **Réseau** | Sélectionnez dans la liste des groupes de ports distribués configurés dans votre cloud privé vSphere.  |
    | **Adaptateur** | Sélectionnez un adaptateur vSphere dans la liste des types disponibles configurés pour la machine virtuelle. Pour plus d’informations, consultez l’article VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Choosing a network adapter for your virtual machine</a>. |
    | **Mettre sous tension au démarrage** | Choisissez s’il faut activer la carte réseau quand la machine virtuelle est démarrée. La valeur par défaut est **Activer**. |

    Pour les disques, sélectionnez **Ajouter un disque**, puis configurez les paramètres suivants :

    | Paramètre | Description |
    | ------------ | ------------- |
    | **Nom** | Entrez un nom pour identifier le disque.  |
    | **Taille** | Sélectionnez une des tailles disponibles.  |
    | **Contrôleur SCSI** | Sélectionnez un contrôleur SCSI pour le disque.  |
    | **Mode** | Le mode spécifie comment le disque participe aux captures instantanées. Choisissez une de ces options : <br> **Indépendant persistant** : Toutes les modifications écrites sur le disque sont écrites définitivement.<br> **Indépendant non persistant** : Les modifications écrites sur le disque sont abandonnées lors de la mise hors tension ou de la réinitialisation de la machine virtuelle. Le mode indépendant non persistant vous permet de toujours redémarrer la machine virtuelle dans le même état. Pour plus d’informations, consultez la <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentation de VMware</a>.

7. Une fois la validation terminée, passez en revue les paramètres et sélectionnez **Créer**. Pour apporter des modifications, sélectionnez les onglets en haut ou sélectionnez **Précédent : Configurations**.

    ![Créer une machine virtuelle CloudSimple - Vérifier + créer](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Étapes suivantes

* [Afficher une liste des machines virtuelles CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Gérer des machines virtuelles CloudSimple à partir d’Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
