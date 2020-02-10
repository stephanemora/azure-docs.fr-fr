---
title: Guide de démarrage rapide d’Azure VMware Solutions (AVS) – Créer des machines virtuelles VMware sur Azure
description: Découvrir comment créer et configurer des machines virtuelles VMware à partir du portail Azure en utilisant Azure VMware Solution (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019551"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Guide de démarrage rapide – Créer des machines virtuelles VMware sur Azure

Pour créer une machine virtuelle sur le portail Azure, utilisez les modèles de machines virtuelles que votre administrateur AVS a activés pour votre abonnement. Les modèles de machine virtuelle se trouvent dans l’infrastructure VMware.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>La création d’une machine virtuelle AVS sur Azure nécessite un modèle de machine virtuelle

Créez une machine virtuelle sur votre cloud privé AVS à partir de l’interface utilisateur de vCenter. Pour créer un modèle, suivez les instructions de la page [Cloner une machine virtuelle sur un modèle dans le client web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Stockez le modèle de machine virtuelle sur votre vCenter de cloud privé AVS.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Créer une machine virtuelle dans le portail Azure

1. Sélectionnez **Tous les services**.

2. Recherchez des **machines virtuelles AVS**.

3. Cliquez sur **Add**.

    ![Créer une machine virtuelle AVS](media/create-cloudsimple-virtual-machine.png)

4. Entrez les informations de base, puis cliquez sur **Suivant : taille**.

    ![Créer une machine virtuelle AVS – Concepts de base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Champ | Description |
    | ------------ | ------------- |
    | Subscription | Abonnement Azure associé à votre cloud privé AVS. |
    | Groupe de ressources | Groupe de ressources auquel la machine virtuelle sera attribuée. Vous pouvez sélectionner un groupe existant ou en créer un. |
    | Name | Nom destiné à identifier la machine virtuelle.  |
    | Location | Région Azure dans laquelle la machine virtuelle est hébergée.  |
    | Cloud privé AVS | Cloud privé AVS où vous voulez créer la machine virtuelle. |
    | Liste de ressources partagées | Liste de ressources partagées mappée pour la machine virtuelle. Choisissez parmi les pools de ressources disponibles. |
    | Modèle vSphere | Modèle vSphere pour la machine virtuelle.  |
    | Nom d'utilisateur | Nom d’utilisateur de l’administrateur de la machine virtuelle (pour les modèles Windows).|
    | Mot de passe |  Mot de passe de l’administrateur de la machine virtuelle (pour les modèles Windows). |
    | Confirmer le mot de passe | Confirmez le mot de passe. |

5. Sélectionnez le nombre de cœurs et la capacité mémoire pour la machine virtuelle et cliquez sur **Suivant :Configurations**. Sélectionnez la case à cocher si vous voulez exposer la virtualisation complète de l’UC au système d’exploitation invité. Les applications nécessitant la virtualisation matérielle peuvent fonctionner sur des machines virtuelles sans traduction binaire ou paravirtualisation. Pour plus d’informations, consultez l’article VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Afficher la virtualisation assistée du matériel VMware</a>.

    ![Créer une machine virtuelle AVS – Taille](media/create-cloudsimple-virtual-machine-size.png)

6. Configurez les interfaces réseau et les disques comme décrit dans les tableaux suivants, puis cliquez sur **Vérifier + créer**.

    ![Créer une machine virtuelle AVS – Configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Pour les interfaces réseau, cliquez sur **Ajouter une interface réseau**, puis configurez les paramètres suivants.

    | Control | Description |
    | ------------ | ------------- |
    | Name | Entrez un nom pour identifier l’interface.  |
    | Réseau | Sélectionnez dans la liste des groupes de ports distribués configurés dans votre cloud privé AVS vSphere. |
    | Adaptateur | Sélectionnez un adaptateur vSphere dans la liste des types disponibles configurés pour la machine virtuelle. Pour plus d’informations, consultez l’article de la base de connaissances VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Choosing a network adapter for your virtual machine</a>. |
    | Mettre sous tension au démarrage | Choisissez s’il faut activer la carte réseau quand la machine virtuelle est démarrée. La valeur par défaut est **Activer**. |

    Pour les disques, cliquez sur **Ajouter un disque**, puis configurez les paramètres suivants.

    | Élément | Description |
    | ------------ | ------------- |
    | Name | Entrez un nom pour identifier le disque. |
    | Size | Sélectionnez une des tailles disponibles. |
    | Contrôleur SCSI | Sélectionnez un contrôleur SCSI pour le disque. |
    | Mode | Détermine comment le disque participe aux captures instantanées. Choisissez une de ces options : <br> - Indépendant persistant : Toutes les données écrites sur le disque sont écrites de façon permanente.<br> - Indépendant non persistant : Les modifications écrites sur le disque sont abandonnées lors de la mise hors tension ou de la réinitialisation de la machine virtuelle. Le mode indépendant non persistant vous permet de toujours redémarrer la machine virtuelle dans le même état. Pour plus d’informations, consultez la <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentation de VMware</a>.

7. Une fois la validation terminée, passez en revue les paramètres et cliquez sur **Créer**. Pour apporter des modifications, cliquez sur les onglets situés en haut.

    ![Créer une machine virtuelle AVS – Revue](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Étapes suivantes

* [Afficher la liste des machines virtuelles AVS](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [Gérer une machine virtuelle AVS à partir d’Azure](azure-manage-vm.md)
