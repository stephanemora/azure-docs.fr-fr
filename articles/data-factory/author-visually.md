---
title: Création visuelle dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser la création visuelle dans Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: shlo
ms.openlocfilehash: 3db32dfe2d7b65535f7d30f76241b33ecca96c15
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957953"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Création visuelle dans Azure Data Factory
L’expérience utilisateur Azure Data Factory vous permet de créer et de déployer visuellement des ressources dans votre fabrique de données sans avoir à écrire du code. Vous pouvez glisser-déposer des activités sur le canevas d’un pipeline, effectuer des séries de tests, déboguer de manière itérative, mais aussi déployer et surveiller les exécutions de votre pipeline. Vous pouvez utiliser l’expérience utilisateur pour la création visuelle de deux manières :

- Créer directement avec le service Data Factory.
- Créer avec l’intégration Azure Repos Git pour la collaboration, le contrôle du code source et la gestion des versions.

## <a name="author-directly-with-the-data-factory-service"></a>Créer directement avec le service Data Factory
Deux éléments distinguent la création visuelle avec le service Data Factory de la création visuelle avec l’intégration Git :

- Le service Data Factory n’inclut pas un référentiel pour stocker les entités JSON de vos modifications.
- Le service Data Factory n’est pas optimisé pour la collaboration ou le contrôle de version.

![Configurer le service Data Factory ](media/author-visually/configure-data-factory.png)

Lorsque vous utilisez les **Canevas de création** UX pour une création directe avec le service Data Factory, seul le mode **Tout publier** est disponible. Les modifications que vous apportez sont publiées directement dans le service Data Factory.

