---
title: Problèmes connus - Azure Digital Twins
description: Recevez de l’aide pour la reconnaissance et l’atténuation des problèmes connus avec Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 9130a3248e881c9d4e2c9bfe9017f43198d50f51
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590164"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problèmes connus dans Azure Digital Twins

Cet article fournit des informations sur les problèmes connus associés à Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>« Erreur 400 du client : Requête incorrecte » dans Cloud Shell

Les commandes dans Cloud Shell peuvent échouer par intermittence avec l’erreur « Erreur 400 du client : Requête incorrecte pour l’URL : http://localhost:50342/oauth2/token  » suivie par l’arborescence complète des appels de procédure.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Cela peut être résolu en réexécutant à nouveau la commande `az login` et en suivant les étapes de connexion suivantes.

Passée cette étape, vous devez pouvoir réexécuter la commande.

### <a name="possible-causes"></a>Causes possibles

Voici le résultat d’un problème connu dans Cloud Shell : [*L’obtention d’un jeton à partir de Cloud Shell échoue par intermittence avec l’Erreur 400 du client : Requête incorrecte*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>Attribution de rôle manquante après l’installation par script

Certains utilisateurs peuvent rencontrer des problèmes dans la partie « Attribution des rôles » de [*Guide pratique : Configurer une instance et l’authentification (procédure scriptée)* ](how-to-set-up-instance-scripted.md). Le script n’indique pas d’échec, mais le rôle *Propriétaire Azure Digital Twins (préversion)* n’est pas correctement attribué à l’utilisateur, ce qui aura un impact sur la capacité à créer d’autres ressources en déplacement.

Pour déterminer si votre attribution de rôle a été correctement configurée après l’exécution du script, suivez les instructions de la section [*Vérifier l’attribution du rôle utilisateur*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) de l’article sur l’installation. Si votre utilisateur n’est pas associé à ce rôle, ce problème vous concerne.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Pour résoudre ce problème, vous pouvez configurer manuellement votre attribution de rôle à l’aide de l’interface CLI ou du portail Azure. 

Suivez ces instructions :
* [INTERFACE DE LIGNE DE COMMANDE](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Causes possibles

Pour les utilisateurs connectés avec un [compte Microsoft (MSA)](https://account.microsoft.com/account) personnel, l’ID principal d’utilisateur qui vous identifie dans des commandes comme celle-ci peut être différent de l’e-mail de connexion de votre utilisateur, ce qui rend difficile la découverte et l’utilisation du script pour attribuer le rôle correctement.

## <a name="issue-with-interactive-browser-authentication"></a>Problème avec l’authentification interactive du navigateur

Lorsque vous écrivez du code d’authentification dans vos applications Azure Digital Twins à l’aide de la version la plus récente (version **1.2.0**) de la **bibliothèque [Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)** , vous pouvez rencontrer des problèmes avec la méthode [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet).

La méthode concernée est utilisée dans les articles suivants : 
* [*Tutoriel : Coder une application cliente*](tutorial-code.md)
* [*Guide pratique : Écrire le code d’authentification de l’application*](how-to-authenticate-client.md)
* [*Guide pratique : Utiliser les API et les Kits de développement logiciel (SDK) Azure Digital Twins*](how-to-use-apis-sdks.md)

Le problème comprend une réponse d’erreur « Azure.Identity.AuthenticationFailedException » lors de la tentative d’authentification dans une fenêtre du navigateur. La fenêtre du navigateur peut ne pas démarrer complètement ou sembler réussir à authentifier l’utilisateur, alors que l’application cliente échoue toujours avec l’erreur.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Pour résoudre ce problème, faites en sorte que vos applications utilisent explicitement la version **1.1.1** d’Azure.Identity. Avec cette version de la bibliothèque, le navigateur doit se charger et s’authentifier comme prévu.

>[!NOTE]
> Il n’est pas suffisant d’ajouter la bibliothèque sans spécifier de version, car cela aura toujours comme valeur par défaut la version **1.2.0** plus récente. Vous devez spécifier la version **1.1.1** explicitement.

### <a name="possible-causes"></a>Causes possibles

Il s’agit d’une incompatibilité entre Azure Digital Twins et la version la plus récente de la bibliothèque Azure.Identity, soit la version **1.2.0**. 

Vous verrez ce problème si vous utilisez la version **1.2.0** dans votre application, ou si vous ajoutez la bibliothèque à votre projet sans spécifier de version (car la version la plus récente est également utilisée par défaut).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la sécurité et les autorisations sur Azure Digital Twins :
* [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md)