---
title: Contrôle de code source
description: Découvrez comment configurer le contrôle de code source dans Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 1cc5932eca520b0bbc0c592b54d36ea8b5942b08
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031627"
---
# <a name="source-control-in-azure-data-factory"></a>Contrôle de code source dans Azure Data Factory

L’expérience en matière d’interface utilisateur Azure Data Factory comporte deux expériences disponibles pour la création visuelle :

- Créer directement avec le service Data Factory
- Créer avec l’intégration GitHub ou Azure Repos Git

> [!NOTE]
> Seule la création directe avec le service Data Factory est prise en charge dans le cloud Azure Government.

## <a name="author-directly-with-the-data-factory-service"></a>Créer directement avec le service Data Factory

Lorsque vous créez directement avec le service Data Factory, le seul moyen d’enregistrer les modifications se fait via le bouton **Publier tout**. Une fois que vous avez cliqué sur le bouton, les modifications que vous apportez sont publiées directement sur le service Data Factory. 

![Mode Publier](media/author-visually/data-factory-publish.png)

La création directe avec le service Data Factory présente les limitations suivantes :

- Le service Data Factory n’inclut pas un référentiel pour stocker les entités JSON de vos modifications.
- Le service Data Factory n’est pas optimisé pour la collaboration ou le contrôle de version.

> [!NOTE]
> La création directe avec le service Data Factory est désactivée dans l’expérience en matière d’interface utilisateur Azure Data Factory quand un dépôt Git est configuré. Les modifications peuvent être apportées directement au service via PowerShell ou un kit SDK.

## <a name="author-with-azure-repos-git-integration"></a>Créer avec l’intégration Azure Repos Git

La création visuelle avec l’intégration Azure Repos Git prend en charge le contrôle du code source et la collaboration pour les utiliser sur vos pipelines Data Factory. Vous pouvez associer une fabrique de données à un dépôt d’organisation Azure Repos Git pour le contrôle du code source, la collaboration, la gestion des versions, etc. Une seule organisation Azure Repos Git peut avoir plusieurs dépôts, mais un dépôt Azure Repos Git ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas de dépôt ni d’organisation Azure Repos, suivez [ces instructions](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) pour créer vos ressources.

> [!NOTE]
> Vous pouvez stocker les fichiers de scripts et de données dans un dépôt Azure Repos Git. Toutefois, vous devez charger les fichiers manuellement dans le stockage Azure. Un pipeline Data Factory ne charge pas automatiquement les fichiers de scripts ou de données stockés dans un dépôt Azure Repos Git sur le stockage Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurer un dépôt Azure Repos Git avec Azure Data Factory

Vous pouvez configurer un dépôt Azure Repos Git avec une fabrique de données via deux méthodes.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Méthode de configuration 1 : Page d’accueil Azure Data Factory

Dans la page d’accueil Azure Data Factory, sélectionnez **Set up Code Repository** (Configurer le dépôt de code).

