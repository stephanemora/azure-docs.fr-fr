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
ms.date: 08/14/2018
ms.author: shlo
ms.openlocfilehash: b457d1ae01e523ac99c6171fa8d2123023ebcd2c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2018
ms.locfileid: "42140251"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Création visuelle dans Azure Data Factory
L’expérience utilisateur Azure Data Factory vous permet de créer et de déployer visuellement des ressources dans votre fabrique de données sans avoir à écrire du code. Vous pouvez glisser-déposer des activités sur le canevas d’un pipeline, effectuer des séries de tests, déboguer de manière itérative, mais aussi déployer et surveiller les exécutions de votre pipeline. Vous pouvez utiliser l’expérience utilisateur pour la création visuelle de deux manières :

- Créer directement avec le service Data Factory.
- Créer avec Visual Studio Team Services (VSTS) Git Integration pour la collaboration, le contrôle du code source ou la gestion des versions.

## <a name="author-directly-with-the-data-factory-service"></a>Créer directement avec le service Data Factory
Deux éléments distinguent la création visuelle avec le service de Data Factory diffère de la création visuelle avec VSTS :

- Le service Data Factory n’inclut pas un référentiel pour stocker les entités JSON de vos modifications.
- Le service Data Factory n’est pas optimisé pour la collaboration ou le contrôle de version.

![Configurer le service Data Factory ](media/author-visually/configure-data-factory.png)

Lorsque vous utilisez les **Canevas de création** UX pour une création directe avec le service Data Factory, seul le mode **Tout publier** est disponible. Les modifications que vous apportez sont publiées directement dans le service Data Factory.

