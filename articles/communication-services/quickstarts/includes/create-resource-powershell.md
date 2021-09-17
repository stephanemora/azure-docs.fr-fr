---
ms.openlocfilehash: 6f7a0d8169fed92564437166c74255743ea61a7f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820795"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet/).
- Installer le [module PowerShell Azure Az](/powershell/azure/)

## <a name="create-azure-communication-services-resource"></a>Créer une ressource Azure Communication Services

Pour créer une ressource Azure Communication Services, [connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli). Pour ce faire, vous pouvez utiliser le terminal et la commande ```Connect-AzAccount```, puis fournir vos informations d’identification.

Tout d’abord, assurez-vous d’installer le module Azure Communication Services ```Az.Communication``` à l’aide de la commande suivante.

```PowerShell
PS C:\> Install-Module Az.Communication
```

Exécutez la commande suivante pour créer la ressource :

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Si vous souhaitez sélectionner un abonnement particulier, vous pouvez également spécifier l’indicateur ```--subscription``` et fournir l’ID d’abonnement.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Vous pouvez configurer votre ressource Communication Services avec les options suivantes :

* Groupe de ressources
* Nom de la ressource Communication Services
* Zone géographique à laquelle associer la ressource

À l’étape suivante, vous pouvez attribuer des étiquettes à la ressource. Les étiquettes peuvent être utiles pour organiser vos ressources Azure. Pour plus d’informations sur les étiquettes, consultez la [documentation sur l’étiquetage des ressources](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Gérer la ressource Communication Services

Pour ajouter des balises à votre ressource Communication Services, exécutez les commandes suivantes : Vous pouvez également cibler un abonnement spécifique.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Pour répertorier toutes vos ressources Azure Communication Services dans un abonnement spécifique, utilisez la commande suivante :

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Pour répertorier toutes les informations d’une ressource spécifique, utilisez la commande suivante :

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