![Configurer un dépôt de code Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Méthode de configuration 2 : Canevas de création de l’expérience utilisateur
Dans la zone de travail de création de l’expérience en matière d’interface utilisateur Azure Data Factory, sélectionnez le menu déroulant **Data Factory**, puis **Set up Code Repository** (Configurer le dépôt de code).

![Configurer les paramètres du référentiel de code pour la création de l’expérience utilisateur](media/author-visually/configure-repo-2.png)

Les deux méthodes ouvrent le volet de configuration des paramètres du dépôt.

![Configurer les paramètres du référentiel de code](media/author-visually/repo-settings.png)

Le volet de configuration affiche les paramètres du dépôt de code Azure Repos suivants :

| Paramètre | Description | Valeur |
|:--- |:--- |:--- |
| **Type de référentiel** | Type du dépôt de code Azure Repos.<br/> | Azure DevOps Git ou GitHub |
| **Azure Active Directory** | Le nom de votre abonné Azure AD. | `<your tenant name>` |
| **Organisation Azure Repos** | Nom de votre organisation Azure Repos. Vous trouverez le nom de votre organisation Azure Repos sur `https://{organization name}.visualstudio.com`. Vous pouvez vous [connecter à votre organisation Azure Repos](https://www.visualstudio.com/team-services/git/) pour accéder à votre profil Visual Studio et visualiser vos dépôts et projets. | `<your organization name>` |
| **Nom du projet** | Nom de votre projet Azure Repos. Vous trouverez le nom de votre projet Azure Repos sur `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | Nom de votre dépôt de code Azure Repos. Les projets Azure Repos contiennent des dépôts Git pour gérer votre code source à mesure que votre projet se développe. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le projet. | `<your Azure Repos code repository name>` |
| **Branche de collaboration** | Votre branche de collaboration Azure Repos utilisée pour la publication. Par défaut, il s’agit de `master`. Modifiez ce paramètre au cas où vous souhaitez publier des ressources à partir d’une autre branche. | `<your collaboration branch name>` |
| **Dossier racine** | Votre dossier racine de votre branche de collaboration Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Indique s’il convient d’importer des ressources de fabrique de données existantes à partir du **canevas de création** de l’expérience utilisateur dans un dépôt Azure Repos Git. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |
| **Branche sur laquelle importer la ressource** | Indique dans quelle branche les ressources de la fabrique de données (pipelines, ensembles de données, services liés, etc.) sont importées. Vous pouvez importer des ressources dans l’une des branches suivantes : a. Collaboration b. Créer c. Utiliser l’existant |  |

> [!NOTE]
> Si vous utilisez Microsoft Edge et que vous ne voyez aucune valeur dans la liste déroulante de votre compte Azure DevOps, ajoutez https://*.visualstudio.com à la liste des sites de confiance.

### <a name="use-a-different-azure-active-directory-tenant"></a>Utiliser un autre locataire Azure Active Directory

Vous pouvez créer un dépôt Azure Repos Git dans un autre locataire Azure Active Directory. Pour définir un autre locataire Azure AD, vous devez disposer des droits d’administrateur pour l’abonnement Azure que vous utilisez.

### <a name="use-your-personal-microsoft-account"></a>Ajouter votre compte Microsoft personnel

Pour utiliser un compte Microsoft personnel à des fins d'intégration de Git, vous pouvez lier votre référentiel Azure personnel au répertoire Active Directory de votre organisation.

1. Ajoutez votre compte Microsoft personnel au répertoire Active Directory de votre organisation en tant qu’invité. Pour plus d'informations, voir [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](../active-directory/b2b/add-users-administrator.md).

2. Connectez-vous au portail Azure avec votre compte Microsoft personnel. Basculez ensuite vers le répertoire Active Directory de votre organisation.

3. Accédez à la section Azure DevOps qui affiche désormais votre référentiel personnel. Sélectionnez le référentiel et connectez-vous à Active Directory.

Au terme de cette procédure de configuration, votre référentiel personnel est disponible lorsque vous configurez l’intégration de Git dans l’interface utilisateur de Data Factory.

Pour plus d’informations sur la connexion de référentiels Azure au répertoire Active Directory de votre organisation, voir [Connecter votre organisation Azure DevOps à Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Créer avec l’intégration de GitHub

La création avec l’intégration de GitHub prend en charge le contrôle du code source et la collaboration pendant l’utilisation de vos pipelines de fabrique de données. Vous pouvez associer une fabrique de données à un référentiel de compte GitHub pour le contrôle du code source, la collaboration et la gestion des versions. Un compte GitHub peut avoir plusieurs référentiels, mais un référentiel GitHub ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas de compte ou de référentiel GitHub, suivez  [ces instructions](https://github.com/join)  pour créer vos ressources.

L’intégration de GitHub à Data Factory prend à la fois en charge le service GitHub public ([https://github.com](https://github.com)) que GitHub Enterprise. Vous pouvez utiliser des référentiels GitHub publics et privés avec Data Factory à condition de disposer des autorisations de lecture et d'écriture pour ceux-ci dans GitHub.

Pour configurer un dépôt GitHub, vous devez disposer des droits d’administrateur pour l’abonnement Azure que vous utilisez.

Pour une présentation de neuf minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configurer un dépôt GitHub avec Azure Data Factory

Vous pouvez configurer un référentiel GitHub avec une fabrique de données via deux méthodes.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Méthode de configuration 1 : Page d’accueil Azure Data Factory

Dans la page d’accueil Azure Data Factory, sélectionnez **Set up Code Repository** (Configurer le dépôt de code).

![Configurer un dépôt de code Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Méthode de configuration 2 : Canevas de création de l’expérience utilisateur

Dans la zone de travail de création de l’expérience en matière d’interface utilisateur Azure Data Factory, sélectionnez le menu déroulant **Data Factory**, puis **Set up Code Repository** (Configurer le dépôt de code).

![Configurer les paramètres du référentiel de code pour la création de l’expérience utilisateur](media/author-visually/configure-repo-2.png)

Les deux méthodes ouvrent le volet de configuration des paramètres du dépôt.

![Paramètres du référentiel GitHub](media/author-visually/github-integration-image2.png)

Le volet de configuration affiche les paramètres du dépôt GitHub suivants :

| **Paramètre** | **Description**  | **Valeur**  |
|:--- |:--- |:--- |
| **Type de référentiel** | Type du dépôt de code Azure Repos. | GitHub |
| **Utiliser GitHub Enterprise** | Cochez la case pour sélectionner GitHub Enterprise | non sélectionné (par défaut) |
| **URL GitHub Enterprise** | L’URL racine de GitHub Enterprise. Par exemple : https://github.mydomain.com. Obligatoire uniquement si l’option **Utiliser GitHub Enterprise** est sélectionnée | `<your GitHub enterprise url>` |                                                           
| **Compte GitHub** | Le nom de votre compte GitHub. Vous trouverez ce nom dans https:\//github.com/{nom du compte}/{nom du référentiel}. En naviguant sur cette page, vous êtes invité à entrer les informations d’identification GitHub OAuth sur votre compte GitHub. | `<your GitHub account name>` |
| **Nom du dépôt**  | Le nom de votre référentiel de code GitHub. Les comptes GitHub contiennent des référentiels Git pour gérer votre code source. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le compte. | `<your repository name>` |
| **Branche de collaboration** | Votre branche de collaboration GitHub utilisée pour la publication. Maître par défaut. Modifiez ce paramètre au cas où vous souhaitez publier des ressources à partir d’une autre branche. | `<your collaboration branch>` |
| **Dossier racine** | Votre dossier racine de votre branche de collaboration GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Indique s’il faut importer des ressources de fabrique de données existantes à partir de la zone de travail de création de l’expérience en matière d’interface utilisateur dans un dépôt GitHub. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |
| **Branche sur laquelle importer la ressource** | Indique dans quelle branche les ressources de la fabrique de données (pipelines, ensembles de données, services liés, etc.) sont importées. Vous pouvez importer des ressources dans l’une des branches suivantes : a. Collaboration b. Créer c. Utiliser l’existant |  |

### <a name="known-github-limitations"></a>Limitations connues de GitHub

- Vous pouvez stocker les fichiers de scripts et de données dans un référentiel GitHub. Toutefois, vous devez charger les fichiers manuellement dans le stockage Azure. Un pipeline Data Factory ne charge pas automatiquement les fichiers de scripts ou de données stockés dans un référentiel GitHub vers le stockage Azure.

- Les versions de GitHub Enterprise antérieures à 2.14.0 ne fonctionnent pas dans le navigateur Microsoft Edge.

- L’intégration de GitHub aux outils de création visuelle Data Factory ne fonctionne que dans la version généralement disponible de Data Factory.

- Un maximum de 1 000 entités par type de ressource (par exemple, des pipelines et des jeux de données) peut être extrait à partir d’une seule branche GitHub. Si cette limite est atteinte, il est suggéré de fractionner vos ressources en fabriques distinctes. Azure DevOps Git n’a pas cette limitation.

## <a name="switch-to-a-different-git-repo"></a>Passer à un autre dépôt Git

Pour passer à un autre dépôt Git, cliquez sur l’icône **Paramètres de dépôt Git** dans le coin supérieur droit de la page Vue d’ensemble de Data Factory. Si vous ne voyez pas l’icône, effacez le cache de votre navigateur local. Sélectionnez l’icône pour supprimer l’association au dépôt actuel.

![Icône Git](media/author-visually/remove-repo.png)

Une fois que le volet Paramètres du dépôt s’affiche, sélectionnez **Supprimer Git**. Entrez le nom de votre fabrique de données, puis cliquez sur **Confirmer** pour supprimer le dépôt Git associé à votre fabrique de données.

![Supprimer l’association au référentiel Git actuel](media/author-visually/remove-repo2.png)

Après avoir supprimé l’association avec le dépôt actuel, vous pouvez configurer vos paramètres Git pour utiliser un autre dépôt, puis importer des ressources Data Factory dans le nouveau dépôt. 

## <a name="version-control"></a>Gestion de versions

Les systèmes de contrôle de version (également appelé _contrôle du code source_) permettent aux développeurs de collaborer sur le code et de suivre les modifications apportées à la base de code. Le contrôle du code source est un outil essentiel pour les projets impliquant plusieurs développeurs.

### <a name="creating-feature-branches"></a>Création de branches de fonctionnalités

Chaque dépôt Azure Repos Git associé à une fabrique de données comporte une branche de collaboration. (`master` est la branche de collaboration par défaut). Les utilisateurs peuvent également créer des branches de fonctionnalités en cliquant sur **+ Nouvelle branche** dans la liste déroulante des branches. Une fois le volet de la nouvelle branche affiché, entrez le nom de votre branche de fonctionnalités.

![Créer une branche](media/author-visually/new-branch.png)

Lorsque vous êtes prêt à fusionner les modifications de votre branche de fonctionnalités dans votre branche de collaboration, cliquez sur la liste déroulante des branches et sélectionnez **Créer la demande de tirage (pull request)** . Cette action vous dirige vers Azure Repos Git où vous pouvez augmenter les demandes de tirage, procéder à des revues du code et fusionner les modifications dans votre branche de collaboration. (`master` est la valeur par défaut). Vous êtes uniquement autorisé à publier sur le service Data Factory à partir de votre branche de collaboration. 

![Créer une nouvelle demande de tirage (pull request)](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurer les paramètres de publication

Pour configurer la branche de publication, autrement dit la branche où les modèles Resource Manager sont enregistrés, ajoutez un fichier `publish_config.json` au dossier racine dans la branche de collaboration. Data Factory lit ce fichier, recherche le champ `publishBranch` et crée une branche (si elle n’existe pas déjà) avec la valeur fournie. Il enregistre ensuite tous les modèles Resource Manager à l’emplacement spécifié. Par exemple :

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Quand vous spécifiez une nouvelle branche de publication, Data Factory ne supprime pas la branche de publication précédente. Si vous souhaitez la supprimer, faites-le manuellement.

> [!NOTE]
> Data Factory lit uniquement le fichier `publish_config.json` lors du chargement de la fabrique. Si la fabrique est déjà chargée sur le portail, actualisez le navigateur pour que vos modifications prennent effet.

### <a name="publish-code-changes"></a>Publier les modifications de code

Après avoir fusionné des modifications dans la branche de collaboration (`master` est la valeur par défaut), cliquez sur **Publier** pour publier manuellement les modifications de votre code dans la branche principale pour le service Data Factory.

![Publier les modifications apportées au service Data Factory](media/author-visually/publish-changes.png)

Un volet latéral s’ouvre, dans lequel vous confirmez que la branche de publication et les modifications en attente sont correctes. Une fois que vous avez vérifié vos modifications, cliquez sur **OK** pour confirmer la publication.

![Confirmer la branche de publication correcte](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> La branche principale n’est pas représentative de ce qui est déployé dans le service Data Factory. La branche principale *doit* être publiée manuellement sur le service Data Factory.

## <a name="advantages-of-git-integration"></a>Avantages de l’intégration Git

-   **Contrôle du code source**. Lorsque vos charges de travail de fabrique de données sont cruciales, vous pouvez intégrer votre fabrique avec Git bénéficier de plusieurs avantages de contrôle source tels que les suivants :
    -   Possibilité de suivre/auditer les modifications.
    -   Possibilité d’annuler les modifications qui ont introduit de bogues.
-   **Sauvegardes partielles**. Si vous apportez de nombreuses modifications à votre fabrique, vous constaterez qu’en mode LIVE classique, il vous est impossible d'enregistrer vos modifications en tant que brouillon, car vous n’êtes pas prêt ou ne souhaitez pas perdre vos modifications en cas de panne de votre ordinateur. Avec l’intégration Git, vous pouvez continuer d'enregistrer vos modifications de façon incrémentielle et publier dans la fabrique lorsque vous êtes prêt uniquement. Git sert d'emplacement intermédiaire à votre travail pour vous permettre de bien tester vos modifications.
-   **Collaboration et contrôle**. Si plusieurs membres de votre équipe participent à la même fabrique, vous pouvez leur permettre de collaborer via un processus de vérification du code. Vous pouvez également configurer votre fabrique de manière à ce qu'aucun contributeur ne soit autorisé à y effectuer un déploiement. Certains membres de l’équipe seront uniquement autorisés à apporter des modifications via Git, et d'autres à « publier » les modifications dans la fabrique.
-   **Affichage des différences**. En mode Git, vous pouvez constater une différence de la charge utile sur le point d'être publiée dans la fabrique. Cette différence correspond à toutes les ressources/entités modifiées/ajoutées/supprimées depuis la dernière publication dans la fabrique. En fonction de cette différence, vous pouvez continuer de publier ou revenir en arrière et vérifier vos modifications, puis y revenir plus tard.
-   **Intégration continue/Déploiement continu améliorés**. En mode Git, vous pouvez configurer le déclenchement automatique de votre pipeline de mise en production dès que des modifications sont apportées à la fabrique de développement. Vous pouvez également personnaliser les propriétés de votre fabrique disponibles en tant que paramètres dans le modèle Resource Manager. Cela permet de ne conserver que les propriétés requises en tant que paramètres, et de coder en dur tout le reste.
-   **Meilleures performances**. Une fabrique moyenne se charge dix fois plus vite en mode Git qu’en mode LIVE classique, car les ressources sont téléchargées via Git.

## <a name="best-practices-for-git-integration"></a>Meilleures pratiques d'intégration Git

### <a name="permissions"></a>Autorisations

En règle générale, vous ne souhaitez pas autoriser tous les membres de l’équipe à mettre à jour la fabrique. Les paramètres des autorisations suivants sont recommandés :

*   Tous les membres de l’équipe doivent disposer d'autorisations de lecture dans la fabrique de données.
*   Seul un ensemble sélectionné de personnes doit être autorisé à publier sur la fabrique. Pour ce faire, elles doivent avoir le rôle **Contributeurs de Data Factory** sur la fabrique. Pour plus d’informations sur les autorisations, consultez [Rôles et autorisations pour Azure Data Factory](concepts-roles-permissions.md).
   
Il est recommandé de ne pas autoriser les archivages directs dans la branche de collaboration. Cette restriction peut aider à éviter les bogues, car chaque archivage passe par un processus de demande de tirage décrit dans [Création de branches de fonctionnalités](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Utilisation de mots de passe à partir d’Azure Key Vault

Il est recommandé d’utiliser Azure Key Vault pour stocker les chaînes de connexion ou mots de passe des services liés Data Factory. Pour des raisons de sécurité, nous ne stockons pas ces informations secrètes dans Git, si bien que toutes les modifications apportées aux services liés sont publiées immédiatement sur le service Azure Data Factory.

L’utilisation de Key Vault facilite également l’intégration et le déploiement continus, car vous n’aurez pas à fournir ces secrets lors du déploiement du modèle Resource Manager.

## <a name="troubleshooting-git-integration"></a>Résolution des problèmes d’intégration Git

### <a name="stale-publish-branch"></a>Branche de publication obsolète

Si la branche de publication n’est pas synchronisée avec la branche principale et contient des ressources obsolètes malgré une publication récente, essayez d’effectuer les étapes suivantes :

1. Supprimez votre dépôt Git actuel
1. Reconfigurez Git avec les mêmes paramètres, mais vérifiez que l’option**Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le dépôt) est sélectionnée et choisissez **Nouvelle branche**
1. Créez une demande de tirage pour fusionner les modifications apportées à la branche de collaboration 

Voici quelques exemples de situations qui peuvent provoquer une branche de publication obsolète :
- Un utilisateur a plusieurs branches. Dans une branche de fonctionnalité, l’utilisateur a supprimé un service lié qui n’est pas associé à AKV (les services liés non-AKV sont publiés immédiatement, qu’ils soient dans Git ou non) et n’a jamais fusionné la branche de fonctionnalité dans la branche de collaboration.
- Un utilisateur a modifié la fabrique de données à l’aide du SDK ou de PowerShell.
- Un utilisateur a déplacé toutes les ressources vers une nouvelle branche et a essayé de publier pour la première fois. Les services liés doivent être créés manuellement au moment de l’importation des ressources.
- Un utilisateur charge manuellement un service lié non-AKV ou un fichier JSON Integration Runtime. Il fait référence à cette ressource à partir d’une autre ressource telle qu’un jeu de données, un service lié ou un pipeline. Un service lié non-AKV créé par le biais de l’expérience utilisateur est publié immédiatement parce que les informations d’identification doivent être chiffrées. Si vous chargez un jeu de données qui fait référence à ce service lié et essayez de le publier, l’expérience utilisateur autorise cette opération puisqu’il existe dans l’environnement git. Il est rejeté au moment de la publication dans la mesure où il n’existe pas dans le service de fabrique de données.

## <a name="provide-feedback"></a>Fournir des commentaires
Sélectionnez **Feedback** (Commentaire) pour donner votre avis sur les fonctionnalités ou informer Microsoft de problèmes avec l’outil :

![Commentaires](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md).
* Pour implémenter l’intégration et le déploiement continus, consultez [Intégration et livraison continues (CI/CD) dans Azure Data Factory](continuous-integration-deployment.md).
