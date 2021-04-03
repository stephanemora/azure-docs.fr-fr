---
title: Filtres de sécurité pour filtrer les résultats avec Active Directory
titleSuffix: Azure Cognitive Search
description: Découvrez comment implémenter des privilèges de sécurité au niveau du document pour les résultats de la recherche Recherche cognitive Azure, à l’aide de filtres de sécurité et d’identités Azure Active Directory (AD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97629508"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Utilisation de filtres de sécurité pour filtrer les résultats de Recherche cognitive Azure à l’aide d’identités Active Directory

Cet article explique comment utiliser les identités de sécurité Azure Active Directory (AD) avec des filtres dans Recherche cognitive Azure pour tronquer les résultats de recherche en fonction de l’appartenance à des groupes d’utilisateurs.

Cet article décrit les tâches suivantes :
> [!div class="checklist"]
> - Créer des groupes et des utilisateurs Azure AD
> - Associer l’utilisateur au groupe que vous avez créé
> - Mettre en cache les nouveaux groupes
> - Indexer les documents avec les groupes associés
> - Émettre une demande de recherche avec un filtre d’identificateurs de groupe
> 
> [!NOTE]
> Dans cet article, les exemples d’extraits de code sont écrits en C#. L’intégralité du code source est disponible [sur GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Prérequis

Votre index dans Recherche cognitive Azure doit avoir un [champ de sécurité](search-security-trimming-for-azure-search.md) pour stocker la liste des identités de groupe disposant d’un accès en lecture pour le document. Ce cas d’usage implique une correspondance exacte entre un élément sécurisable (par exemple l’application d’un établissement scolaire) et un champ de sécurité spécifiant qui a accès à cet élément (personnel en charge des admissions).

Vous devez disposer des autorisations d’administrateur Azure AD requises dans cette procédure pour créer des utilisateurs, des groupes et des associations. 

Votre application doit également être inscrite auprès d’Azure AD en tant qu’application multi-locataire, comme décrit dans la procédure suivante.

### <a name="register-your-application-with-azure-active-directory"></a>Inscrire votre application auprès d’Azure Active Directory

Cette étape intègre votre application avec Azure AD afin qu’elle puisse accepter les connexions des comptes d’utilisateur et de groupe. Si vous n’êtes pas administrateur de locataire dans votre organisation, vous devrez peut-être [créer un nouveau locataire](../active-directory/develop/quickstart-create-new-tenant.md) pour effectuer les étapes suivantes.

1. Dans [Portail Azure](https://portal.azure.com), recherchez la ressource Azure Active Directory pour votre abonnement.

1. Sur la gauche, sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez **Nouvelle inscription**.

1. Donnez un nom à l’inscription, peut-être un nom similaire au nom de l’application de recherche. Sélectionnez **Inscription**.

1. Une fois l’inscription de l’application créée, copiez l’ID d’application. Vous devrez fournir cette chaîne à votre application.

   Si vous utilisez le code [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), collez cette valeur dans le fichier **app.config**.

   Répétez l’opération pour l’ID de locataire.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID d’application dans la section Essentials":::

1. Sur la gauche, sélectionnez **Autorisations d’API**, puis **Ajouter une autorisation**. 

1. Sélectionnez **Microsoft Graph**, puis **Autorisations déléguées**.

1. Recherchez et ajoutez les autorisations déléguées suivantes :

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

Microsoft Graph fournit une API qui permet l’accès par programmation à Azure AD par le biais d’une API REST. L’exemple de code pour cette procédure utilise les autorisations pour appeler l’API Microsoft Graph pour la création de groupes, d’utilisateurs et d’associations. Les API sont également utilisées pour mettre en cache les identificateurs de groupe afin d’accélérer le filtrage.

## <a name="create-users-and-groups"></a>Créer des utilisateurs et des groupes

Si vous ajoutez une fonctionnalité de recherche à une application établie, il est possible que vous disposiez déjà d’identificateurs d’utilisateur et de groupe dans Azure AD. Dans ce cas, vous pouvez ignorer les trois étapes suivantes. 

Toutefois, si vous n’avez pas d’utilisateurs existants, vous pouvez utiliser les API Microsoft Graph pour créer des entités de sécurité. Les extraits de code suivants montrent comment générer des identificateurs qui deviennent des valeurs de données pour le champ de sécurité dans votre index Recherche cognitive Azure. Dans l’exemple de notre application d’admission scolaire, il s’agirait des identificateurs de sécurité pour le personnel en charge des admissions.

La gestion des groupes et des utilisateurs peut s’avérer très fluide, en particulier dans les grandes organisations. Le code qui génère les identités d’utilisateur et de groupe doit s’exécuter assez souvent pour tenir compte des modifications apportées aux groupes de l’organisation. De même, votre index Recherche cognitive Azure requiert une planification de mise à jour similaire pour refléter l’état actuel des utilisateurs et des ressources autorisés.

### <a name="step-1-create-group"></a>Étape 1 : [Create Group](/graph/api/group-post-groups) (Créer un groupe) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Étape 2 : [Create User](/graph/api/user-post-users) (Créer un utilisateur)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Étape 3 : Associer les utilisateurs et les groupes

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Étape 4 : Mettre en cache les identificateurs de groupe

Si vous le souhaitez, pour réduire la latence du réseau, vous pouvez mettre en cache les associations utilisateurs-groupes. Ainsi, lorsqu’une requête de recherche est émise, les groupes sont renvoyés à partir du cache, ce qui évite un aller-retour vers Azure AD. Vous pouvez utiliser l’[API Batch Azure AD](/graph/json-batching) pour envoyer une requête HTTP unique avec plusieurs utilisateurs et générer le cache.

Microsoft Graph est conçu pour gérer un volume élevé de demandes. Si un trop grand nombre de demandes sont émises, Microsoft Graph génère une erreur avec le code d’état HTTP 429. Pour plus d’informations, consultez le document [Limitation dans Microsoft Graph](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexer les documents avec leurs groupes autorisés

Les opérations de demande dans Recherche cognitive Azure sont exécutées via un index Recherche cognitive Azure. Dans cette étape, une opération d’indexation importe les données interrogeables sous forme d’index, y compris les identificateurs utilisés comme filtres de sécurité. 

Le service Recherche cognitive Azure n’authentifie pas les identités des utilisateurs et ne fournit aucune logique pour définir le contenu qu’un utilisateur est autorisé à consulter. Le cas d’usage pour le filtrage de sécurité implique que vous fournissiez l’association entre un document sensible et l’identificateur de groupe ayant accès à ce document importé sans modification dans un index de recherche. 

Dans notre exemple hypothétique, le corps de la demande PUT sur un index Recherche cognitive Azure inclurait une épreuve ou une transcription d’un candidat, ainsi que l’identificateur de groupe ayant l’autorisation d’afficher ce contenu. 

Dans l’exemple générique utilisé dans l’exemple de code pour cette procédure, l’action d’indexation peut se présenter comme suit :

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Émettre une demande de recherche

Pour des raisons de filtrage de sécurité, les valeurs du champ de sécurité dans l’index sont des valeurs statiques utilisées pour inclure ou exclure des documents dans les résultats de la recherche. Par exemple, si l’identificateur de groupe pour les admissions est « A11B22C33D44-E55F66G77-H88I99JKK », tous les documents dans un index Recherche cognitive Azure ayant cet identificateur dans le champ de sécurité sont inclus (ou exclus) dans les résultats de la recherche renvoyés au demandeur.

Pour filtrer les documents renvoyés dans les résultats de la recherche en fonction des groupes de l’utilisateur qui émet la demande, procédez comme suit.

### <a name="step-1-retrieve-users-group-identifiers"></a>Étape 1 : Récupérer les identificateurs de groupe de l’utilisateur

Si les groupes de l’utilisateur n’ont pas encore été mis en cache, ou si le cache a expiré, exécutez la requête [groups](/graph/api/directoryobject-getmembergroups).

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Étape 2 : Composer la requête de recherche

En supposant que vous connaissez les groupes de l’utilisateur, vous pouvez émettre la demande de recherche avec les valeurs de filtre appropriées.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Étape 3 : Gérer les résultats

La réponse inclut une liste répertoriant uniquement les documents que l’utilisateur est autorisé à afficher. Selon la façon dont vous construisez la page de résultats, vous pouvez inclure des repères visuels pour matérialiser le jeu de résultats obtenu.

## <a name="next-steps"></a>Étapes suivantes

Dans cette procédure pas à pas, vous avez découvert un modèle d’utilisation des connexions Azure AD pour filtrer des documents dans les résultats de Recherche cognitive Azure et ignorer les documents ne correspondant pas au filtre défini dans la requête. Pour obtenir un autre modèle plus simple, ou pour revisiter d’autres fonctionnalités de sécurité, consultez les liens suivants.

- [Filtres de sécurité pour le filtrage des résultats](search-security-trimming-for-azure-search.md)
- [Sécurité dans Recherche cognitive Azure](search-security-overview.md)