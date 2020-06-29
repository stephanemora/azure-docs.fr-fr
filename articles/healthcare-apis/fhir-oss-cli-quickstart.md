---
title: 'Azure CLI : Déployer un serveur FHIR open source pour Azure - API Azure pour Azure'
description: Ce guide de démarrage rapide explique comment déployer le serveur FHIR open source pour Azure de Microsoft.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "84819974"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Démarrage rapide : Déployer le serveur FHIR open source à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous allez apprendre à déployer un serveur FHIR&reg; open source sur Azure à l’aide d’Azure CLI.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Créer un groupe de ressources

Choisissez le nom du groupe de ressources qui doit contenir les ressources provisionnées, puis créez ce groupe :

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Déployer un modèle

Le [dépôt GitHub](https://github.com/Microsoft/fhir-server) de Microsoft FHIR Server pour Azure contient un modèle qui déploie toutes les ressources nécessaires. Déployez-le à l’aide de :

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Vérifier que le serveur FHIR est en cours d’exécution

Obtenez une déclaration de capacité du serveur FHIR avec :

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Il faut environ une minute au serveur pour répondre la première fois.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le groupe de ressources en effectuant les étapes suivantes :

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé le serveur FHIR open source pour Azure de Microsoft sur votre abonnement. Pour savoir comment accéder à l’API FHIR à l’aide de Postman, effectuez le didacticiel Postman.
 
>[!div class="nextstepaction"]
>[Accédez à l’API FHIR à l’aide de Postman](access-fhir-postman-tutorial.md)