![Mode Publier](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Créer avec l’intégration Azure Repos Git
La création visuelle avec l’intégration Azure Repos Git prend en charge le contrôle du code source et la collaboration pour les utiliser sur vos pipelines Data Factory. Vous pouvez associer une fabrique de données à un dépôt d’organisation Azure Repos Git pour le contrôle du code source, la collaboration, la gestion des versions, etc. Une seule organisation Azure Repos Git peut avoir plusieurs dépôts, mais un dépôt Azure Repos Git ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas de dépôt ni d’organisation Azure Repos, suivez [ces instructions](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) pour créer vos ressources.

> [!NOTE]
> Vous pouvez stocker les fichiers de scripts et de données dans un dépôt Azure Repos Git. Toutefois, vous devez charger les fichiers manuellement dans le stockage Azure. Un pipeline Data Factory ne charge pas automatiquement les fichiers de scripts ou de données stockés dans un dépôt Azure Repos Git sur le stockage Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurer un dépôt Azure Repos Git avec Azure Data Factory
Vous pouvez configurer un dépôt Azure Repos Git avec une fabrique de données via deux méthodes.

#### <a name="method1"></a> Méthode de configuration 1 (dépôt Azure Repos Git) : page Let’s get started (Prise en main)

Dans Azure Data Factory, accédez à la page **Let’s get started** (Prise en main). Sélectionnez **Configure Code Repository** (Configurer le référentiel de code) :

![Configurer un dépôt de code Azure Repos](media/author-visually/configure-repo.png)

Le volet de configuration **Paramètres du référentiel** apparaît :

![Configurer les paramètres du référentiel de code](media/author-visually/repo-settings.png)

Le volet affiche les paramètres du dépôt de code Azure Repos suivants :

| Paramètre | Description | Valeur |
|:--- |:--- |:--- |
| **Type de référentiel** | Type du dépôt de code Azure Repos.<br/>**Remarque** : GitHub n’est actuellement pas pris en charge. | Azure Repos Git |
| **Azure Active Directory** | Le nom de votre abonné Azure AD. | <your tenant name> |
| **Organisation Azure Repos** | Nom de votre organisation Azure Repos. Vous trouverez le nom de votre organisation Azure Repos sur `https://{organization name}.visualstudio.com`. Vous pouvez vous [connecter à votre organisation Azure Repos](https://www.visualstudio.com/team-services/git/) pour accéder à votre profil Visual Studio et visualiser vos dépôts et projets. | <your organization name> |
| **Nom du projet** | Nom de votre projet Azure Repos. Vous trouverez le nom de votre projet Azure Repos sur `https://{organization name}.visualstudio.com/{project name}`. | <your Azure Repos project name> |
| **RepositoryName** | Nom de votre dépôt de code Azure Repos. Les projets Azure Repos contiennent des dépôts Git pour gérer votre code source à mesure que votre projet se développe. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le projet. | <your Azure Repos code repository name> |
| **Branche de collaboration** | Votre branche de collaboration Azure Repos utilisée pour la publication. Par défaut, il s’agit de `master`. Modifiez ce paramètre au cas où vous souhaitez publier des ressources à partir d’une autre branche. | <your collaboration branch name> |
| **Dossier racine** | Votre dossier racine de votre branche de collaboration Azure Repos. | <your root folder name> |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Indique s’il convient d’importer des ressources de fabrique de données existantes à partir du **canevas de création** de l’expérience utilisateur dans un dépôt Azure Repos Git. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Méthode de configuration 2 (dépôt Azure Repos Git) : canevas de création de l’expérience utilisateur
Dans l’expérience utilisateur Azure Data Factory **Canevas de création**, recherchez votre fabrique de données. Sélectionnez le menu déroulant **Fabrique de données** et cliquez sur **Configure code repository** (Configurer le référentiel de code)à.

Un volet de configuration apparaît. Pour plus d’informations sur les paramètres de configuration, consultez les descriptions dans <a href="#method1">Méthode de configuration 1</a>.

![Configurer les paramètres du référentiel de code pour la création de l’expérience utilisateur](media/author-visually/configure-repo-2.png)

## <a name="use-a-different-azure-active-directory-tenant"></a>Utiliser un autre locataire Azure Active Directory

Vous pouvez créer un dépôt Azure Repos Git dans un autre locataire Azure Active Directory. Pour définir un autre locataire Azure AD, vous devez disposer des droits d’administrateur pour l’abonnement Azure que vous utilisez.

## <a name="use-your-personal-microsoft-account"></a>Ajouter votre compte Microsoft personnel

Pour utiliser un compte Microsoft personnel à des fins d'intégration de Git, vous pouvez lier votre référentiel Azure personnel au répertoire Active Directory de votre organisation.

1. Ajoutez votre compte Microsoft personnel au répertoire Active Directory de votre organisation en tant qu’invité. Pour plus d'informations, voir [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](../active-directory/b2b/add-users-administrator.md).

2. Connectez-vous au portail Azure avec votre compte Microsoft personnel. Basculez ensuite vers le répertoire Active Directory de votre organisation.

3. Accédez à la section Azure DevOps qui affiche désormais votre référentiel personnel. Sélectionnez le référentiel et connectez-vous à Active Directory.

Au terme de cette procédure de configuration, votre référentiel personnel est disponible lorsque vous configurez l’intégration de Git dans l’interface utilisateur de Data Factory.

Pour plus d’informations sur la connexion de référentiels Azure au répertoire Active Directory de votre organisation, voir [Connecter votre organisation Azure DevOps à Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="switch-to-a-different-git-repo"></a>Passer à un autre dépôt Git

Pour passer à un autre dépôt Git, recherchez l’icône dans le coin supérieur droit de la page Vue d’ensemble de Data Factory, comme le montre la capture d’écran suivante. Si vous ne voyez pas l’icône, effacez le cache de votre navigateur local. Sélectionnez l’icône pour supprimer l’association au dépôt actuel.

Après avoir supprimé l’association avec le dépôt actuel, vous pouvez configurer vos paramètres Git pour utiliser un autre dépôt. Vous pouvez ensuite importer des ressources Data Factory dans le nouveau dépôt.

![Supprimer l’association au référentiel Git actuel](media/author-visually/remove-repo.png)

## <a name="use-version-control"></a>Utiliser le contrôle de version
Les systèmes de contrôle de version (également appelé _contrôle du code source_) permettent aux développeurs de collaborer sur le code et de suivre les modifications apportées à la base de code. Le contrôle du code source est un outil essentiel pour les projets impliquant plusieurs développeurs.

Chaque dépôt Azure Repos Git associé à une fabrique de données comporte une branche de collaboration. (`master` est la branche de collaboration par défaut). Les utilisateurs peuvent également créer des branches de fonctionnalités en cliquant sur **+ Nouvelle branche** et en procédant à un développement dans les branches de fonctionnalités.

![Modifier le code via la synchronisation ou la publication](media/author-visually/sync-publish.png)

Lorsque vous êtes prêt pour le développement des fonctionnalités dans votre branche de fonctionnalités, vous pouvez cliquer sur **Créer une demande de tirage (pull request)**. Cette action vous dirige vers Azure Repos Git où vous pouvez augmenter les demandes de tirage, procéder à des revues du code et fusionner les modifications dans votre branche de collaboration. (`master` est la valeur par défaut). Vous êtes uniquement autorisé à publier sur le service Data Factory à partir de votre branche de collaboration. 

![Créer une nouvelle demande de tirage (pull request)](media/author-visually/create-pull-request.png)

## <a name="configure-publishing-settings"></a>Configurer les paramètres de publication

Pour configurer la branche de publication, autrement dit la branche où les modèles Resource Manager sont enregistrés, ajoutez un fichier `publish_config.json` au dossier racine dans la branche de collaboration. Data Factory lit ce fichier, recherche le champ `publishBranch` et crée une branche (si elle n’existe pas déjà) avec la valeur fournie. Il enregistre ensuite tous les modèles Resource Manager à l’emplacement spécifié. Par exemple : 

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quand vous publiez à partir du mode Git, vous pouvez confirmer que Data Factory utilise la branche de publication prévue, comme illustré dans la capture d’écran suivante :

![Confirmer la branche de publication correcte](media/author-visually/configure-publish-branch.png)

Quand vous spécifiez une nouvelle branche de publication, Data Factory ne supprime pas la branche de publication précédente. Si vous souhaitez la supprimer, faites-le manuellement.

Data Factory lit uniquement le fichier `publish_config.json` lors du chargement de la fabrique. Si la fabrique est déjà chargée sur le portail, actualisez le navigateur pour que vos modifications prennent effet.

## <a name="publish-code-changes"></a>Publier les modifications de code
Après avoir fusionné des modifications dans la branche de collaboration (`master` est la valeur par défaut), sélectionnez **Publier** pour publier manuellement les modifications de votre code dans la branche principale pour le service Data Factory.

![Publier les modifications apportées au service Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> La branche principale n’est pas représentative de ce qui est déployé dans le service Data Factory. La branche principale *doit* être publiée manuellement sur le service Data Factory.

## <a name="author-with-github-integration"></a>Créer avec l’intégration de GitHub

La création avec l’intégration de GitHub prend en charge le contrôle du code source et la collaboration pendant l’utilisation de vos pipelines de fabrique de données. Vous pouvez associer une fabrique de données à un référentiel de compte GitHub pour le contrôle du code source, la collaboration et la gestion des versions. Un compte GitHub peut avoir plusieurs référentiels, mais un référentiel GitHub ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas un compte ou un référentiel GitHub, suivez [ces instructions](https://github.com/join) pour créer vos ressources. L’intégration de GitHub avec Data Factory prend en charge aussi bien le GitHub public que GitHub Enterprise.

Pour configurer un référentiel GitHub, vous devez disposer des droits d’administrateur pour l’abonnement Azure que vous utilisez.

Pour une présentation de neuf minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Limites

- Vous pouvez stocker les fichiers de scripts et de données dans un référentiel GitHub. Toutefois, vous devez charger les fichiers manuellement dans le stockage Azure. Un pipeline Data Factory ne charge pas automatiquement les fichiers de scripts ou de données stockés dans un référentiel GitHub vers le stockage Azure.

- Les versions de GitHub Enterprise antérieures à 2.14.0 ne fonctionnent pas dans le navigateur Microsoft Edge.

- L’intégration de GitHub avec les outils de création visuelle de Data Factor ne fonctionne que dans la version généralement disponible de Data Factory.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Configurer un référentiel GitHub public avec Azure Data Factory

Vous pouvez configurer un référentiel GitHub avec une fabrique de données via deux méthodes.

**Méthode de configuration 1 (référentiel public) : page Let’s get started (Prise en main)**

Dans Azure Data Factory, accédez à la page **Let’s get started** (Prise en main). Sélectionnez **Configure Code Repository** (Configurer le référentiel de code) :

![Page de prise en main de Data Factory](media/author-visually/github-integration-image1.png)

Le volet de configuration **Paramètres du référentiel** apparaît :

![Paramètres du référentiel GitHub](media/author-visually/github-integration-image2.png)

Le volet affiche les paramètres du dépôt de code Azure Repos suivants :

| **Paramètre**                                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valeur**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Type de référentiel**                                      | Type du dépôt de code Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Compte GitHub**                                       | Le nom de votre compte GitHub. Vous pouvez trouver ce nom dans https://github.com/{account nom}/{nom du référentiel}. En naviguant sur cette page, vous êtes invité à entrer les informations d’identification GitHub OAuth sur votre compte GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Le nom de votre référentiel de code GitHub. Les comptes GitHub contiennent des référentiels Git pour gérer votre code source. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le compte.                                                                                                                                                                                                                              |                    |
| **Branche de collaboration**                                 | Votre branche de collaboration GitHub utilisée pour la publication. Maître par défaut. Modifiez ce paramètre au cas où vous souhaitez publier des ressources à partir d’une autre branche.                                                                                                                                                                                                                                                               |                    |
| **Dossier racine**                                          | Votre dossier racine de votre branche de collaboration GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Permet de spécifier s’il faut importer des ressources de fabrique de données existantes à partir de l’expérience utilisateur **Canevas de création** dans un référentiel GitHub. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |
| **Branche sur laquelle importer la ressource**                        | Indique dans quelle branche les ressources de la fabrique de données (pipelines, ensembles de données, services liés, etc.) sont importées. Vous pouvez importer des ressources dans l’une des branches suivantes : a. Collaboration b. Créer c. Utiliser l’existant                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Méthode de configuration 2 (référentiel public) : canevas de création de l’expérience utilisateur

Dans l’expérience utilisateur Azure Data Factory **Canevas de création**, recherchez votre fabrique de données. Sélectionnez le menu déroulant **Fabrique de données** et cliquez sur **Configure code repository** (Configurer le référentiel de code)à.

Un volet de configuration apparaît. Pour plus d’informations sur les paramètres de configuration, consultez les descriptions dans *Méthode de configuration 1* ci-dessus.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Configurer un référentiel GitHub Enterprise avec Azure Data Factory

Vous pouvez configurer un référentiel GitHub Enterprise avec une fabrique de données via deux méthodes.

 #### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Méthode de configuration 1 (référentiel Entreprise) : page Let’s get started (Prise en main)

Dans Azure Data Factory, accédez à la page **Let’s get started** (Prise en main). Sélectionnez **Configure Code Repository** (Configurer le référentiel de code) :

![Page de prise en main de Data Factory](media/author-visually/github-integration-image1.png)

Le volet de configuration **Paramètres du référentiel** apparaît :

![Paramètres du référentiel GitHub](media/author-visually/github-integration-image3.png)

Le volet affiche les paramètres du dépôt de code Azure Repos suivants :

| **Paramètre**                                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valeur**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Type de référentiel**                                      | Type du dépôt de code Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Utiliser GitHub Enterprise**                                | Cochez la case pour sélectionner GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **URL GitHub Enterprise**                                | L’URL racine de GitHub Enterprise. Par exemple : https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **Compte GitHub**                                       | Le nom de votre compte GitHub. Vous pouvez trouver ce nom dans https://github.com/{account nom}/{nom du référentiel}. En naviguant sur cette page, vous êtes invité à entrer les informations d’identification GitHub OAuth sur votre compte GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Le nom de votre référentiel de code GitHub. Les comptes GitHub contiennent des référentiels Git pour gérer votre code source. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le compte.                                                                                                                                                                                                                              |                    |
| **Branche de collaboration**                                 | Votre branche de collaboration GitHub utilisée pour la publication. Maître par défaut. Modifiez ce paramètre au cas où vous souhaitez publier des ressources à partir d’une autre branche.                                                                                                                                                                                                                                                               |                    |
| **Dossier racine**                                          | Votre dossier racine de votre branche de collaboration GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Permet de spécifier s’il faut importer des ressources de fabrique de données existantes à partir de l’expérience utilisateur **Canevas de création** dans un référentiel GitHub. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |
| **Branche sur laquelle importer la ressource**                        | Indique dans quelle branche les ressources de la fabrique de données (pipelines, ensembles de données, services liés, etc.) sont importées. Vous pouvez importer des ressources dans l’une des branches suivantes : a. Collaboration b. Créer c. Utiliser l’existant                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Méthode de configuration 2 (référentiel Entreprise) : canevas de création de l’expérience utilisateur

Dans l’expérience utilisateur Azure Data Factory **Canevas de création**, recherchez votre fabrique de données. Sélectionnez le menu déroulant **Fabrique de données** et cliquez sur **Configure code repository** (Configurer le référentiel de code)à.

Un volet de configuration apparaît. Pour plus d’informations sur les paramètres de configuration, consultez les descriptions dans *Méthode de configuration 1* ci-dessus.

## <a name="use-the-expression-language"></a>Utiliser le langage d’expression
Vous pouvez spécifier des expressions pour les valeurs de propriété en utilisant le langage d’expression pris en charge par Azure Data Factory.

Spécifiez des expressions pour les valeurs de propriété en sélectionnant **Ajouter du contenu dynamique**:

![Utiliser le langage d’expression](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Utiliser les fonctions et paramètres

Vous pouvez utiliser des fonctions ou spécifier des paramètres pour des pipelines et des jeux de données dans le **générateur d’expressions** Data Factory :

Pour plus d’informations sur les expressions prises en charge, consultez [Expressions et fonctions dans Azure Data Factory](control-flow-expression-language-functions.md).

![Ajouter du contenu dynamique](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Fournir des commentaires
Sélectionnez **Feedback** (Commentaire) pour donner votre avis sur les fonctionnalités ou informer Microsoft de problèmes avec l’outil :

![Commentaires](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md).