![Mode Publier](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Créer avec VSTS Git Integration
La création avec VSTS Git Integration prend en charge le contrôle du code source et la collaboration pendant l’utilisation de vos pipelines de fabrique de données. Vous pouvez associer une fabrique de données à un référentiel de compte VSTS Git pour le contrôle du code source, la collaboration, la gestion des versions, etc. Un compte VSTS Git peut avoir plusieurs référentiels, mais un référentiel VSTS Git ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas un compte ou un référentiel VSTS, suivez [ces instructions](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) pour créer vos ressources.

> [!NOTE]
> Vous pouvez stocker les fichiers de scripts et de données dans un référentiel Git VSTS. Toutefois, vous devez charger les fichiers manuellement dans le stockage Azure. Un pipeline Data Factory ne charge pas automatiquement les fichiers de scripts ou de données stockés dans un référentiel Git VSTS vers le stockage Azure.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Configurer un référentiel Git VSTS avec Azure Data Factory
Vous pouvez configurer un référentiel VSTS Git avec une fabrique de données via deux méthodes.

#### <a name="method1"></a> Méthode de configuration 1 (référentiel VSTS Git) : page Let’s get started (Prise en main)

Dans Azure Data Factory, accédez à la page **Let’s get started** (Prise en main). Sélectionnez **Configure Code Repository** (Configurer le référentiel de code) :

![Configurer un référentiel de code VSTS](media/author-visually/configure-repo.png)

Le volet de configuration **Paramètres du référentiel** apparaît :

![Configurer les paramètres du référentiel de code](media/author-visually/repo-settings.png)

Le volet affiche les paramètres du référentiel de code VSTS suivants :

| Paramètre | Description | Valeur |
|:--- |:--- |:--- |
| **Type de référentiel** | Le type de référentiel de code VSTS.<br/>**Remarque** : GitHub n’est actuellement pas pris en charge. | Git Visual Studio Team Services |
| **Azure Active Directory** | Le nom de votre abonné Azure AD. | <your tenant name> |
| **Compte Visual Studio Team Services** | Le nouveau de votre compte VSTS. Vous pouvez rechercher le nom de votre compte VSTS à l’adresse `https://{account name}.visualstudio.com`. Vous pouvez vous [connecter à votre compte VSTS](https://www.visualstudio.com/team-services/git/) pour accéder à votre profil Visual Studio et visualiser vos référentiels et projets. | <your account name> |
| **Nom du projet** | Le nom de votre projet VSTS. Vous pouvez rechercher le nom de votre projet VSTS à l’adresse `https://{account name}.visualstudio.com/{project name}`. | <your VSTS project name> |
| **RepositoryName** | Le nom de votre référentiel de code VSTS. Les projets VSTS contiennent des référentiels Git pour gérer votre code source, à mesure que votre projet se développe. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le projet. | <your VSTS code repository name> |
| **Branche de collaboration** | Votre branche de collaboration VSTS utilisée pour la publication. Par défaut, il s’agit de `master`. Modifiez ce paramètre au cas où vous souhaitez publier des ressources à partir d’une autre branche. | <your collaboration branch name> |
| **Dossier racine** | Votre dossier racine de votre branche de collaboration VSTS. | <your root folder name> |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Permet de spécifier s’il faut importer des ressources de fabrique de données existantes à partir de l’expérience utilisateur **Canevas de création** dans un référentiel Git VSTS. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |

#### <a name="configuration-method-2--vsts-git-repo-ux-authoring-canvas"></a>Méthode de configuration 2 (référentiel VSTS Git) : Canevas de création de l’expérience utilisateur
Dans l’expérience utilisateur Azure Data Factory **Canevas de création**, recherchez votre fabrique de données. Sélectionnez le menu déroulant **Fabrique de données** et cliquez sur **Configure code repository** (Configurer le référentiel de code)à.

Un volet de configuration apparaît. Pour plus d’informations sur les paramètres de configuration, consultez les descriptions dans <a href="#method1">Méthode de configuration 1</a>.

![Configurer les paramètres du référentiel de code pour la création de l’expérience utilisateur](media/author-visually/configure-repo-2.png)

## <a name="use-a-different-azure-active-directory-tenant"></a>Utiliser un autre locataire Azure Active Directory

Vous pouvez créer un référentiel VSTS Git dans un autre locataire Azure Active Directory. Pour définir un autre locataire Azure AD, vous devez disposer des droits d’administrateur pour l’abonnement Azure que vous utilisez.

## <a name="switch-to-a-different-git-repo"></a>Passer à un autre dépôt Git

Pour passer à un autre dépôt Git, recherchez l’icône dans le coin supérieur droit de la page Vue d’ensemble de Data Factory, comme le montre la capture d’écran suivante. Si vous ne voyez pas l’icône, effacez le cache de votre navigateur local. Sélectionnez l’icône pour supprimer l’association au dépôt actuel.

Après avoir supprimé l’association avec le dépôt actuel, vous pouvez configurer vos paramètres Git pour utiliser un autre dépôt. Vous pouvez ensuite importer des ressources Data Factory dans le nouveau dépôt.

![Supprimer l’association au référentiel Git actuel](media/author-visually/remove-repo.png)

## <a name="use-version-control"></a>Utiliser le contrôle de version
Les systèmes de contrôle de version (également appelé _contrôle du code source_) permettent aux développeurs de collaborer sur le code et de suivre les modifications apportées à la base de code. Le contrôle du code source est un outil essentiel pour les projets impliquant plusieurs développeurs.

Chaque dépôt VSTS Git associé à une fabrique de données comporte une branche de collaboration. (`master` est la branche de collaboration par défaut). Les utilisateurs peuvent également créer des branches de fonctionnalités en cliquant sur **+ Nouvelle branche** et en procédant à un développement dans les branches de fonctionnalités.

![Modifier le code via la synchronisation ou la publication](media/author-visually/sync-publish.png)

Lorsque vous êtes prêt pour le développement des fonctionnalités dans votre branche de fonctionnalités, vous pouvez cliquer sur **Créer une demande de tirage (pull request)**. Cette action vous dirigera vers Git VSTS où vous pouvez augmenter les demandes de tirage, procéder à des révisions du code et fusionner les modifications dans votre branche de collaboration. (`master` est la valeur par défaut). Vous êtes uniquement autorisé à publier sur le service Data Factory à partir de votre branche de collaboration. 

![Créer une nouvelle demande de tirage (pull request)](media/author-visually/create-pull-request.png)

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

Le volet affiche les paramètres du référentiel de code VSTS suivants :

| **Paramètre**                                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valeur**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Type de référentiel**                                      | Le type de référentiel de code VSTS.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
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

Le volet affiche les paramètres du référentiel de code VSTS suivants :

| **Paramètre**                                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valeur**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Type de référentiel**                                      | Le type de référentiel de code VSTS.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
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
