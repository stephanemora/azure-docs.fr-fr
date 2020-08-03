---
title: 'Tutoriel : Déployer un cluster vSphere dans Azure'
description: Découvrir comment déployer un cluster vSphere dans Azure en utilisant Azure VMware Solution (AVS)
ms.topic: tutorial
ms.date: 07/15/2020
ms.openlocfilehash: 4f3b33ea401c62124ae5f8a4c881d86d2f19b40c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079415"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Tutoriel : Déployer un cloud privé AVS dans Azure

Azure VMware Solution (AVS) vous donne la possibilité de déployer un cluster vSphere dans Azure. Le déploiement initial minimal est de trois hôtes. Des hôtes supplémentaires peuvent être ajoutés un à la fois, jusqu’à un maximum de 16 hôtes par cluster. 

Comme AVS ne vous permet pas de gérer votre cloud privé avec votre vCenter local au lancement, vous devez effectuer une configuration supplémentaire pour une connexion à une instance vCenter locale, à un réseau virtuel, etc. Ces procédures et les prérequis associés sont traités dans ce tutoriel.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un cloud privé AVS
> * Vérifier le cloud privé déployé

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Droits d’administration appropriés et autorisation de créer un cloud privé.
- Vérifiez que vous disposez d’un réseau approprié configuré comme décrit dans [Tutoriel : Check-list du réseau](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

Pour utiliser AVS, vous devez d’abord inscrire le fournisseur de ressources auprès de votre abonnement.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Pour d’autres façons d’inscrire un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).


## <a name="create-a-private-cloud"></a>Créer un cloud privé

Vous pouvez créer un cloud privé AVS en utilisant le [portail Azure](#azure-portal) ou [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Créer une ressource**. Dans la zone de texte **Rechercher dans la Place de marché**, tapez `Azure VMware Solution`, puis sélectionnez **Azure VMware Solution** dans la liste. Dans la fenêtre **Azure VMware Solution**, sélectionnez **Créer**

1. Sous l’onglet **De base**, entrez des valeurs pour les champs. Le tableau suivant liste les propriétés des champs.

   | Champ   | Valeur  |
   | ---| --- |
   | **Abonnement** | Abonnement que vous prévoyez d’utiliser pour le déploiement.|
   | **Groupe de ressources** | Groupe de ressources pour vos ressources de cloud privé. |
   | **Lieu** | Sélectionnez un emplacement, comme **USA Est**.|
   | **Nom de la ressource** | Nom de votre cloud privé AVS. |
   | **Référence (SKU)** | Sélectionnez la valeur de référence SKU suivante : AV36 |
   | **Hôtes** | Nombre d’hôtes à ajouter au cluster du cloud privé. La valeur par défaut est 3. Elle peut être augmentée ou diminuée après le déploiement.  |
   | **Mot de passe de l’administrateur du vCenter** | Entrez en mot de passe d’administrateur du cloud. |
   | **Mot de passe de NSX-T Manager** | Entrez un mot de passe d’administrateur NSX-T. |
   | **Bloc d’adresses** | Entrez un bloc d’adresses IP pour le réseau CIDR pour le cloud privé, par exemple 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Créer un cloud privé" border="true":::

1. Quand vous avez terminé, sélectionnez **Vérifier + créer**. Dans l’écran suivant, vérifiez les informations entrées. Si elles sont toutes correctes, sélectionnez **Créer**.

   > [!NOTE]
   > Cette étape prend environ 2 heures. 

1. Vérifiez que déploiement a réussi. Accédez au groupe de ressources que vous avez créé et sélectionnez votre cloud privé.  Quand le déploiement est terminé, vous voyez l’état **Réussi**. 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Vérifier le cloud privé déployé" border="true":::

### <a name="azure-cli"></a>Azure CLI

Pour créer un cloud privé AVS, vous pouvez utiliser l’interface Azure CLI à l’aide d’Azure Cloud Shell au lieu du portail Azure. Il s’agit d’un interpréteur de commandes interactif gratuit dans lequel les outils Azure courants ont été préinstallés et configurés pour pouvoir être utilisés avec votre compte. 

#### <a name="open-azure-cloud-shell"></a>Ouvrir Azure Cloud Shell

Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur **Entrée** pour les exécuter.

#### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Créer un cloud privé

Spécifiez un nom de groupe de ressources, un nom pour le cloud privé, un emplacement et la taille du cluster.

| Propriété  | Description  |
| --------- | ------------ |
| **-g** (nom de groupe de ressources)     | Nom du groupe de ressources pour vos ressources de cloud privé.        |
| **-n** (nom du cloud privé)     | Nom de votre cloud privé AVS.        |
| **--location**     | Emplacement utilisé pour votre cloud privé.         |
| **--cluster-size**     | Taille du cluster La valeur minimale est 3.         |
| **--network-block**     | Bloc réseau d’adresses IP CIDR à utiliser pour votre cloud privé. Le bloc d’adresses ne doit pas chevaucher ceux utilisés dans d’autres réseaux virtuels se trouvant dans votre abonnement et des réseaux locaux.        |
| **--sku** | Valeur de référence SKU : AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Supprimer un cloud privé (portail Azure)

Si vous avez un cloud privé AVS dont vous n’avez plus besoin, vous pouvez le supprimer. Quand vous supprimez un cloud privé, tous les clusters et tous leurs composants sont supprimés.

Pour cela, accédez à votre cloud privé dans le portail Azure, puis sélectionnez **Supprimer**. Dans la page confirmation, confirmez le nom du cloud privé, puis sélectionnez **Oui**.

> [!CAUTION]
> La suppression du cloud privé est une opération irréversible. Dès lors que le cloud privé a été supprimé, les données ne peuvent pas être récupérées, car cela met fin à toutes les charges de travail en cours d’exécution et à tous les composants et détruit toutes les données et tous les paramètres de configuration du cloud privé, y compris les adresses IP publiques. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un cloud privé AVS
> * Vérifier le cloud privé déployé

Passez au tutoriel suivant pour découvrir comment créer un réseau virtuel à utiliser avec votre cloud privé dans le cadre de la configuration de la gestion locale pour vos clusters de cloud privé.

> [!div class="nextstepaction"]
> [Créer un réseau virtuel](tutorial-configure-networking.md)
