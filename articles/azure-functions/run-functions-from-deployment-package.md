---
title: Exécuter des fonctions Azure à partir d’un package | Microsoft Docs
description: Exécutez vos fonctions avec le runtime Azure Functions, en montant un fichier de package de déploiement qui contient les fichiers projet de votre application de fonction.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 2b9cc3618bf21eac268e3c25f08b80124d52e6af
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669289"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Exécuter des fonctions Azure à partir d’un fichier de package

> [!NOTE]
> Les fonctionnalités décrites dans cet article sont actuellement en préversion. Elles ne sont pas disponibles dans Functions sur Linux.

Dans Azure, vous pouvez exécuter vos fonctions directement depuis un fichier de package de déploiement de votre application de fonction. L’autre option consiste à déployer vos fichiers projet de fonction dans le répertoire `d:\home\site\wwwroot` de votre application de fonction.

Cet article décrit les avantages que présente l’exécution de vos fonctions à partir d’un package. Il explique également comment activer cette fonctionnalité dans votre application de fonction.

## <a name="benefits-of-running-from-a-package-file"></a>Avantages de l’exécution à partir d’un fichier de package
  
L’exécution à partir d’un fichier de package présente plusieurs avantages :

+ Réduction des risques de verrouillage lors de la copie de fichiers
+ Déploiement possible sur une application de production (après redémarrage)
+ Connaissance des fichiers qui sont exécutés dans votre application
+ Amélioration des performances des [déploiements Azure Resource Manager](functions-infrastructure-as-code.md)
+ Réduction du temps de démarrage à froid des fonctions JavaScript

Pour plus d’informations, lisez [cette annonce](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Permettre aux fonctions de s’exécuter à partir d’un package

Pour permettre à votre application de fonction de s’exécuter à partir d’un package, il suffit d’ajouter un paramètre `WEBSITE_RUN_FROM_ZIP` à vos paramètres d’application de fonction. Le paramètre `WEBSITE_RUN_FROM_ZIP` doit avoir l’une des valeurs suivantes :

| Valeur  | Description  |
|---------|---------|
|**`<url>`**  | Emplacement d’un fichier de package que vous souhaitez exécuter. Lorsque vous utilisez le stockage Blob, vous devez utiliser un conteneur privé avec une [signature d’accès partagé (SAP)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) pour permettre au runtime Functions d’accéder au package. Vous pouvez utiliser [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour charger des fichiers de package sur votre compte Stockage Blob.         |
| **`1`**  | Lancez l’exécution à partir d’un fichier de package dans le dossier `d:\home\data\SitePackages` de votre application de fonction. Avec cette option, le dossier doit contenir également un fichier nommé `packagename.txt`. Ce fichier contient uniquement le nom du fichier de package du dossier, sans espace. |

L’exemple suivant montre une application de fonction configurée pour s’exécuter à partir d’un fichier zip hébergé dans le stockage Blob Azure :

![Paramètre d’application WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Actuellement, seuls les fichiers de package zip sont pris en charge.

## <a name="integration-with-zip-deployment"></a>Intégration au déploiement zip

[Déploiement zip][Zip deployment for Azure Functions] est une fonctionnalité Azure App Service qui vous permet de déployer votre projet d’application de fonction dans le répertoire `wwwroot`. Le projet est empaqueté dans un fichier de déploiement zip. Les mêmes API peuvent être utilisées pour déployer votre package dans le dossier `d:\home\data\SitePackages`. Quand le paramètre d’application `WEBSITE_RUN_FROM_ZIP` est défini sur `1`, les API de déploiement zip copient votre package dans le dossier `d:\home\data\SitePackages` au lieu d’extraire les fichiers dans `d:\home\site\wwwroot`. Le fichier `packagename.txt` est également créé. L’application de fonction est alors exécutée à partir du package après un redémarrage, et `wwwroot` devient en lecture seule. Pour plus d’informations sur le déploiement zip, consultez [Déploiement zip pour Azure Functions](deployment-zip-push.md).

## <a name="adding-the-websiterunfromzip-setting"></a>Ajout du paramètre WEBSITE_RUN_FROM_ZIP

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
