---
title: Installer ou mettre à jour manuellement des extensions de liaison d’Azure Functions
description: Découvrez comment installer ou mettre à jour des extensions de liaison d’Azure Functions pour les applications de fonction déployées.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: 83ae2cdd16bce0b0a5b11b8c24e996306453a7a9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977486"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Installer ou mettre à jour manuellement des extensions de liaison d’Azure Functions à partir du portail

À compter de la version 2.x, le runtime Azure Functions utilise des extensions de liaison afin d’implémenter du code pour des déclencheurs et des liaisons. Les extensions de liaison sont fournies dans des packages NuGet. Pour inscrire une extension, vous installez essentiellement un package. Lorsque vous développez des fonctions, la façon d’installer des extensions de liaison dépend de l’environnement de développement. Pour plus d’informations, consultez [Inscrire des extensions de liaison](./functions-bindings-register.md) dans l’article des liaisons et des déclencheurs.

Parfois, vous devez installer ou mettre à jour manuellement vos extensions de liaison dans le portail Azure. Par exemple, vous devrez peut-être mettre à jour une liaison inscrite vers une version plus récente. Vous devrez peut-être également inscrire une liaison prise en charge qui ne peut pas être installée dans l’onglet **Intégrer** du portail.

## <a name="install-a-binding-extension"></a>Installer une extension de liaison

Utilisez les étapes suivantes pour installer ou mettre à jour des extensions manuellement à partir du portail.

1. Dans le [portail Azure](https://portal.azure.com), recherchez votre application de fonction et sélectionnez-la. Choisissez l’onglet **Vue d’ensemble** et sélectionnez **Arrêter**.  L’arrêt de l’application de fonction déverrouille les fichiers afin que les modifications puissent être effectuées.

1. Choisissez l’onglet **Fonctionnalités de la plateforme** et sous **Outils de développement**, sélectionnez **Outils avancés (Kudu)** . Le point de terminaison Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) est ouvert dans une nouvelle fenêtre.

1. Dans la fenêtre Kudu, sélectionnez **Console de débogage** > **CMD**.  

1. Dans la fenêtre de commande, accédez à `D:\home\site\wwwroot` et cliquez sur l’icône Supprimer à côté de `bin` pour supprimer le dossier. Sélectionnez **OK** pour confirmer la suppression.

1. Cliquez sur l’icône modifier à côté du fichier `extensions.csproj` qui définit les extensions de liaison pour l’application de fonction. Le fichier de projet est ouvert dans l’éditeur en ligne.

1. Faites les ajouts et les mises à jour nécessaires des éléments **PackageReference** dans **ItemGroup**, puis sélectionnez **Enregistrer**. Vous trouverez la liste actuelle des versions de package prises en charge dans l’article wiki [De quels packages ai-je besoin ?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need). Les trois liaisons de stockage Azure nécessitent le package Microsoft.Azure.WebJobs.Extensions.Storage.

1. À partir du dossier `wwwroot`, exécutez la commande suivante pour régénérer les ensembles référencés dans le dossier `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. De retour dans l’onglet **Vue d’ensemble** du portail, choisissez **Démarrer** pour redémarrer l’application de fonction.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
