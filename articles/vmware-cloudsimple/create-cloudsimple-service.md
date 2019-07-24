---
title: Créer la solution VMware du service CloudSimple
description: Décrit comment créer le service CloudSimple dans le portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0ccce6f298270b2751307868fdf85697cb7e8ee
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154969"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Créer la solution VMware du service CloudSimple

Pour commencer à utiliser la solution Azure VMware de CloudSimple, créez la solution VMware du service CloudSimple dans le portail Azure.

> [!NOTE]
> Avant de créer le service CloudSimple, vous devez inscrire le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure. Suivez les étapes dans [Inscrire le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

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

8. Entrez la notation CIDR de la passerelle du service. Spécifiez un sous-réseau /28 qui ne chevauche aucun sous-réseau existant.  Ceux-ci incluent les sous-réseaux locaux, les sous-réseaux Azure, ou tous les sous-réseaux CloudSimple prévus. Une fois le service créé, vous ne pouvez plus modifier le CIDR.

    ![Créer le service CloudSimple](media/create-cloudsimple-service.png)

9. Cliquez sur **OK**.

Le service est créé et ajouté à la liste des services.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Apprenez à [configurer un environnement de cloud privé](quickstart-create-private-cloud.md).
