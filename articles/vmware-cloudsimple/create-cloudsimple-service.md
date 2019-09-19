---
title: Solution Azure VMware de CloudSimple - Créer la solution VMware du service CloudSimple
description: Décrit comment créer le service CloudSimple dans le portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6d4af28d79b2375e774da98d4fdb1ad9dc22063
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035722"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Créer la solution Azure VMware du service CloudSimple

Pour commencer à utiliser la solution Azure VMware de CloudSimple, créez la solution VMware du service CloudSimple dans le portail Azure.

> [!IMPORTANT]
> Avant de créer le service CloudSimple, vous devez inscrire le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure. Suivez les étapes dans [Inscrire le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Créer le service

1. Sélectionnez **Tous les services**.
2. Recherchez **Services CloudSimple**.
    ![Rechercher le service CloudSimple](media/create-cloudsimple-service-search.png)
3. Sélectionnez **Services CloudSimple**.
4. Cliquez sur **Ajouter** pour créer un service.
    ![Ajouter le service CloudSimple](media/create-cloudsimple-service-add.png)
5. Sélectionnez l’abonnement dans lequel vous souhaitez créer le service CloudSimple.
6. Sélectionnez le groupe de ressources du service. Pour ajouter un nouveau groupe de ressources, cliquez sur **Créer**.
7. Entrez un nom pour identifier le service.
8. Entrez la notation CIDR de la passerelle du service. Spécifiez un sous-réseau /28 qui ne chevauche aucun de vos sous-réseaux locaux, sous-réseaux Azure ou sous-réseaux CloudSimple planifiés. Une fois le service créé, vous ne pouvez plus modifier le CIDR.

    ![Créer le service CloudSimple](media/create-cloudsimple-service.png)
9. Cliquez sur **OK**.

Le service est créé et ajouté à la liste des services.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](create-private-cloud.md)
* Découvrez comment [configurer un environnement de cloud privé](quickstart-create-private-cloud.md)
