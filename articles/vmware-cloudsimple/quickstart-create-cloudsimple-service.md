---
title: La VMware Solution Azure par CloudSimple Quickstart - créer le service
description: Apprenez à créer le service CloudSimple, l’achat de nœuds et la réserve de nœuds
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577569"
---
# <a name="quickstart---create-service"></a>Guide de démarrage rapide - créer de service

Pour commencer, créez la VMware Solution Azure par CloudSimple dans le portail Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solution de VMware à CloudSimple - vue d’ensemble du Service

Le service CloudSimple vous permet de vous permettent de consommer la Solution de VMware Azure par CloudSimple.  Création du service vous permet d’acheter des nœuds, réserve de nœuds et créez des clouds privés.  Vous ajoutez le service de CloudSimple dans chaque région Azure où le service CloudSimple est disponible.  Le service définit le réseau de périmètre de la Solution de VMware Azure par CloudSimple.  Ce réseau de périmètre est utilisé pour les services qui incluent la connectivité VPN, ExpressRoute et Internet pour vos clouds privés.

Pour ajouter le service CloudSimple, vous devez créer un sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et nécessite un sous-réseau/28 bloc CIDR. L’espace d’adressage de sous-réseau passerelle doit être unique. Il ne peut pas se chevaucher avec vos espaces d’adressage réseau sur site ou un espace d’adressage de réseau virtuel Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Activer le fournisseur de ressources Microsoft.VMwareCloudSimple

Suivez les étapes ci-dessous pour activer le fournisseur de ressources pour le service de CloudSimple.

1. Sélectionnez **Tous les services**.
2. Recherchez et sélectionnez **abonnements**.

    ![Sélectionner des abonnements](media/cloudsimple-service-select-subscriptions.png)

3. Sélectionnez l’abonnement sur lequel vous souhaitez activer le service de CloudSimple
4. Cliquez sur **fournisseurs de ressources** pour l’abonnement
5. Utilisez **Microsoft.VMwareCloudSimple** pour filtrer le fournisseur de ressources
6. Sélectionnez le **Microsoft.VMwareCloudSimple** fournisseur de ressources, puis cliquez sur **inscrire**

    ![S’inscrire auprès du fournisseur de ressources](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Créer le service

>[!NOTE]
> Service de CloudSimple doit être activé sur votre abonnement. Si votre abonnement n’est pas activée, vous recevrez une erreur lorsque vous tentez de créer le service.  Suivez les étapes de [CloudSimple activer service](https://docs.azure.cloudsimple.com/enable-cloudsimple-service) article pour activer le service.

1. Sélectionnez **Tous les services**.
2. Recherchez **CloudSimple Service**.

    ![Service de recherche CloudSimple](media/create-cloudsimple-service-search.png)

3. Sélectionnez **CloudSimple Services**.
4. Cliquez sur **ajouter** pour créer un nouveau service.

    ![Ajouter un Service de CloudSimple](media/create-cloudsimple-service-add.png)

5. Sélectionnez l’abonnement dans lequel vous souhaitez créer le service CloudSimple.
6. Sélectionnez le groupe de ressources pour le service. Pour ajouter un nouveau groupe de ressources, cliquez sur **créer un nouveau**.
7. Entrez un nom pour identifier le service.
8. Entrez la CIDR pour la passerelle de service. Spécifiez un sous-réseau/28 sous-réseau qui ne chevauche aucune de vos sous-réseaux locaux, sous-réseaux Azure ou les sous-réseaux CloudSimple planifiés. Vous ne pouvez pas modifier la CIDR que le service est créé.

    ![Création du service CloudSimple](media/create-cloudsimple-service.png)

9. Cliquez sur **OK**.

Le service est créé et ajouté à la liste des services.

## <a name="purchase-nodes"></a>Acheter des nœuds

Pour configurer un paiement-sous-vous accédez capacité pour un environnement de Cloud privé de CloudSimple, tout d’abord configurer des nœuds dans le portail Azure.

1. Sélectionnez **Tous les services**.
2. Recherchez **CloudSimple nœuds**.

    ![Rechercher les nœuds CloudSimple](media/create-cloudsimple-node-search.png)

3. Sélectionnez **CloudSimple nœuds**.
4. Cliquez sur **ajouter** pour créer des nœuds.

    ![Ajouter des nœuds de CloudSimple](media/create-cloudsimple-node-add.png)

5. Sélectionnez l’abonnement dans lequel vous voulez acheter CloudSimple nœuds.
6. Sélectionnez le groupe de ressources pour les nœuds. Pour ajouter un nouveau groupe de ressources, cliquez sur **créer un nouveau**.
7. Entrez le préfixe pour identifier les nœuds.
8. Sélectionnez l’emplacement pour les ressources de nœud.
9. Sélectionnez l’emplacement dédié pour héberger les ressources du nœud.
10. Sélectionnez le type de nœud. Vous pouvez choisir le [option CS28 ou CS36](cloudsimple-node.md). Cette dernière option inclut la capacité de calcul et de mémoire maximale.
11. Sélectionnez le nombre de nœuds à approvisionner.
12. Sélectionnez **Vérifier + créer**.
13. Passez en revue les paramètres. Pour modifier les paramètres, cliquez sur **précédent**.
14. Sélectionnez **Créer**.

## <a name="next-steps"></a>Étapes suivantes

* [Cloud privé de créer et configurer l’environnement](quickstart-create-private-cloud.md)
* En savoir plus sur [CloudSimple service](https://docs.azure.cloudsimple.com/cloudsimple-service)
