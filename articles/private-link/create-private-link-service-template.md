---
title: Modèle Resource Manager de service Azure Private Link
description: Modèle Resource Manager de service de liaison privée
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 93a66057ddb0034f7ac9ac62578292ca38f2d2fe
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84236408"
---
# <a name="create-a-private-link-service---resource-manager-template"></a>Créer un service de liaison privée – Modèle Resource Manager

Dans ce guide de démarrage rapide, vous allez utiliser un modèle Resource Manager pour créer un service de liaison privée.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Vous pouvez également suivre ce guide de démarrage rapide en utilisant le [portail Azure](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md) ou [Azure CLI](create-private-link-service-cli.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-link-service"></a>Créer un service Private Link

Ce modèle crée un service de liaison privée.

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-privatelink-service/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : service de liaison privée pour exposer en privé le service
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : point de terminaison privé pour accéder au service en privé
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) : équilibreur de charge qui expose les machines virtuelles qui hébergent le service
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : deux machines virtuelles, une qui héberge le service et l’autre pour tester la connexion au point de terminaison privé
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : extension qui installe le serveur web
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : une pour chaque machine virtuelle
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : deux IP publiques, une pour chaque machine virtuelle
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : deux interfaces réseau, une pour chaque machine virtuelle

### <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle Resource Manager sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée une machine virtuelle, un équilibreur de charge standard, un service de liaison privée, un point de terminaison privé, une mise en réseau et une machine virtuelle à valider.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Sélectionnez ou créez votre groupe de ressources.
3. Saisissez le nom d’utilisateur et le mot de passe administrateur de la machine virtuelle.
4. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis **Acheter**.

## <a name="validate-the-deployment"></a>Valider le déploiement

> [!NOTE]
> Le modèle Resource Manager génère un nom unique pour la machine virtuelle myConsumerVm<b>{uniqueid}</b> (remplacez <b>{uniqueid}</b> par la valeur générée).

### <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Connectez-vous à la machine virtuelle _myConsumerVm{uniqueid}_ via Internet comme suit :

1.  Dans la barre de recherche du portail, saisissez _myConsumerVm{uniqueid}_ .

2.  Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

3.  Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( _.rdp_) et le télécharge sur votre ordinateur.

4.  Ouvrez le fichier downloaded.rdp\*.

    a. Si vous y êtes invité, sélectionnez **Connexion**.

    b. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.
    
    > [!NOTE]
    > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

5.  Sélectionnez **OK**.

6.  Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

7.  Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.

### <a name="access-the-http-service-privately-from-the-vm"></a>Accéder au service http en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter au service http à partir de la machine virtuelle à l’aide du point de terminaison privé.

1.  Accédez au Bureau à distance de _myConsumerVm{uniqueid}_ .
2.  Ouvrez un navigateur et entrez l’adresse du point de terminaison privé http://10.0.0.5/.
3.  La page IIS par défaut s’affiche.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec le service de liaison privée, supprimez le groupe de ressources. Cette action supprime le service de liaison privée et toutes les ressources qui y sont associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Private Link](private-link-overview.md)
