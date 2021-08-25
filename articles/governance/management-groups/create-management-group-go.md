---
title: 'Démarrage rapide : Créer un groupe d’administration avec Go'
description: Dans ce guide de démarrage rapide, vous allez utiliser Go pour créer un groupe d’administration afin d’organiser vos ressources dans une hiérarchie de ressources.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 4949ef10d7bb38ada2faefcee1d6b104c4a08ad7
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322938"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Démarrage rapide : Créer un groupe d’administration avec Go

Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Créez ces conteneurs pour construire une hiérarchie efficace utilisable avec [Azure Policy](../policy/overview.md) et les [contrôles d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](overview.md).

La création du premier groupe d’administration dans l’annuaire peut nécessiter jusqu’à 15 minutes. En effet, des processus s’exécutent la première fois pour configurer le service des groupes d’administration dans Azure pour votre annuaire. Vous recevez une notification quand le processus est terminé. Pour plus d’informations, consultez la [configuration initiale des groupes d’administration](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

- Un principal de service Azure, y compris les _clientId_ et _clientSecret_. Si vous n’avez pas de principal de service à utiliser avec Azure Policy ou si souhaitez en créer un, consultez [Authentification des bibliothèques de gestion Azure pour .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignorez l’étape d’installation des packages .NET Core, car nous l’effectuerons dans les prochaines étapes.

- Tout utilisateur Azure AD dans le locataire peut créer un groupe d’administration sans l’autorisation d’écriture du groupe d’administration attribuée à cet utilisateur si la [protection de la hiérarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) n’est pas activée. Ce nouveau groupe d’administration devient alors un enfant du groupe d’administration racine ou le [groupe d’administration par défaut](./how-to/protect-resource-hierarchy.md#setting---default-management-group), et le rôle « Propriétaire » est attribué au créateur. Le service de groupe d’administration offre cette possibilité afin que les attributions de rôles ne soient pas nécessaires au niveau de la racine. Aucun utilisateur n’a accès au groupe d’administration racine lors de sa création. Pour éviter l’obstacle que constitue la recherche des administrateurs généraux Azure AD pour commencer à utiliser des groupes d’administration, nous autorisons la création des groupes d’administration initiaux au niveau racine.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Ajouter le package de groupes d’administration

Pour permettre à Go de gérer des groupes d’administration, le package doit être ajouté. Ce package fonctionne partout où Go peut être utilisé, notamment avec [bash sur Windows 10](/windows/wsl/install-win10) ou par le biais d’une installation locale.

1. Vérifiez que la version la plus récente de Go est installée (**1.15** au minimum). Si ce n’est pas le cas, téléchargez-la à l’adresse [Golang.org](https://golang.org/dl/).

1. Vérifiez que la version la plus récente d’Azure CLI est installée (**2.5.1** au minimum). Si ce n’est pas le cas, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI est nécessaire pour permettre à Go d’utiliser la méthode `auth.NewAuthorizerFromCLI()` dans l’exemple suivant. Pour plus d’informations sur les autres options, consultez [Azure SDK pour Go - Autres détails d’authentification](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. S’authentifier par le biais d’Azure CLI.

   ```azurecli
   az login
   ```

1. Dans l’environnement Go de votre choix, installez les packages nécessaires pour les groupes d’administration :

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resources/mgmt/2020-05-01/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Configuration des applications

Les packages Go étant ajoutés à l’environnement de votre choix, il est temps de configurer l’application Go qui peut créer un groupe d’administration.

1. Créez l’application Go et enregistrez la source suivante sous `mgCreate.go` :

   ```go
   package main

   import (
    "context"
    "fmt"
    "os"

    mg "github.com/Azure/azure-sdk-for-go/services/resources/mgmt/2020-05-01/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )

   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]

    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }

    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }

    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Générez l’application Go :

   ```bash
   go build mgCreate.go
   ```

1. Créez un groupe d’administration à l’aide de l’application Go compilée. Remplacez `<Name>` par le nom de votre nouveau groupe d’administration :

   ```bash
   mgCreate "<Name>"
   ```

Le résultat est un nouveau groupe d’administration dans le groupe d’administration racine.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez supprimer les packages installés de votre environnement Python, vous pouvez utiliser la commande suivante :

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resources/mgmt/2020-05-01/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un groupe d’administration pour organiser votre hiérarchie de ressources. Le groupe d’administration peut contenir des abonnements ou d’autres groupes d’administration.

Pour en savoir plus sur les groupes d’administration et sur la gestion de votre hiérarchie de ressources, consultez :

> [!div class="nextstepaction"]
> [Gérer vos ressources avec des groupes d’administration](./manage.md)
