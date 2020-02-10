---
title: Azure VMware Solutions (AVS) – Créer une machine virtuelle dans Azure avec des modèles de machine virtuelle
description: Indique comment créer des machines virtuelles dans Azure à l’aide de modèles de machine virtuelle sur l’infrastructure VMware pour votre cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 533aaab13f1b957e709f66b23b511fc199ee0285
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015199"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Créez une machine virtuelle dans Azure à l’aide de modèles d’ordinateur virtuel sur l’infrastructure VMware

Vous pouvez créer une machine virtuelle sur le portail Azure à l’aide de modèles de machine virtuelle sur l’infrastructure VMware que votre administrateur AVS a activés pour votre abonnement.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-avs-virtual-machine"></a>Créer une machine virtuelle AVS

1. Sélectionnez **Tous les services**.

2. Recherchez des **machines virtuelles AVS**.

3. Cliquez sur **Add**.

    ![Créer une machine virtuelle AVS](media/create-cloudsimple-virtual-machine.png)

4. Entrez les informations de base, puis cliquez sur **Suivant : taille**.

    > [!NOTE]
    > La création d’une machine virtuelle AVS sur Azure nécessite un modèle de machine virtuelle. Le modèle d’ordinateur virtuel doit exister sur votre vCenter de cloud privé. Créez une machine virtuelle sur votre cloud privé à partir de l’interface utilisateur vCenter avec les configurations et le système d’exploitation souhaités. Pour créer un modèle, suivez les instructions de la page [Cloner une machine virtuelle sur un modèle dans le client web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html).

    ![Créer une machine virtuelle AVS – Concepts de base](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Champ | Description |
    | ------------ | ------------- |
    | Subscription | Abonnement Azure associé à votre cloud privé.  |
    | Groupe de ressources | Groupe de ressources auquel la machine virtuelle sera attribuée. Vous pouvez sélectionner un groupe existant ou en créer un. |
    | Name | Nom destiné à identifier la machine virtuelle.  |
    | Location | Région Azure dans laquelle la machine virtuelle est hébergée.  |
    | Cloud privé | Cloud privé AVS où vous voulez créer la machine virtuelle. |
    | Liste de ressources partagées | Liste de ressources partagées mappée pour la machine virtuelle. Choisissez parmi les pools de ressources disponibles. |
    | Modèle vSphere | Modèle vSphere pour la machine virtuelle.  |
    | Nom d'utilisateur | Nom d’utilisateur de l’administrateur de la machine virtuelle (pour les modèles Windows)|
    | Mot de passe <br>Confirmer le mot de passe | Mot de passe de l’administrateur de la machine virtuelle (pour les modèles Windows).  |

5. Sélectionnez le nombre de cœurs et la capacité mémoire pour la machine virtuelle et cliquez sur **Suivant :Configurations**. Activez la case à cocher si vous souhaitez exposer la virtualisation de l’UC complète au système d’exploitation invité afin que les applications qui requièrent la virtualisation matérielle puissent s’exécuter sur des machines virtuelles sans traduction binaire ni paravirtualisation. Pour plus d’informations, consultez l’article VMware [Afficher la virtualisation assistée du matériel VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Créer une machine virtuelle AVS – Taille](media/create-cloudsimple-virtual-machine-size.png)

6. Configurez les interfaces réseau et les disques comme décrit dans les tableaux suivants, puis cliquez sur **Vérifier + créer**.

    ![Créer une machine virtuelle AVS – Configurations](media/create-cloudsimple-virtual-machine-configurations.png)

    Pour les interfaces réseau, cliquez sur **Ajouter une interface réseau**, puis configurez les paramètres suivants.

    | Control | Description |
    | ------------ | ------------- |
    | Name | Entrez un nom pour identifier l’interface.  |
    | Réseau | Sélectionnez dans la liste des groupes de ports distribués configurés dans votre cloud privé vSphere.  |
    | Adaptateur | Sélectionnez un adaptateur vSphere dans la liste des types disponibles configurés pour la machine virtuelle. Pour plus d’informations, consultez l’article de la base de connaissances VMware [Choosing a network adapter for your virtual machine](https://kb.vmware.com/s/article/1001805). |
    | Mettre sous tension au démarrage | Choisissez s’il faut activer la carte réseau quand la machine virtuelle est démarrée. La valeur par défaut est **Activer**. |

    Pour les disques, cliquez sur **Ajouter un disque**, puis configurez les paramètres suivants.

    | Élément | Description |
    | ------------ | ------------- |
    | Name | Entrez un nom pour identifier le disque.  |
    | Size | Sélectionnez une des tailles disponibles.  |
    | Contrôleur SCSI | Sélectionnez un contrôleur SCSI pour le disque.  |
    | Mode | Détermine comment le disque participe aux captures instantanées. Choisissez une de ces options : <br> - Indépendant persistant : Toutes les données écrites sur le disque sont écrites de façon permanente.<br> - Indépendant non persistant : Les modifications écrites sur le disque sont abandonnées lors de la mise hors tension ou de la réinitialisation de la machine virtuelle. Le mode indépendant non persistant vous permet de toujours redémarrer la machine virtuelle dans le même état. Pour plus d’informations, consultez la [documentation de VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Une fois la validation terminée, passez en revue les paramètres et cliquez sur **Créer**. Pour apporter des modifications, cliquez sur les onglets situés en haut.

    ![Créer une machine virtuelle AVS – Revue](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-avs-virtual-machines"></a>Afficher la liste des machines virtuelles AVS

1. Sélectionnez **Tous les services**.

2. Recherchez des **machines virtuelles AVS**.

3. Sélectionnez l’élément sur lequel votre cloud privé a été créé.

    ![Liste des machines virtuelles AVS](media/list-cloudsimple-virtual-machines.png)

La liste des machines virtuelles AVS comprend les machines virtuelles créées à partir du portail Azure.  Les machines virtuelles créées sur le vCenter de cloud privé dans une liste de ressources partagées mappée vCenter seront affichées sur le portail Azure.  
