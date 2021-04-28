---
title: Erreur de l’authentification Azure Digital Twins Explorer
description: Causes et résolutions de l’erreur « L’authentification a échoué. » dans Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 9f315a0446c1fb712558c1031df56760e6d5b7e7
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108123426"
---
# <a name="authentication-failed"></a>Échec de l'authentification.

Cet article décrit les causes et les étapes de résolution en cas de réception de l’erreur « Échec de l’authentification » lors de l’exécution de l’exemple [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) sur votre ordinateur local. 

## <a name="symptoms"></a>Symptômes

Lors de la configuration et de l’exécution de l’application Azure Digital Twins Explorer, les tentatives d’authentification auprès de l’application se heurtent au message d’erreur suivant :

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Capture d’écran d’un message d’erreur dans Azure Digital Twins Explorer avec le texte suivant : « Échec de l’authentification ». Si vous exécutez l’application localement, vérifiez que vous êtes connecté à Azure sur votre ordinateur hôte, par exemple en exécutant « az login » dans une invite de commandes, en vous connectant à Visual Studio ou VS Code ou en définissant des variables d’environnement. Si vous avez besoin de plus d’informations, consultez le fichier Lisez-moi ou recherchez DefaultAzureCredential dans la documentation Azure.Identity. Si vous exécutez adt-explorer hébergé dans le cloud, assurez-vous que votre fonction Azure d’hébergement dispose d’une identité managée affectée par le système. Pour plus d’informations, consultez le fichier Lisez-moi.":::

## <a name="causes"></a>Causes

### <a name="cause-1"></a>Cause no 1

L’application Azure Digital Twins Explorer utilise [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (qui fait partie de la bibliothèque `Azure.Identity`), qui recherche les informations d’identification dans votre environnement local.

Comme l’indique le texte de l’erreur, cette erreur peut se produire si vous n’avez pas fourni d’informations d’identification locales que `DefaultAzureCredential` puisse récupérer.

Pour plus d’informations sur l’utilisation des informations d’identification locales avec Azure Digital Twins Explorer, consultez la section [*Configurer les informations d’identification Azure locales*](./quickstart-azure-digital-twins-explorer.md#set-up-local-azure-credentials) du guide *Démarrage rapide : Explorer un exemple de scénario* d’Azure Digital Twins Explorer.

### <a name="cause-2"></a>Cause no 2

Cette erreur peut également se produire si votre compte Azure ne dispose pas des autorisations de contrôle d’accès en fonction du rôle Azure (Azure RBAC) requises définies sur votre instance Azure Digital Twins. Pour accéder aux données de votre instance, vous devez avoir le rôle **Lecteur des données Azure Digital Twins** ou **Propriétaire des données Azure Digital Twins** sur l’instance que vous essayez de lire ou de gérer, respectivement. 

Pour plus d’informations sur la sécurité et les rôles dans Azure Digital Twins, consultez [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md).

## <a name="solutions"></a>Solutions

### <a name="solution-1"></a>Solution no 1

Tout d’abord, assurez-vous que vous avez fourni les informations d’identification nécessaires à l’application.

#### <a name="provide-local-credentials"></a>Fournir les informations d’identification locales

`DefaultAzureCredential` s’authentifie auprès du service en utilisant les informations d’une connexion Azure locale. Vous pouvez fournir vos informations d’identification Azure en vous connectant à votre compte Azure dans une fenêtre [Azure CLI](/cli/azure/install-azure-cli) locale, ou dans Visual Studio ou Visual Studio Code.

Vous pouvez consulter les types d’informations d’identification que `DefaultAzureCredential` accepte, ainsi que l’ordre dans lequel ils sont tentés, dans la [documentation Azure Identity pour DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Si vous êtes déjà connecté localement au compte Azure approprié et que le problème n’est pas résolu, passez à la solution suivante.

### <a name="solution-2"></a>Solution no 2

Vérifiez que votre utilisateur Azure dispose du rôle **Lecteur des données Azure Digital Twins** sur l’instance Azure Digital Twins si vous tentez simplement de lire ses données ou du rôle **Propriétaire des données Azure Digital Twins** sur l’instance si vous essayez de gérer ses données.

Notez que ce rôle est différent…
* L’ancien nom de ce rôle pendant la préversion, *Propriétaire Azure Digital Twins (préversion)* (le rôle est le même, mais le nom a été modifié)
* du rôle *Propriétaire* sur l’ensemble de l’abonnement Azure. *Propriétaire de données Azure Digital Twins* est un rôle au sein d’Azure Digital Twins qui est étendu à cette instance individuelle d’Azure Digital Twins.
* du rôle *Propriétaire* dans Azure Digital Twins. Il s’agit de deux rôles de gestion distincts pour Azure Digital Twins et *Propriétaire de données Azure Digital Twins* est le rôle qui doit être utilisé pour la gestion.

 Si vous n’avez pas ce rôle, configurez-le pour résoudre le problème.

#### <a name="check-current-setup"></a>Vérifier la configuration actuelle

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Corriger les problèmes 

Si ce rôle ne vous est pas attribué, une personne disposant d’un rôle Propriétaire dans votre **abonnement Azure** doit exécuter la commande suivante pour donner à votre utilisateur Azure le rôle approprié sur l’**instance Azure Digital Twins**. 

Si vous êtes propriétaire de l’abonnement, vous pouvez exécuter cette commande vous-même. Dans le cas contraire, contactez un propriétaire pour qu’il l’exécute à votre place. Le nom du rôle est soit **Propriétaire des données Azure Digital Twins** pour un accès en modification, soit **Lecteur des données Azure Digital Twins** pour un accès en lecture.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Pour plus d’informations sur cette exigence de rôle et le processus d’attribution, consultez la [section *Configurer les autorisations d’accès de votre utilisateur*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) de l’article *Guide pratique : Configurer une instance et l’authentification (CLI ou portail)* .

## <a name="next-steps"></a>Étapes suivantes

Lisez les étapes de configuration relatives à la création et à l’authentification d’une nouvelle instance Azure Digital Twins :
* [*Guide pratique : Configurer une instance et l’authentification (CLI)*](how-to-set-up-instance-cli.md).

En savoir plus sur la sécurité et les autorisations sur Azure Digital Twins :
* [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md)