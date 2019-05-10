---
title: La VMware Solution Azure par CloudSimple Quickstart - utiliser des machines virtuelles VMware sur Azure
description: Découvrez comment configurer et d’utiliser des machines virtuelles VMware à partir du portail Azure à l’aide de la Solution de VMware Azure par CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0bb8d7a5a1ea30b704b44c9337cd28043597ff7
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209521"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guide de démarrage rapide - utiliser des machines virtuelles VMware sur Azure

Pour créer une machine virtuelle dans le portail Azure, utilisez les modèles d’ordinateurs virtuels activés par votre administrateur CloudSimple pour votre abonnement. Ces modèles de machine virtuelle sont trouvent sur l’infrastructure VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>La création de CloudSimple VM sur Azure nécessite un modèle de machine virtuelle

Créer une machine virtuelle sur votre cloud privé à partir de l’interface utilisateur de vCenter. Pour créer un modèle, suivez les instructions de [cloner un ordinateur virtuel à un modèle dans le Client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store le modèle de machine virtuelle sur votre vCenter de cloud privé.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Créer une machine virtuelle dans le portail Azure

1. Sélectionnez **Tous les services**.

2. Recherchez **CloudSimple des Machines virtuelles**.

3. Cliquez sur **Add**.

    ![Créer la machine virtuelle de CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Entrez les informations de base, cliquez **suivant : taille**.

    ![Créer la machine virtuelle de CloudSimple - principes de base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Champ | Description  |
    | ------------ | ------------- |
    | Abonnement | Abonnement Azure associé à votre Cloud privé.  |
    | Groupe de ressources | Groupe de ressources auquel la machine virtuelle sera être affectée. Vous pouvez sélectionner un groupe existant ou créez-en un. |
    | Nom | Nom pour identifier la machine virtuelle.  |
    | Lieu | Région Azure dans lequel cet ordinateur virtuel est hébergé.  |
    | Cloud privé | CloudSimple privé où vous souhaitez créer la machine virtuelle. |
    | Liste de ressources partagées | Pool de ressources mappé pour la machine virtuelle. Sélectionnez dans les pools de ressources disponibles. |
    | vSphere modèle | modèle de vSphere pour la machine virtuelle.  |
    | Nom d'utilisateur | Nom d’utilisateur de l’administrateur de la machine virtuelle (pour les modèles de Windows)|
    | Mot de passe |  Mot de passe pour l’administrateur de la machine virtuelle (pour les modèles de Windows). |
    | Confirmer le mot de passe | Confirmer le mot de passe |

5. Sélectionnez le nombre de cœurs et la capacité de mémoire pour la machine virtuelle et cliquez sur **suivant : Configurations**. Sélectionnez la case à cocher si vous voulez exposer la virtualisation complète du processeur au système d’exploitation invité. Les applications nécessitant la virtualisation de matériel peuvent fonctionner sur des machines virtuelles sans traduction binaire ou de paravirtualisation. Pour plus d’informations, consultez l’article de VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">virtualisation d’assistance VMware matérielle exposer</a>.

    ![Créer la machine virtuelle de CloudSimple - taille](media/create-cloudsimple-virtual-machine-size.png)

6. Configurer les interfaces réseau et des disques, comme décrit dans les tableaux suivants et cliquez sur **révision + créer**.

    ![Créer la machine virtuelle de CloudSimple - configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Pour les interfaces réseau, cliquez sur **interface de réseau Add** et configurez les paramètres suivants.
    
    | Contrôle | Description  |
    | ------------ | ------------- |
    | Nom | Entrez un nom pour identifier l’interface.  |
    | Réseau | Sélectionnez dans la liste du groupe de ports distribué configurée dans votre Cloud privé de vSphere.  |
    | Adaptateur | Sélectionnez un adaptateur de vSphere dans la liste des types disponibles configuré pour la machine virtuelle. Pour plus d’informations, consultez l’article de base de connaissances VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">en choisissant une carte réseau pour votre machine virtuelle</a>. |
    | Mise sous tension au démarrage | Choisissez s’il faut activer le matériel de carte réseau lors de la machine virtuelle est démarrée. La valeur par défaut est **activer**. |

    Pour les disques, cliquez sur **ajouter un disque de** et configurez les paramètres suivants.

    | Item | Description  | 
    | ------------ | ------------- | 
    | Nom | Entrez un nom pour identifier le disque.  | 
    | Taille | Sélectionnez une des tailles disponibles.  | 
    | Contrôleur SCSI | Sélectionnez un contrôleur SCSI pour le disque.  |
    | Mode | Détermine comment le disque participe de captures instantanées. Choisissez une des options suivantes : <br> -Indépendant persistant : Toutes les données écrites sur le disque est écrit de façon permanente.<br> -Indépendant non persistant : Les modifications écrites sur le disque sont ignorées lors de la mise hors tension ou de réinitialiser la machine virtuelle.  Mode non persistant indépendant vous permet de toujours redémarrer la machine virtuelle dans le même état. Pour plus d’informations, consultez le <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentation VMware</a>.

7. Une fois la validation terminée, passez en revue les paramètres et cliquez sur **créer**. Pour apporter des modifications, cliquez sur les onglets en haut, ou cliquez sur.

    ![Créer la machine virtuelle de CloudSimple : passez en revue](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Étapes suivantes

* [Afficher la liste des machines virtuelles de CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Gérer la machine virtuelle de CloudSimple à partir d’Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
