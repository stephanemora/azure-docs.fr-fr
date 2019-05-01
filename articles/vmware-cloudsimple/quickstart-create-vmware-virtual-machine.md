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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577719"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Guide de démarrage rapide - utiliser des machines virtuelles VMware sur Azure

Pour créer une machine virtuelle dans le portail Azure, utilisez les modèles d’ordinateurs virtuels activés par votre administrateur CloudSimple pour votre abonnement. Ces modèles de machine virtuelle sont trouvent sur l’infrastructure VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>La création de CloudSimple VM sur Azure nécessite un modèle de machine virtuelle

Créer une machine virtuelle sur votre cloud privé à partir de l’interface utilisateur de vCenter. Pour créer un modèle, suivez les instructions de [cloner un ordinateur virtuel à un modèle dans le Client Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store le modèle de machine virtuelle sur votre vCenter de cloud privé.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Créer une machine virtuelle dans le portail Azure

1. Dans le menu de gauche, cliquez sur **+** ou **créer une ressource**.

2. Dans le menu de gauche, cliquez sur **calcul**, puis cliquez sur **CloudSimple Virtual Machine**.

3. Cliquez sur **confirmer** pour vérifier que vous souhaitez créer une machine virtuelle.

4. Définissez la configuration de base comme décrit dans le tableau suivant, puis cliquez sur **suivant : Taille**.

    | Champ | Description |
    | ------------ | ------------- |
    | Abonnement | Abonnement Azure associé à votre déploiement de cloud privé.  |
    | Groupe de ressources | Groupe de déploiement auquel la machine virtuelle sera être affectée. Vous pouvez sélectionner un groupe existant ou créez-en un. |
    | Nom | Nom pour identifier la machine virtuelle.  |
    | Lieu | Région Azure dans lequel cet ordinateur virtuel est hébergé.  |
    | Liste de ressources partagées | Ressources physiques de la machine virtuelle. Sélectionnez dans les pools de ressources disponibles. |
    | vSphere modèle | Type de modèle de système d’exploitation pour la machine virtuelle.  |
    | Nom d'utilisateur | Nom d’utilisateur de l’administrateur de la machine virtuelle. |
    | Mot de passe de confirmer mot de passe | Mot de passe pour l’administrateur de la machine virtuelle.  |

5. Sélectionnez le nombre de cœurs et la capacité de mémoire pour la machine virtuelle.

6. (Facultatif) Si vous souhaitez exposer la virtualisation complète du processeur au système d’exploitation invité, sélectionnez le **exposer au système d’exploitation invité** case à cocher.
Cette sélection permet aux applications qui nécessitent la virtualisation matérielle à exécuter sur les machines virtuelles sans traduction binaire ou de paravirtualisation. Pour plus d’informations, consultez l’article de VMware [virtualisation d’assistance VMware matérielle exposer](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Cliquez sur **Suivant : Configuration**.

8. Configurer les interfaces réseau et des disques, comme décrit dans les tableaux suivants.

    Pour les interfaces réseau, cliquez sur **interface de réseau Add** et configurez les paramètres suivants.

    | Contrôle | Description |
    | ------------ | ------------- |
    | Nom | Entrez un nom pour identifier l’interface.  |
    | Réseau | Sélectionnez dans la liste des réseaux configurés dans votre Cloud privé de vSphere.  |
    | Adaptateur | Sélectionnez un adaptateur de vSphere dans la liste des types disponibles configuré pour la machine virtuelle. Pour plus d’informations, consultez l’article de base de connaissances VMware [en choisissant une carte réseau pour votre machine virtuelle](https://kb.vmware.com/s/article/1001805). |
    | Mise sous tension au démarrage | Choisissez s’il faut activer le matériel de carte réseau lors de la machine virtuelle est démarrée. La valeur par défaut est **activer**. |

    Pour les disques, cliquez sur **ajouter un disque de** et configurez les paramètres suivants.

    | Item | Description |
    | ------------ | ------------- |
    | Nom | Entrez un nom pour identifier le disque.  |
    | Taille | Sélectionnez une des tailles disponibles.  |
    | Contrôleur SCSI | Sélectionnez un contrôleur SCSI. Les contrôleurs disponibles varient selon les différents systèmes d’exploitation pris en charge.  |
    | Mode | Détermine comment le disque participe de captures instantanées. Choisissez une des options suivantes : <br> -Indépendant persistant : Toutes les données écrites sur le disque est écrit de façon permanente.<br> -Indépendant non persistant : Les modifications écrites sur le disque sont ignorées lors de la mise hors tension ou de réinitialiser la machine virtuelle.  Mode non persistant indépendant vous permet de toujours redémarrer la machine virtuelle dans le même état. Pour plus d’informations, consultez le [documentation VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Passez en revue les paramètres. Pour apporter des modifications, cliquez sur les onglets en haut.

10. Cliquez sur **créer** pour enregistrer les paramètres et créer la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

* [Afficher la liste des machines virtuelles de CloudSimple](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Gérer la machine virtuelle de CloudSimple à partir d’Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)