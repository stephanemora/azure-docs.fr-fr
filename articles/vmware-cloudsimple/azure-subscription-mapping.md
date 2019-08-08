---
title: Mapper un abonnement Azure aux pools de ressources sur la solution Azure VMware de CloudSimple
description: Décrit comment mapper un pool de ressources sur la solution Azure VMware de CloudSimple à votre abonnement Azure.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816267"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapper des pools de ressources de votre cloud privé à votre abonnement Azure

Le mappage des abonnements Azure vous permet de mapper des pools de ressources de votre cloud privé vCenter à votre abonnement Azure. Vous pouvez mapper uniquement l’abonnement dans lequel vous avez créé le service CloudSimple.  La création d’une machine virtuelle VMware à partir du Portail Azure déploie cette dernière dans le pool de ressources mappé.  Dans le portail CloudSimple, vous pouvez afficher et gérer l’abonnement Azure pour vos clouds privés.

Un abonnement peut être mappé à plusieurs pools de ressources vCenter d’un cloud privé.  Vous devez mapper les pools de ressources de chaque cloud privé.  Seuls les pools de ressources mappés seront disponibles pour la création d’une machine virtuelle VMware à partir du Portail Azure.

> [!IMPORTANT]
> Le mappage d’un pool de ressources mappe également tous les pools de ressources enfants. Un pool de ressources parent ne peut pas être mappé si l’un de ses pools de ressources enfants est déjà mappé.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous disposez d’un service CloudSimple et d’un cloud privé dans votre abonnement.  Pour créer un service CloudSimple, consultez [Démarrage rapide - Créer un service](quickstart-create-cloudsimple-service.md).  Si vous avez besoin de créer un cloud privé, consultez [Démarrage rapide - Configurer un environnement de cloud privé](quickstart-create-private-cloud.md).

Vous pouvez mapper le cluster vCenter (pool de ressources racine) à votre abonnement.  Si vous souhaitez créer un pool de ressources, consultez l’article décrivant la [création d’un pool de ressources](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) sur le site de la documentation VMware.

## <a name="default-resource-group"></a>Groupe de ressources par défaut

La création d’une machine virtuelle CloudSimple à partir du Portail Azure vous permet de sélectionner le groupe de ressources.  Une machine virtuelle créée sur le serveur vCenter de cloud privé dans un pool de ressources mappé sera visible sur le Portail Azure.  La machine virtuelle détectée sera placée dans le groupe de ressources Azure par défaut.  Vous pouvez modifier le nom du groupe de ressources par défaut.

## <a name="map-azure-subscription"></a>Mapper un abonnement Azure

1. Accédez au [portail CloudSimple](access-cloudsimple-portal.md).

2. Ouvrez la page **Resources** (Ressources)et sélectionnez le cloud privé que vous souhaitez mapper.

3. Sélectionnez **Azure subscriptions mapping** (Mappage des abonnements Azure).

4. Cliquez sur **Edit Azure subscription mapping** (Modifier le mappage des abonnements Azure).

5. Pour mapper les pools de ressources disponibles, sélectionnez-les sur la gauche et cliquez sur la flèche pointant à droite.

6. Pour supprimer des mappages, sélectionnez-les sur la droite et cliquez sur la flèche pointant à gauche.

    ![Abonnements Azure](media/resources-azure-mapping.png)

7. Cliquez sur **OK**.

## <a name="change-default-resource-group-name"></a>Modifier le nom du groupe de ressources par défaut

1. Accédez au [portail CloudSimple](access-cloudsimple-portal.md).

2. Ouvrez la page **Resources** (Ressources)et sélectionnez le cloud privé que vous souhaitez mapper.

3. Sélectionnez **Azure subscriptions mapping** (Mappage des abonnements Azure).

4. Cliquez sur **Edit** (Modifier) sous le nom du groupe de ressources Azure.

    ![Modifier le nom du groupe de ressources](media/resources-edit-resource-group-name.png)

5. Entrez un nouveau nom pour le groupe de ressources et cliquez sur **Submit** (Envoyer).

    ![Entrer un nouveau nom de groupe de ressources](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* En savoir plus sur les [machines virtuelles CloudSimple](cloudsimple-virtual-machines.md)