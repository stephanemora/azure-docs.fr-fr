---
title: Inscrire une connexion Peering Service en préversion à l’aide d’Azure CLI
description: Découvrez comment inscrire une connexion Peering Service à l’aide d’Azure CLI.
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: fbb9a6237aafe1c1b7c94d9dd23ad4c3665c0c73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871459"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Inscrire une connexion Peering Service à l’aide d’Azure CLI.

Azure Peering Service est un service réseau qui améliore la connectivité entre le client et les services cloud de Microsoft tels qu’Office 365, Dynamics 365, les services SaaS, Azure ou les services Microsoft accessibles via le réseau Internet public. Cet article explique comment inscrire une connexion Peering Service en utilisant Azure CLI.

Si vous n’avez pas d’abonnement Azure, créez un [compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0.28 ou ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prérequis 

Vous devez disposer des éléments suivants :

### <a name="azure-account"></a>Compte Azure

Vous devez avoir un compte Microsoft Azure actif et valide. Sinon, vous ne pourrez pas configurer la connexion Peering Service. Peering Service est une ressource fournie dans les abonnements Azure.

### <a name="connectivity-provider"></a>Fournisseur de connectivité

Vous pouvez faire appel à un fournisseur de services Internet ou un partenaire d’échange Internet pour obtenir une connectivité Peering Service qui vous permettra de connecter votre réseau au réseau Microsoft.

Assurez-vous que les fournisseurs de connectivité sont des partenaires de Microsoft.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Vous connecter à votre compte Azure et sélectionner votre abonnement

Pour commencer votre configuration, connectez-vous à votre compte Azure. Si vous utilisez l’option **Essayer** de Cloud Shell, vous êtes connecté automatiquement. Utilisez les exemples suivants pour faciliter votre connexion.

```azurecli-interactive
az login
```

Vérifiez les abonnements associés au compte.

```azurecli-interactive
az account list
```

Sélectionnez l’abonnement pour lequel vous souhaitez inscrire la connexion Peering Service.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Si vous n’avez pas encore de groupe de ressources, vous devez en créer un avant d’inscrire votre connexion Peering Service. Vous pouvez créer un groupe de ressources à l’aide de la commande suivante :

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Inscrire votre abonnement auprès du fournisseur de ressources et de l’indicateur de fonctionnalité

Avant de passer aux étapes d’inscription de la connexion Peering Service à l’aide d’Azure CLI, inscrivez votre abonnement auprès du fournisseur de ressources et de l’indicateur de fonctionnalité à l’aide d’Azure CLI. Les commandes Azure CLI sont spécifiées ici :

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. Inscrire la connexion Peering Service

Inscrivez la connexion Peering Service à l’aide de l’ensemble de commandes suivant dans Azure CLI. Cet exemple inscrit la connexion Peering Service nommée myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. Inscrire le préfixe

Inscrivez le préfixe fourni par le fournisseur de connectivité en exécutant les commandes suivantes dans Azure CLI. Cet exemple inscrit le préfixe nommé myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la connexion Peering Service, consultez [Connexion Peering Service](connection.md).
- Pour en savoir plus sur la télémétrie des connexions Peering Service, consultez [Télémétrie des connexions Peering Service](connection-telemetry.md).
- Pour mesurer les données de télémétrie, consultez [Mesurer les données de télémétrie des connexions](measure-connection-telemetry.md).
- Pour inscrire la connexion par le biais d’Azure PowerShell, consultez [Inscrire une connexion Peering Service - Azure PowerShell](powershell.md).
- Pour inscrire la connexion à l’aide du portail Azure, consultez [Inscrire une connexion Peering Service - Portail Azure](azure-portal.md).
