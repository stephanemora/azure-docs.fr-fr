---
title: Exécuter des fonctions Azure à partir d’un package | Microsoft Docs
description: Exécutez vos fonctions avec le runtime Azure Functions, en montant un fichier de package de déploiement qui contient les fichiers projet de votre application de fonction.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: glenga
ms.openlocfilehash: a0e643397372e5b132119a7c23f251ecec876916
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346575"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Exécuter des fonctions Azure à partir d’un fichier de package

> [!NOTE]
> Les fonctionnalités décrites dans cet article ne sont pas disponibles dans Functions sur Linux.

Dans Azure, vous pouvez exécuter vos fonctions directement dans un fichier de package de déploiement de votre application de fonction. L’autre option consiste à déployer vos fichiers dans le répertoire `d:\home\site\wwwroot` de votre application de fonction.

Cet article décrit les avantages que présente l’exécution de vos fonctions à partir d’un package. Il explique également comment activer cette fonctionnalité dans votre application de fonction.

## <a name="benefits-of-running-from-a-package-file"></a>Avantages de l’exécution à partir d’un fichier de package
  
L’exécution à partir d’un fichier de package présente plusieurs avantages :

+ Réduction des risques de verrouillage lors de la copie de fichiers
+ Déploiement possible sur une application de production (après redémarrage)
+ Connaissance des fichiers qui sont exécutés dans votre application
+ Amélioration des performances des [déploiements Azure Resource Manager](functions-infrastructure-as-code.md)
+ Peut réduire les temps de démarrage à froid, en particulier pour les fonctions JavaScript avec des arborescences de package npm de grande taille.

Pour plus d’informations, lisez [cette annonce](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Permettre aux fonctions de s’exécuter à partir d’un package

Pour permettre à votre application de fonction de s’exécuter à partir d’un package, il suffit d’ajouter un paramètre `WEBSITE_RUN_FROM_PACKAGE` à vos paramètres d’application de fonction. Le paramètre `WEBSITE_RUN_FROM_PACKAGE` doit avoir l’une des valeurs suivantes :

| Valeur  | Description  |
|---------|---------|
|**`<url>`**  | Emplacement d’un fichier de package que vous souhaitez exécuter. Lorsque vous utilisez le stockage Blob, vous devez utiliser un conteneur privé avec une [signature d’accès partagé (SAP)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) pour permettre au runtime Functions d’accéder au package. Vous pouvez utiliser [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour charger des fichiers de package sur votre compte Stockage Blob.         |
| **`1`**  | Lancez l’exécution à partir d’un fichier de package dans le dossier `d:\home\data\SitePackages` de votre application de fonction. Ave cette option, le dossier doit contenir également un fichier nommé `packagename.txt`. Ce fichier contient uniquement le nom du fichier de package du dossier, sans espace. |

L’exemple suivant montre une application de fonction configurée pour s’exécuter à partir d’un fichier zip hébergé dans le stockage Blob Azure :

![Paramètre d’application WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Actuellement, seuls les fichiers de package zip sont pris en charge.

## <a name="integration-with-zip-deployment"></a>Intégration au déploiement zip

[Déploiement zip][Zip deployment for Azure Functions] est une fonctionnalité Azure App Service qui vous permet de déployer votre projet d’application de fonction dans le répertoire `wwwroot`. Le projet est empaqueté dans un fichier de déploiement zip. Les mêmes API peuvent être utilisées pour déployer votre package dans le dossier `d:\home\data\SitePackages`. Quand le paramètre d’application `WEBSITE_RUN_FROM_PACKAGE` est défini sur `1`, les API de déploiement zip copient votre package dans le dossier `d:\home\data\SitePackages` au lieu d’extraire les fichiers dans `d:\home\site\wwwroot`. Le fichier `packagename.txt` est également créé. L’application de fonction est alors exécutée à partir du package après un redémarrage, et `wwwroot` devient en lecture seule. Pour plus d’informations sur le déploiement zip, consultez [Déploiement zip pour Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfrompackage-setting"></a>Ajout du paramètre WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
