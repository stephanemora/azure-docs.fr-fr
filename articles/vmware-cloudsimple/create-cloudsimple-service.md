---
title: Créer la Solution de VMware Azure par CloudSimple - service
description: Décrit comment créer le service CloudSimple dans le portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676952"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Créer la Solution VMware Azure par CloudSimple - Service

Pour commencer à utiliser Azure VMware Solution par CloudSimple, créez la VMware Solution Azure par service CloudSimple dans le portail Azure.

> [!NOTE]
> Avant de créer le service CloudSimple, vous devez inscrire le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure. Suivez les étapes de [activer le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Créer le service

1. Sélectionnez **Tous les services**.

2. Recherchez **CloudSimple Services**.

    ![Service de recherche CloudSimple](media/create-cloudsimple-service-search.png)

3. Sélectionnez **CloudSimple Services**.

4. Cliquez sur **ajouter** pour créer un nouveau service.

    ![Ajouter un Service de CloudSimple](media/create-cloudsimple-service-add.png)

5. Sélectionnez l’abonnement dans lequel vous souhaitez créer le service CloudSimple.

6. Sélectionnez le groupe de ressources pour le service. Pour ajouter un nouveau groupe de ressources, cliquez sur **créer un nouveau**.

7. Entrez un nom pour identifier le service.

8. Entrez la CIDR pour la passerelle de service. Spécifiez un sous-réseau/28 sous-réseau qui ne se chevauche pas avec une de vos sous-réseaux existants.  Ceux-ci incluent des sous-réseaux locaux, les sous-réseaux Azure, ou les vont CloudSimple sous-réseaux. Vous ne pouvez pas modifier la CIDR que le service est créé.

    ![Création du service CloudSimple](media/create-cloudsimple-service.png)

9. Cliquez sur **OK**.

Le service est créé et ajouté à la liste des services.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Découvrez comment [configurer un environnement de cloud privé](quickstart-create-private-cloud.md)
