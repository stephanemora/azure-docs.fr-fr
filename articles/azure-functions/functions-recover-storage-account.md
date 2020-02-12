---
title: Comment résoudre les problèmes du runtime d’Azure Functions est inaccessible.
description: Découvrez comment résoudre les problèmes relatifs à un compte de stockage non valide.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963884"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Comment résoudre les messages « functions runtime is unreachable »

Cet article vise à résoudre le message d’erreur « Le runtime Functions est inaccessible » lorsqu’il est affiché dans le Portail Azure. Lorsque cette erreur se produit, vous voyez s’afficher la chaîne d’erreur suivante dans le portail.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Cela se produit lorsque le runtime d’Azure Functions ne peut pas démarrer. Cette erreur se produit le plus souvent lorsque l’application de fonction perd l’accès à son compte de stockage. Pour plus d’informations, consultez [Exigences pour le compte de stockage](storage-considerations.md#storage-account-requirements).

Le reste de cet article vous aide à détecter le problème pour les causes suivantes de cette erreur, notamment la façon d’identifier et de résoudre chaque cas.

+ [Compte de stockage supprimé](#storage-account-deleted)
+ [Paramètres d’application du compte de stockage supprimés](#storage-account-application-settings-deleted)
+ [Informations d’identification du compte de stockage non valides](#storage-account-credentials-invalid)
+ [Compte de stockage inaccessible](#storage-account-inaccessible)
+ [Quota d’exécution quotidien dépassé](#daily-execution-quota-full)
+ [Votre application se trouve derrière un pare-feu](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Compte de stockage supprimé

Chaque application de fonction nécessite un compte de stockage afin de fonctionner. Si ce compte est supprimé, votre fonction ne s’exécutera pas.

### <a name="how-to-find-your-storage-account"></a>Comment trouver votre compte de stockage

Démarrez en recherchant le nom de votre compte de stockage dans les paramètres d’application. `AzureWebJobsStorage` ou `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` contiendra le nom de votre compte de stockage encapsulé dans une chaîne de connexion. En savoir plus sur les informations plus précises aux [références de paramètres d’application ici](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Recherchez votre compte de stockage dans le portail Azure pour voir s’il existe toujours. S’il a été supprimé, vous devez recréer un compte de stockage et remplacer vos chaînes de connexion de stockage. Votre code de fonction est perdu et vous devrez à nouveau le redéployer.

## <a name="storage-account-application-settings-deleted"></a>Paramètres d’application du compte de stockage supprimés

Dans l’étape précédente, si vous ne disposiez pas d’une chaîne de connexion à un compte de stockage, il est probable qu’elle ait été supprimée ou écrasée. La suppression des paramètres de l’application s’effectue le plus souvent en utilisant des emplacements de déploiement ou des scripts Azure Resource Manager pour définir les paramètres de l’application.

### <a name="required-application-settings"></a>Paramètres de l’application obligatoires

* Obligatoire
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obligatoire pour les fonctions du Plan de consommation
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[En savoir plus sur ces paramètres d’application ici](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Assistance

* N’activez pas le « paramètre d’emplacement » pour ces paramètres. Lorsque vous échangez les emplacements de déploiement, l’application de fonction s’arrête.
* Ne modifiez pas ces paramètres dans le cadre de déploiements automatisés.
* Ces paramètres doivent être fournis et valides au moment de la création. Un déploiement automatisé sans ces paramètres produit une application de fonction qui ne s’exécute pas et ce, même si les paramètres sont ajoutés ultérieurement.

## <a name="storage-account-credentials-invalid"></a>Informations d’identification du compte de stockage invalides

Les chaînes de connexion de compte de stockage ci-dessus doivent être mises à jour si vous régénérez les clés de stockage. [En savoir plus sur la gestion des clés de stockage ici](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Compte de stockage inaccessible

Votre application de fonction doit être en mesure d’accéder au compte de stockage. Les problèmes courants qui bloquent l’accès d’une Function à un compte de stockage sont :

+ Des applications de fonction déployées sur des App Service Environments (ASE) sans les règles de réseau appropriées pour autoriser le trafic vers et depuis le compte de stockage.

+ Le pare-feu du compte de stockage est activé et non configuré pour autoriser le trafic vers et depuis des fonctions. Pour plus d’informations, consultez [Configurer les pare-feu et les réseaux virtuels dans le Stockage Azure](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Quota d’exécution quotidien atteint

Si vous avez configuré un quota d’exécution quotidien, votre application de fonction sera temporairement désactivée et la plupart des commandes du portail seront indisponibles. 

+ Pour vérifier dans le [Portail Azure](https://portal.azure.com), ouvrez **Fonctionnalités de la plateforme** > **Paramètres de l’application de fonction** dans votre application de fonction. Lorsque vous aurez dépassé le **quota d’utilisation quotidien** que vous avez défini, le message suivant s’affiche :

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Pour résoudre ce problème, supprimez ou augmentez le quota quotidien et redémarrez votre application. Dans le cas contraire, l’exécution de votre application sera bloquée jusqu’au jour suivant.

## <a name="app-is-behind-a-firewall"></a>Application située derrière un pare-feu

Le runtime de votre fonction sera inaccessible si votre application de fonction est hébergée dans un [environnement ASE de charge équilibrée en interne](../app-service/environment/create-ilb-ase.md) et si elle est configurée pour bloquer le trafic Internet entrant, ou si des [restrictions d’adresse IP entrantes](functions-networking-options.md#inbound-ip-restrictions) sont configurées pour bloquer l’accès à Internet. Le portail Azure appelle directement l’application en cours d’exécution pour extraire la liste des fonctions, et il effectue un appel HTTP vers le point de terminaison KUDU. Les paramètres définis au niveau de la plateforme sous l’onglet `Platform Features` seront toujours disponibles.

Pour vérifier votre configuration ASE, accédez au groupe de sécurité réseau du sous-réseau où se trouve l’environnement ASE, puis validez les règles de trafic entrant pour autoriser le trafic provenant de l’adresse IP publique de l’ordinateur sur lequel vous accédez à l’application. Vous pouvez également utiliser le portail à partir d’un ordinateur connecté au réseau virtuel où est exécutée votre application, ou à partir d’une machine virtuelle exécutée sur votre réseau virtuel. [En savoir plus sur la configuration des règles de trafic entrant](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la supervision de vos applications de fonction :

> [!div class="nextstepaction"]
> [Superviser Azure Functions](functions-monitoring.md)
