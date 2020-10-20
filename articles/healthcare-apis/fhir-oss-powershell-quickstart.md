---
title: 'PowerShell : Déployer un serveur FHIR pour Azure - API Azure pour FHIR'
description: Ce guide de démarrage rapide explique comment déployer le serveur FHIR open source de Microsoft à l’aide de PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817962"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Démarrage rapide : Déployer le serveur FHIR open source à l’aide de PowerShell

Dans ce guide de démarrage rapide, découvrez comment déployer le serveur FHIR open source pour Azure de Microsoft à l’aide de PowerShell.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Choisissez le nom du groupe de ressources qui doit contenir les ressources provisionnées, puis créez ce groupe :

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Déployer le modèle de serveur FHIR

Le [dépôt GitHub](https://github.com/Microsoft/fhir-server) de Microsoft FHIR Server pour Azure contient un modèle qui déploie toutes les ressources nécessaires. Le processus de déploiement prend plusieurs minutes, le temps que les ressources Azure requises soient créées et configurées. Déployez-le à l’aide de :

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> Si vous n’êtes pas déjà connecté, exécutez d’abord les commandes suivantes.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

Pour utiliser un groupe de ressources existant, changez les valeurs $rg dans la ligne de la définition de la variable $rg et dans la ligne de commande de déploiement du modèle Azure ARM, comme indiqué dans le code.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Vérifier que le serveur FHIR est en cours d’exécution

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Il faut environ une minute au serveur pour répondre la première fois.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le groupe de ressources en effectuant les étapes suivantes :

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé le serveur FHIR open source pour Azure de Microsoft sur votre abonnement. Pour savoir comment accéder à l’API FHIR à l’aide de Postman, effectuez le didacticiel Postman.
 
>[!div class="nextstepaction"]
>[Accédez à l’API FHIR à l’aide de Postman](access-fhir-postman-tutorial.md)
