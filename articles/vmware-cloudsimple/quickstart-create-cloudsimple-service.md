---
title: Démarrage rapide de la solution Azure VMware de CloudSimple - Créer le service
description: En savoir plus sur la création du service CloudSimple, l’achat et la réservation de nœuds
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577569"
---
# <a name="quickstart---create-service"></a>Démarrage rapide - Créer le service

Pour commencer, créez la Solution Azure VMware de CloudSimple dans le portail Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solution VMware de CloudSimple - Vue d’ensemble du service

Le service CloudSimple vous permet d'utiliser la Solution Azure VMware de CloudSimple.  En créant ce service, vous pouvez acheter, réserver des nœuds et créer des clouds privés.  Vous ajoutez le service de CloudSimple à chaque région Azure où ce service est disponible.  Le service définit le réseau de périmètre de la Solution Azure VMware de CloudSimple.  Ce réseau de périmètre est destiné aux services comprenant une connectivité VPN, ExpressRoute et Internet vers vos clouds privés.

Pour ajouter le service CloudSimple, vous devez créer un sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et requiert un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne peut pas chevaucher un de vos espaces d'adressage de réseau local ni l'espace d'adressage du réseau virtuel Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Activer le fournisseur de ressources Microsoft.VMwareCloudSimple

Suivez les étapes ci-dessous pour activer le fournisseur de ressources du service CloudSimple.

1. Sélectionnez **Tous les services**.
2. Recherchez et sélectionnez des **abonnements**.

    ![Sélectionner des abonnements](media/cloudsimple-service-select-subscriptions.png)

3. Sélectionnez l’abonnement sur lequel activer le service CloudSimple.
4. Cliquer sur **Fournisseurs de ressources** pour l’abonnement
5. Utiliser **Microsoft.VMwareCloudSimple** pour filtrer le fournisseur de ressources
6. Sélectionner le fournisseur de ressources **Microsoft.VMwareCloudSimple**, puis cliquer sur **S'inscrire**

    ![S’inscrire auprès du fournisseur de ressources](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Créer le service

>[!NOTE]
> Le service CloudSimple doit être activé sur votre abonnement. Si votre abonnement n’est pas activé, une erreur s'affiche lorsque vous tentez de créer le service.  Pour activer le service, suivez la procédure décrite dans l'article [Activer le service CloudSimple](https://docs.azure.cloudsimple.com/enable-cloudsimple-service).

1. Sélectionnez **Tous les services**.
2. Recherchez le **service CloudSimple**.

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

## <a name="purchase-nodes"></a>Acheter des nœuds

Pour configurer la capacité avec paiement à l'utilisation d'un environnement de cloud privé CloudSimple, commencez par approvisionner des nœuds dans le portail Azure.

1. Sélectionnez **Tous les services**.
2. Recherchez **Nœuds CloudSimple**.

    ![Rechercher des nœuds CloudSimple](media/create-cloudsimple-node-search.png)

3. Sélectionnez **Nœuds CloudSimple**.
4. Cliquez sur **Ajouter** pour créer les nœuds.

    ![Ajouter des nœuds CloudSimple](media/create-cloudsimple-node-add.png)

5. Sélectionnez l’abonnement dans lequel vous souhaitez acheter des nœuds CloudSimple.
6. Sélectionnez le groupe de ressources pour vos nœuds. Pour ajouter un nouveau groupe de ressources, cliquez sur **Créer**.
7. Entrez le préfixe pour identifier les nœuds.
8. Sélectionnez l’emplacement des ressources de nœud.
9. Sélectionnez l’emplacement dédié où héberger les ressources de nœud.
10. Sélectionnez le type de nœud. Vous pouvez choisir l’[option CS28 ou CS36](cloudsimple-node.md). Cette dernière option inclut la capacité de calcul et de mémoire maximale.
11. Sélectionnez le nombre de nœuds à approvisionner.
12. Sélectionnez **Vérifier + créer**.
13. Passez en revue les paramètres. Pour modifier les paramètres, cliquez sur **Précédent**.
14. Sélectionnez **Créer**.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cloud privé et configurer l’environnement](quickstart-create-private-cloud.md)
* En savoir plus sur le [service CloudSimple](https://docs.azure.cloudsimple.com/cloudsimple-service)
