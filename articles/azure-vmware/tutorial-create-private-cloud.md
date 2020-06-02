---
title: 'Tutoriel : Déployer un cluster vSphere dans Azure'
description: Découvrir comment déployer un cluster vSphere dans Azure en utilisant Azure VMware Solution (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fc753f43563650357cf43c102e94f0057b62a406
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873733"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Tutoriel : Déployer un cloud privé AVS dans Azure

Azure VMware Solution (AVS) vous donne la possibilité de déployer un cluster vSphere dans Azure. Le déploiement initial minimal est de trois hôtes. Des hôtes supplémentaires peuvent être ajoutés un à la fois, jusqu’à un maximum de 16 hôtes par cluster. 

Comme AVS ne vous permet pas de gérer votre cloud privé avec votre vCenter local au lancement, vous devez effectuer une configuration supplémentaire pour une connexion à une instance vCenter locale, à un réseau virtuel, etc. Ces procédures et les prérequis associés seront traités dans cette série de tutoriels.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un cloud privé AVS
> * Vérifier le cloud privé déployé

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Droits d’administration appropriés et autorisation de créer un cloud privé.
- Vérifiez que vous disposez d’un réseau approprié configuré comme décrit dans [Tutoriel : Check-list du réseau](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

Pour pouvoir utiliser la solution Azure VMware, vous devez d’abord inscrire le fournisseur de ressources. L’exemple suivant inscrit le fournisseur de ressources auprès de votre abonnement.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Pour d’autres façons d’inscrire un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Créer un cloud privé

Vous pouvez créer un cloud privé AVS en utilisant le [portail Azure](#azure-portal) ou [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, sélectionnez **+ Créer une ressource**. Dans la zone de texte **Rechercher dans la Place de marché**, tapez `Azure VMware Solution`, puis sélectionnez **Azure VMware Solution** dans la liste. Dans la fenêtre **Azure VMware Solution**, sélectionnez **Créer**

Sous l’onglet **De base**, entrez des valeurs pour les champs. Le tableau suivant contient une liste détaillée des propriétés.

| Champ   | Valeur  |
| ---| --- |
| **Abonnement** | Abonnement que vous prévoyez d’utiliser pour le déploiement.|
| **Groupe de ressources** | Groupe de ressources pour vos ressources de cloud privé. |
| **Lieu** | Sélectionnez un emplacement, comme **USA Est**.|
| **Nom de la ressource** | Nom de votre cloud privé AVS. |
| **Référence (SKU)** | Sélectionnez la valeur de référence SKU suivante : AV36 |
| **Hôtes** | Il s’agit du nombre d’hôtes à ajouter au cluster du cloud privé. La valeur par défaut est 3. Cette valeur peut être augmentée ou diminuée après le déploiement.  |
| **Mot de passe de l’administrateur du vCenter** | Entrez en mot de passe d’administrateur du cloud. |
| **Mot de passe de NSX-T Manager** | Entrez un mot de passe d’administrateur NSX-T. |
| **Bloc d’adresses** | Entrez un bloc d’adresses IP pour le réseau CIDR pour le cloud privé. Par exemple : 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Créer un cloud privé" border="true":::

Quand vous avez terminé, sélectionnez **Vérifier + créer**. Dans l’écran suivant, vérifiez les informations entrées. Si elles sont toutes correctes, sélectionnez **Créer**.

> [!NOTE]
> Cette étape prend environ 2 heures. 

### <a name="azure-cli"></a>Azure CLI

Vous pouvez aussi utiliser Azure CLI pour créer un cloud privé AVS dans Azure. Pour cela, vous pouvez utiliser Azure Cloud Shell. Les étapes suivantes vous montrent comment procéder.

#### <a name="open-azure-cloud-shell"></a>Ouvrir Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez aussi lancer Cloud Shell dans un onglet distinct du navigateur en accédant à https://shell.azure.com/bash. Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur **Entrée** pour les exécuter.

#### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Créer un cloud privé

Pour créer un cloud privé AVS, vous devez fournir un nom de groupe de ressources, un nom pour le cloud privé, un emplacement et la taille du cluster.


|Propriété  |Description  |
|---------|---------|
|Nom du groupe ressources     | Nom du groupe de ressources sur lequel vous déployez le cloud privé.        |
|Nom du cloud privé     | Nom du cloud privé.        |
|Emplacement     | Emplacement utilisé pour le cloud privé         |
|Taille du cluster     | Taille du cluster La valeur minimale est 3.         |
|Bloc réseau     | Plage CIDR à utiliser pour le cloud privé. Il est recommandé qu’il soit unique dans votre environnement local ainsi que dans votre environnement Azure.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Vérifiez que déploiement a réussi.

Accédez au groupe de ressources que vous avez créé et sélectionnez votre cloud privé : une fois le déploiement terminé, l’écran suivant s’affiche et vous voyez l’état **Réussite**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Vérifier le cloud privé déployé" border="true":::

## <a name="delete-a-private-cloud"></a>Supprimer un cloud privé

Si vous avez un cloud privé AVS dont vous êtes certain de ne plus avoir besoin, vous pouvez le supprimer. Quand vous supprimez un cloud privé, tous les clusters et tous leurs composants sont supprimés.

Pour cela, accédez à votre cloud privé dans le portail Azure, puis sélectionnez **Supprimer**. Dans la page confirmation, confirmez le nom du cloud privé, puis sélectionnez **Oui**.

> [!CAUTION]
> La suppression du cloud privé est une opération irréversible. Une fois le cloud privé supprimé, les données ne peuvent pas être récupérées, car cela met fin à toutes les charges de travail en cours d’exécution et à tous les composants, et détruit toutes les données et tous les paramètres de configuration du cloud privé, y compris les adresses IP publiques. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un cloud privé AVS
> * Vérifier le cloud privé déployé

Passez au tutoriel suivant pour découvrir comment créer un réseau virtuel à utiliser avec votre cloud privé dans le cadre de la configuration de la gestion locale pour vos clusters de cloud privé.

> [!div class="nextstepaction"]
> [Créer un réseau virtuel](tutorial-configure-networking.md)
