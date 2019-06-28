---
title: Se connecter à Azure Allemagne avec PowerShell | Microsoft Docs
description: Informations sur la gestion de votre abonnement dans Azure Germany avec PowerShell
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 38ff91fe9ac50a85d684895d0ccb6333f6257284
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033546"
---
# <a name="connect-to-azure-germany-by-using-powershell"></a>Se connecter à Azure Allemagne avec PowerShell
Pour utiliser Azure PowerShell avec Azure Allemagne, vous devez vous connecter à Azure Allemagne au lieu d’Azure global. Vous pouvez utiliser Azure PowerShell pour gérer un grand abonnement via un script ou accéder à des fonctionnalités qui ne sont pas actuellement disponibles dans le Portail Azure. Si vous avez utilisé PowerShell dans Azure global, tout est très similaire. Les éléments qui diffèrent dans Azure Allemagne sont les suivants :

* Connexion à votre compte
* Noms de régions

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Si vous n’avez pas encore utilisé PowerShell, consultez la [Présentation d’Azure PowerShell](/powershell/azure/overview).

Lorsque vous démarrez PowerShell, vous devez indiquer à Azure PowerShell de se connecter à Azure Allemagne en spécifiant un paramètre d’environnement. Ce paramètre garantit que PowerShell se connecte aux points de terminaison corrects. La collection de points de terminaison est déterminée lorsque vous vous connectez à votre compte. Différentes API requièrent différentes versions du commutateur Environnement :

| Type de connexion | Commande |
| --- | --- |
| Commandes [Azure (modèle de déploiement classique)](/powershell/azure) |`Add-AzureAccount -Environment AzureGermanCloud` |
| Commandes [Azure (modèle de déploiement Resource Manager)](/powershell/azure) |`Connect-AzAccount -EnvironmentName AzureGermanCloud` |
| Commandes [Azure Active Directory (modèle de déploiement classique)](/previous-versions/azure/jj151815(v=azure.100)) |`Connect-MsolService -AzureEnvironment AzureGermanyCloud` |
| Commandes [Resource Manager (modèle de déploiement classique)](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureGermanyCloud` |

Vous pouvez également utiliser le commutateur `Environment` lors de la connexion à un compte de stockage à l’aide de `New-AzStorageContext` et spécifier `AzureGermanCloud`.

## <a name="determining-region"></a>Détermination de la région
Une fois connecté, il existe une différence supplémentaire : les régions utilisées pour cibler un service. Chaque service cloud Azure a des régions différentes. Celles-ci sont répertoriées sur la page relative à la disponibilité du service. Vous utilisez en général la région dans le paramètre `Location` pour une commande.


| Nom courant | Nom complet | Nom de l’emplacement |
| --- | --- | --- |
| Centre de l’Allemagne |`Germany Central` | `germanycentral` |
| Nord-Est de l’Allemagne |`Germany Northeast` | `germanynortheast` |


> [!NOTE]
> Comme avec PowerShell pour Azure global, vous pouvez utiliser le nom complet ou le nom de l’emplacement pour le paramètre `Location`.
>
>

Si vous voulez valider les régions disponibles dans Azure Allemagne, vous pouvez exécuter les commandes suivantes et imprimer la liste actuelle. Pour les déploiements classiques, utilisez la première commande. Pour les déploiements Resource Manager, utilisez la deuxième commande.

    Get-AzureLocation
    Get-AzLocation

Si vous êtes curieux de connaître les environnements disponibles dans Azure, vous pouvez exécuter la commande suivante :

    Get-AzureEnvironment
    Get-AzEnvironment

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la connexion à Azure Allemagne, consultez les ressources suivantes :

* [Se connecter à Azure Allemagne avec Azure CLI](./germany-get-started-connect-with-cli.md)
* [Se connecter à Azure Allemagne avec Visual Studio](./germany-get-started-connect-with-vs.md)
* [Se connecter à Azure Allemagne avec le Portail Azure](./germany-get-started-connect-with-portal.md)




