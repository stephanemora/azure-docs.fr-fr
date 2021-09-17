---
title: Créer un cloud privé Azure VMware Solution
description: Étapes de création d’un cloud privé Azure VMware Solution à l’aide du portail Azure.
ms.topic: include
ms.date: 08/05/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: beae1ba1fe5cb37f9c96d411ca9ee0e8cddfe1d3
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122261729"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Vous pouvez créer un cloud privé Azure VMware Solution en utilisant le Portail Azure ou Azure CLI.


### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Créer une ressource**. 

1. Dans la zone de texte **Rechercher la Place de marché**, tapez `Azure VMware Solution`, puis sélectionnez-le dans les résultats. 

1. Dans la fenêtre **Azure VMware Solution**, sélectionnez **Créer**.

1. Dans l’onglet **Informations de base**, entrez des valeurs pour les champs, puis sélectionnez **Vérifier + créer**. 

   >[!TIP]
   >Vous avez collecté ces informations pendant la [phase de planification](../plan-private-cloud-deployment.md) de ce démarrage rapide.

   | Champ   | Valeur  |
   | ---| --- |
   | **Abonnement** | Sélectionnez l’abonnement que vous prévoyez d’utiliser pour le déploiement. Toutes les ressources d’un abonnement Azure sont facturées en même temps.|
   | **Groupe de ressources** | Sélectionnez le groupe de ressources pour votre cloud privé. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Vous pouvez également créer un nouveau groupe de ressources pour votre cloud privé. |
   | **Nom de la ressource** | Fournissez le nom de votre cloud privé Azure VMware Solution. |
   | **Lieu** | Sélectionnez un emplacement, comme **USA Est**. Il s’agit de la *région* que vous avez définie pendant la phase de planification. |
   | **Taille de l’hôte** | Sélectionnez **AV36**. |
   | **Nombre d’hôtes** | Affiche le nombre d’hôtes alloués au cluster de cloud privé. La valeur par défaut est 3. Vous pouvez l’augmenter ou la diminuer après le déploiement.  |
   | **Bloc d’adresse pour le cloud privé** | Fournissez un bloc d’adresses IP pour le cloud privé.  Le CIDR représente le réseau de gestion de cloud privé et est utilisé pour les services de gestion de clusters, tels que vCenter Server et NSX-T Manager. Utilisez l’espace d’adressage /22, par exemple 10.175.0.0/22.  L’adresse doit être unique et ne pas empiéter sur d’autres réseaux virtuels Azure et des réseaux locaux. |
   

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Capture d’écran représentant l’onglet De base dans la fenêtre Créer un cloud privé." border="true":::

1. Vérifiez les informations saisies et, si elles sont correctes, sélectionnez **Créer**.  

   > [!NOTE]
   > Cette étape prend environ 3 à 4 minutes. L’ajout d’un seul hôte dans un cluster existant ou le même cluster prend entre 30 et 45 minutes.

1. Vérifiez que déploiement a réussi. Accédez au groupe de ressources que vous avez créé et sélectionnez votre cloud privé.  Quand le déploiement est terminé, vous voyez l’état **Réussi**. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Capture d’écran montrant que le déploiement a réussi." border="true":::


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour créer un cloud privé Azure VMware Solution, vous pouvez utiliser l’interface Azure CLI à l’aide d’Azure Cloud Shell au lieu du portail Azure. Pour obtenir la liste des commandes que vous pouvez utiliser avec Azure VMware Solution, consultez [Commandes Azure VMware](/cli/azure/ext/vmware/vmware).

Pour commencer avec Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


1. Créez un groupe de ressources avec la commande ['az group create'](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

   ```azurecli-interactive
   
   az group create --name myResourceGroup --location eastus
   ```

2. Spécifiez un nom pour le groupe de ressources et le cloud privé, un emplacement et la taille du cluster.

   | Propriété  | Description  |
   | --------- | ------------ |
   | **-g** (nom de groupe de ressources)     | Nom du groupe de ressources pour vos ressources de cloud privé.        |
   | **-n** (nom du cloud privé)     | Nom de votre cloud privé Azure VMware Solution.        |
   | **--location**     | Région utilisée pour votre cloud privé.         |
   | **--cluster-size**     | Taille du cluster La valeur minimale est 3.         |
   | **--network-block**     | Bloc réseau d’adresses IP CIDR à utiliser pour votre cloud privé. Le bloc d’adresses ne doit pas chevaucher ceux utilisés dans d’autres réseaux virtuels se trouvant dans votre abonnement et des réseaux locaux.        |
   | **--sku** | Valeur de référence SKU : AV36 |

   ```azurecli-interactive 
   az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
   ```
