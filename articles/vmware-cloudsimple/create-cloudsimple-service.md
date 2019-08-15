---
title: Créer la solution VMware du service CloudSimple
description: Décrit comment créer le service CloudSimple dans le portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812443"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Créer la solution VMware du service CloudSimple

Pour commencer à utiliser la solution Azure VMware de CloudSimple, créez la solution VMware du service CloudSimple dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

Allouez un bloc /28 CIDR pour le sous-réseau de passerelle.  Un sous-réseau de passerelle est requis par le service CloudSimple et est propre à la région dans laquelle il est créé. Le sous-réseau de passerelle est utilisé pour les services de réseau Edge et nécessite un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne doit chevaucher aucun réseau qui communique avec l’environnement CloudSimple.  Les réseaux qui communiquent avec CloudSimple incluent les réseaux locaux et les réseaux virtuels Azure.

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
