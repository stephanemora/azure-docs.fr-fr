---
title: Créer des réseaux locaux virtuels/sous-réseaux
description: Azure VMware Solutions (AVS) - Décrit comment créer et gérer des réseaux locaux virtuels/sous-réseaux pour vos clouds privés AVS, puis appliquer des règles de pare-feu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 249c48500dbcd75f62f856b3345b3a2c02502d1a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024770"
---
# <a name="create-and-manage-vlanssubnets-for-your-avs-private-clouds"></a>Créer et gérer des réseaux locaux virtuels ou des sous-réseaux pour vos clouds privés AVS

Ouvrez l’onglet Réseaux locaux virtuels/sous-réseaux dans la page Réseau pour créer et gérer des réseaux locaux virtuels/sous-réseaux pour vos clouds privés AVS. Après avoir créé un réseau local virtuel/sous-réseau, vous pouvez appliquer des règles de pare-feu.

## <a name="create-a-vlansubnet"></a>Créer un réseau local virtuel/sous-réseau

1. [Accédez au portail AVS](access-cloudsimple-portal.md), puis sélectionnez **Réseau** dans le menu latéral.
2. Sélectionnez **VLANs/subnets** (Réseaux locaux virtuels/sous-réseaux).
3. Cliquez sur **Create VLAN/Subnet** (Créer un VLAN/sous-réseau).

    ![Page de VLAN/sous-réseau](media/vlan-subnet-page.png)

4. Sélectionnez le cloud privé AVS pour le nouveau réseau local virtuel/sous-réseau.
5. Entrez un ID de réseau local virtuel.
6. Entrez le nom du sous-réseau.
7. Pour activer le routage sur le réseau local virtuel (sous-réseau), spécifiez la plage CIDR du sous-réseau. Vérifiez que la plage CIDR ne chevauche aucun de vos sous-réseaux locaux, sous-réseaux Azure ou sous-réseaux de passerelle.
8. Cliquez sur **Envoyer**.

    ![Créer un réseau local virtuel/sous-réseau](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Il existe un quota de 30 VLAN par cloud privé. Il est possible d’augmenter ces limites en [contactant le support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Utiliser les informations de réseau local virtuel pour configurer un groupe de ports distribués dans vSphere

Pour créer un groupe de ports distribués dans vSphere, suivez les instructions de la rubrique VMware « Ajouter un groupe de ports distribués » dans le <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">Guide de mise en réseau vSphere</a>. Quand vous configurez le groupe de ports distribués, spécifiez les informations de réseau local virtuel à partir de la configuration AVS.

![Groupe de ports distribués](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Sélectionner une table de pare-feu

Les tables de pare-feu et les règles associées sont définies dans la page **Network > Firewall tables** (Réseau > Tables de pare-feu). Pour sélectionner la table de pare-feu à appliquer au réseau local virtuel/sous-réseau d’un cloud privé AVS, sélectionnez le réseau local virtuel/sous-réseau et cliquez sur **Attachement de table de pare-feu** dans la page **Réseaux locaux virtuels/sous-réseaux**. Pour obtenir des instructions sur la configuration des tables de pare-feu et la définition des règles, consultez [Tables de pare-feu](firewall.md).

![Lien Table de pare-feu](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Vous pouvez associer un sous-réseau à une table de pare-feu. Vous pouvez associer une table de pare-feu à plusieurs sous-réseaux.

## <a name="edit-a-vlansubnet"></a>Modifier un réseau local virtuel/sous-réseau

Pour modifier les paramètres d’un réseau local virtuel/sous-réseau, sélectionnez-le dans la page **VLANs/Subnets**, puis cliquez sur l’icône **Modifier**. Apportez des modifications et cliquez sur **Submit**.

## <a name="delete-a-vlansubnet"></a>Supprimer un réseau local virtuel/sous-réseau

Pour supprimer un réseau local virtuel/sous-réseau, sélectionnez-le dans la page **VLANs/Subnets**, puis cliquez sur l’icône **Supprimer**. Cliquez sur **Delete** (Supprimer) pour confirmer.
