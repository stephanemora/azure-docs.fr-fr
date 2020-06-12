---
title: Modèle ARM de point de terminaison privé Azure
description: En savoir plus sur Azure Private Link
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: a91415e7e3d91c2950cc4df2235c3d58df284cc0
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235981"
---
# <a name="create-a-private-endpoint---resource-manager-template"></a>Créer un point de terminaison privé : modèle Resource Manager

Dans ce guide de démarrage rapide, vous allez utiliser un modèle Resource Manager pour créer un point de terminaison privé.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Vous pouvez également suivre ce guide de démarrage rapide en utilisant le [portail Azure](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md) ou [Azure CLI](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-private-endpoint"></a>Créer un point de terminaison privé

ce modèle crée un point de terminaison privé pour un serveur SQL Azure.

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-private-endpoint-sql/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : point de terminaison privé pour accéder au serveur SQL Azure en privé
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : utilisé pour résoudre l’adresse IP du point de terminaison privé
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : Pour associer le point de terminaison privé à une zone DNS privée
- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers) : Serveur SQL Azure SQL avec un exemple de base de données
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases) : Exemple de base de données
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Réseau virtuel sur lequel le point de terminaison privé est déployé
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : Adresse IP publique nécessaire pour accéder à la machine virtuelle
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : Machine virtuelle pour tester la connexion avec le point de terminaison privé au serveur SQL Azure
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : Interface réseau pour la machine virtuelle

### <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle Resource Manager sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée le point de terminaison privé, le serveur SQL Azure, l’infrastructure réseau et la machine virtuelle à valider.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Sélectionnez ou créez votre groupe de ressources.
3. Saisissez le nom d’utilisateur et le mot de passe administrateur du serveur SQL
4. Saisissez le nom d’utilisateur et le mot de passe administrateur de la machine virtuelle.
5. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis **Acheter**. Le déploiement peut prendre 20 minutes ou plus.

## <a name="validate-the-deployment"></a>Valider le déploiement

> [!NOTE]
> Le modèle ARM génère un nom unique pour la ressource de la machine virtuelle myVm<b>{uniqueId}</b> et pour celle du serveur SQL Azure sqlserver<b>{uniqueId}</b>, remplacez <b>{uniqueId}</b> par la valeur générée.

### <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Connectez-vous à la machine virtuelle _myVm{uniqueid}_ via Internet comme suit :

1. Dans la barre de recherche du portail, saisissez _myVm{uniqueid}_ .

2. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

3. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( _.rdp_) et le télécharge sur votre ordinateur.

4. OuvrOuvrez le fichier downloaded.rdp\*.

   a. Si vous y êtes invité, sélectionnez **Connexion**.

   b. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

      > [!NOTE]
      > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

5. Sélectionnez **OK**.

6. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

7. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.

### <a name="access-sql-database-server-privately-from-the-vm"></a>Accéder au serveur de base de données SQL en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter au serveur SQL Database à partir de la machine virtuelle à l’aide de Private Endpoint.

1.  Dans le Bureau à distance de _myVM{uniqueid}_ , ouvrez PowerShell.
2.  Saisissez nslookup sqlserver{uniqueid}.database.windows.net  Vous recevrez un message similaire à celui-ci :

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Installer SQL Server Management Studio
4.  Dans Se connecter au serveur, entrez ou sélectionnez les informations suivantes : Type de serveur : Sélectionnez Moteur de base de données.
    Nom du serveur : Sélectionnez sqlserver{uniqueid}.database.windows.net Nom d’utilisateur : Entrez le nom d’utilisateur fourni lors de la création.
    Mot de passe : Entrez le mot de passe fourni lors de la création.
    Mémorisez le mot de passe : Sélectionnez Oui.

5.  Sélectionnez **Connecter**.
6.  Parcourez **Bases de données** à partir du menu de gauche.
7.  (Facultatif) Créez ou interrogez des informations à partir de _sample-db_
8.  Fermez la connexion du Bureau à distance à _myVm{uniqueid}_ .

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec le point de terminaison privé, supprimez le groupe de ressources. Cette action supprime le point de terminaison privé et toutes les ressources qui y sont associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Private Link](private-link-overview.md)
