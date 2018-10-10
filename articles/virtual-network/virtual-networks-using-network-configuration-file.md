---
title: Configurer un réseau virtuel Azure (Classic) - Fichier config réseau | Microsoft Docs
description: Découvrez comment créer et modifier des réseaux virtuels (Classic) en exportant, modifiant et important un fichier config réseau.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 5267ce5c50e8a57843f0bc54165289b38013ad91
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986113"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurer un réseau virtuel (Classic) à l’aide d’un fichier config réseau
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle de déploiement Resource Manager.

Vous pouvez créer et configurer un réseau virtuel (classique) avec un fichier de configuration réseau à l’aide de l’interface de ligne de commande (CLI) Azure Classic ou d’Azure PowerShell. Vous ne pouvez pas utiliser de fichier de configuration de réseau pour créer ou modifier un réseau virtuel via le modèle de déploiement Azure Resource Manager. Vous ne pouvez pas utiliser le portail Azure pour créer ou modifier un réseau virtuel (Classic) à l’aide d’un fichier config réseau, mais vous pouvez utiliser le portail Azure pour créer un réseau virtuel (Classic) sans l’aide d’un fichier config réseau.

La création et la configuration d’un réseau virtuel (Classic) avec un fichier config réseau requièrent l’exportation, la modification et l’importation du fichier.

## <a name="export"></a>Exporter un fichier config réseau

Vous pouvez utiliser PowerShell ou l’interface Azure Classic CLI pour exporter un fichier de configuration réseau. PowerShell exporte un fichier XML tandis que l’interface Azure Classic CLI exporte un fichier JSON.

### <a name="powershell"></a>PowerShell
 
1. [Installez Azure PowerShell et connectez-vous à Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Modifiez le répertoire (et assurez-vous qu’il existe) ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour exporter le fichier config réseau :

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure Classic CLI

1. [Installez l’interface Azure Classic CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Effectuez les étapes restantes à partir d’une invite de commandes de l’interface CLI classique.
2. Connectez-vous à Azure en entrant la commande `azure login`.
3. Assurez-vous que vous vous trouvez bien en mode ASM par le biais de la commande `azure config mode asm`.
4. Modifiez le répertoire (et assurez-vous qu’il existe) ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour exporter le fichier config réseau :
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Créer ou modifier un fichier config réseau

Un fichier de configuration réseau est un fichier XML (quand vous utilisez PowerShell) ou un fichier JSON (quand vous utilisez l’interface CLI classique). Vous pouvez modifier le fichier dans n’importe quel éditeur de texte, ou XML/JSON. L’article [Network configuration file schema settings](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Paramètres de schéma de fichier config réseau) inclut des informations détaillées pour tous les paramètres. Pour obtenir des explications supplémentaires sur les paramètres, consultez [Afficher des réseaux virtuels et des paramètres](manage-virtual-network.md#view-virtual-networks-and-settings). Les modifications apportées au fichier :

- Doivent respecter le schéma, sans quoi l’importation du fichier config réseau échouera.
- Remplacent les paramètres réseau existants de votre abonnement : soyez donc très vigilant lors de vos modifications. Par exemple, servez-vous des exemples de fichier config réseau qui suivent. Supposons que le fichier d’origine contienne deux instances **VirtualNetworkSite** et que vous l’avez modifié comme indiqué dans les exemples. Lorsque vous importez le fichier, Azure supprime le réseau virtuel de l’instance **VirtualNetworkSite** que vous avez supprimée dans le fichier. Ce scénario simplifié suppose qu’aucune ressource ne se trouve dans le réseau virtuel, car dans le cas contraire, le réseau virtuel ne pourrait pas être supprimé, et l’importation échouerait.

> [!IMPORTANT]
> Azure considère tout sous-réseau qui comprend des éléments déployés comme étant **« en cours d'utilisation »**. Une fois le sous-réseau utilisé, il ne peut pas être modifié. Avant toute modification des informations de sous-réseau dans un fichier config réseau, déplacez tout ce que vous avez déployé sur le sous-réseau vers un autre sous-réseau qui n’est pas en cours de modification. Consultez [Déplacer une machine virtuelle ou une instance de rôle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md) pour en savoir plus.

### <a name="example-xml-for-use-with-powershell"></a>Exemple de code XML pour une utilisation avec PowerShell

L’exemple de fichier config réseau suivant crée un réseau virtuel nommé *myVirtualNetwork* avec un espace d’adressage *10.0.0.0/16* dans la région Azure *USA Est*. Le réseau virtuel contient un sous-réseau nommé *mySubnet* avec le préfixe d’adresse *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Si le fichier config réseau que vous avez exporté ne comporte aucun contenu, vous pouvez copier le code XML de l’exemple précédent et le coller dans un nouveau fichier.

### <a name="example-json-for-use-with-the-classic-cli"></a>Exemple JSON pour une utilisation avec l’interface CLI classique

L’exemple de fichier config réseau suivant crée un réseau virtuel nommé *myVirtualNetwork* avec un espace d’adressage *10.0.0.0/16* dans la région Azure *USA Est*. Le réseau virtuel contient un sous-réseau nommé *mySubnet* avec le préfixe d’adresse *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Si le fichier config réseau que vous avez exporté ne comporte aucun contenu, vous pouvez copier le code JSON de l’exemple précédent et le coller dans un nouveau fichier.

## <a name="import"></a>Importer un fichier config réseau

Vous pouvez utiliser PowerShell ou l’interface CLI classique pour importer un fichier de configuration réseau. PowerShell importe un fichier XML tandis que l’interface CLI classique importe un fichier JSON. Si l’importation échoue, vérifiez que le fichier est compatible avec le [schéma de configuration réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installez Azure PowerShell et connectez-vous à Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Modifiez le répertoire ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour importer le fichier config réseau :
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure Classic CLI

1. [Installez l’interface Azure Classic CLI](/cli/azure/install-cli-version-1.0.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Effectuez les étapes restantes à partir d’une invite de commandes de l’interface CLI classique.
2. Connectez-vous à Azure en entrant la commande `azure login`.
3. Assurez-vous que vous vous trouvez bien en mode ASM par le biais de la commande `azure config mode asm`.
4. Modifiez le répertoire ainsi que le nom de fichier dans la commande suivante, selon vos besoins, puis exécutez la commande pour importer le fichier config réseau :

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
