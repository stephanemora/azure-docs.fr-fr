---
title: Déployer des stratégies personnalisées avec GitHub Actions
titleSuffix: Azure AD B2C
description: Découvrez comment déployer des stratégies personnalisées Azure AD B2C dans un pipeline CI/CD à l’aide de GitHub Actions.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ce060e9e251313b93930200a73dde9b747c2d75
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037365"
---
# <a name="deploy-custom-policies-with-github-actions"></a>Déployer des stratégies personnalisées avec GitHub Actions

La fonctionnalité [GitHub Actions](https://docs.github.com/actions/quickstart) permet de créer des flux de travail d’intégration continue (CI) et de déploiement continu (CD) personnalisés directement dans votre dépôt GitHub. Cet article explique comment automatiser le déploiement de [stratégies personnalisées](user-flow-overview.md) Azure Active Directory B2C (Azure AD B2C) à l’aide de GitHub Actions.

Pour automatiser le processus de déploiement de stratégie personnalisée, utilisez des [stratégies personnalisées d’action GitHub pour le déploiement d’Azure AD B2C](https://github.com/marketplace/actions/deploy-azure-ad-b2c-custom-policy). Cette action GitHub a été développée par la [Communauté Azure AD B2C](https://github.com/azure-ad-b2c/deploy-trustframework-policy). 

Cette action déploie des stratégies personnalisées Azure AD B2C dans votre locataire Azure AD B2C à l’aide de l’[API Microsoft Graph](/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true). Si la stratégie n’existe pas encore dans votre locataire, elle est créée. Autrement, elle est remplacée.

> [!IMPORTANT]
> La gestion de stratégies personnalisées Azure AD B2C avec Azure Pipelines utilise actuellement des opérations en **préversion** disponibles sur le point de terminaison `/beta` de l’API Microsoft Graph. L’utilisation de ces API dans les applications de production n’est pas prise en charge. Pour plus d’informations, voir la [référence du point de terminaison beta de l’API REST Microsoft Graph](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true).

## <a name="prerequisites"></a>Prérequis

* Suivez les étapes dans [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](tutorial-create-user-flows.md).
* Si vous n’avez pas créé de dépôt GitHub, [créez-en un](https://docs.github.com/en/get-started/quickstart/create-a-repo).  

## <a name="select-a-custom-policies-folder"></a>Sélectionner un dossier de stratégies personnalisées

Votre dépôt GitHub peut contenir la totalité de vos fichiers de stratégie Azure AD B2C, ainsi que d’autres ressources. Dans le répertoire racine de votre dépôt, créez ou choisissez un dossier existant contenant vos stratégies personnalisées. 

Par exemple, sélectionnez un dossier nommé *policies*. Ajoutez vos fichiers de stratégies personnalisées Azure AD B2C au dossier *policies*. Ensuite, vous devez **Valider** les modifications.

N’envoyez (**Push**) pas les modifications. Vous effectuerez cette opération ultérieurement, après avoir configuré le flux de travail de déploiement.

## <a name="register-a-microsoft-graph-application"></a>Inscrire une application Microsoft Graph

Pour permettre l’interaction de l’action GitHub avec l’[API Microsoft Graph](microsoft-graph-operations.md), créez une inscription d’application dans votre locataire Azure AD B2C. Si vous ne l’avez pas déjà fait, [inscrivez une application Microsoft Graph](microsoft-graph-get-started.md).

Pour permettre à l’action GitHub d’accéder aux données dans Microsoft Graph, accordez à l’application inscrite les [autorisations d’application](/graph/permissions-reference) appropriées. Accordez l’autorisation **Microsoft Graph** > **Stratégie** > **Policy.ReadWrite.TrustFramework** dans les **API Permissions (Autorisations d’API)** de l’inscription d’application.

## <a name="create-a-github-encrypted-secret"></a>Créer un secret chiffré GitHub

Les secrets GitHub sont des variables d’environnement chiffrées que vous créez dans une organisation, un dépôt ou un environnement de dépôt. Dans cette étape, vous stockez le secret d’application pour l’application que vous avez inscrite précédemment à l’étape [Inscrire une application MS Graph](#register-a-microsoft-graph-application).

L’action GitHub de déploiement de stratégies personnalisées Azure AD B2C utilise le secret pour acquérir un jeton d’accès qui est utilisé pour interagir avec l’API Microsoft Graph. Pour plus d’informations, consultez [Création de secrets chiffrés pour un dépôt](https://docs.github.com/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository).

Pour créer un secret GitHub, procédez comme suit :

1. Dans GitHub, accédez à la page principale du dépôt.
1. Sous le nom de votre dépôt, sélectionnez **Settings (Paramètres)** .
1. Dans la barre latérale de gauche, sélectionnez **Secrets**.
1. Sélectionnez **New repository secret** (Nouveau secret de dépôt).
1. Pour **Name (Nom)** , saisissez **ClientSecret**.
1. Pour **Value (Valeur)** , saisissez le secret d’application que vous avez créé précédemment.
1. Sélectionnez **Ajouter un secret**.

## <a name="create-a-github-workflow"></a>Créer un flux de travail GitHub

Un flux de travail (workflow) GitHub est une procédure automatisée que vous ajoutez à votre dépôt. Les flux de travail sont constitués d’un ou de plusieurs travaux et peuvent être planifiés ou déclenchés par un événement. Au cours de cette étape, vous allez créer un flux de travail qui déploie votre stratégie personnalisée.

Pour créer un flux de travail, procédez comme suit :

1. Dans GitHub, accédez à la page principale de votre dépôt.
1. Sous le nom de votre dépôt, sélectionnez **Settings (Paramètres)** .

   ![Capture d’écran montrant l’onglet GitHub Actions](media/deploy-custom-policies-github-action/github-actions-tab.png)

1. Si vous n’avez pas configuré de flux de travail auparavant, sélectionnez **set up a workflow yourself (Configurer un workflow vous-même)** . Sinon, sélectionnez **New Workflow (Nouveau flux de travail)** .

   ![Capture d’écran montrant comment créer un flux de travail](media/deploy-custom-policies-github-action/set-up-a-workflow.png)

1. GitHub propose de créer un fichier de flux de travail nommé `main.yml` dans le dossier `.github/workflows`. Ce fichier contient des informations sur le flux de travail, incluant votre environnement Azure AD B2C et les stratégies personnalisées à déployer. Dans l’éditeur web GitHub, ajoutez le code YAML suivant :

    ```yml
    on: push

    env:
      clientId: 00000000-0000-0000-0000-000000000000
      tenant: your-tenant.onmicrosoft.com
    
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
    
        - name: 'Upload TrustFrameworkBase Policy'
          uses: azure-ad-b2c/deploy-trustframework-policy@v3
          with:
            folder: "./Policies"
            files: "TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
            tenant: ${{ env.tenant }}
            clientId: ${{ env.clientId }}
            clientSecret: ${{ secrets.clientSecret }}
    ```

1.  Mettez à jour les propriétés suivantes du fichier YAML :

    | Section| Nom | Valeur |
    | ---- | ----- |----- |
    | `env` | `clientId` | **ID d’application (client)** de l’application que vous avez inscrite à l’étape [Inscrire une application MS Graph](#register-a-microsoft-graph-application). |
    |`env`| `tenant` | Votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C (par exemple, contoso.onmicrosoft.com). |
    | `with`| `folder`| Dossier dans lequel sont stockés les fichiers de stratégies personnalisées, par exemple, `./Policies`.|
    | `with`| `files` | Liste délimitée par des virgules des fichiers de stratégie à déployer, par exemple, `TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`.|
    
    > [!IMPORTANT]
    > Lors de l’exécution des agents et du chargement des fichiers de stratégie, assurez-vous que ceux-ci ont été chargés dans l’ordre correct :
    >
    > 1. *TrustFrameworkBase.xml*
    > 1. *TrustFrameworkLocalization.xml*
    > 1. *TrustFrameworkExtensions.xml*
    > 1. *SignUpOrSignin.xml*
    > 1. *ProfileEdit.xml*
    > 1. *PasswordReset.xml*

1. Sélectionnez **Démarrer la validation**.
1. Sous les champs de message de validation, indiquez si vous souhaitez ajouter votre validation à la branche active ou à une nouvelle branche. Sélectionnez **Commit new file (Valider le nouveau fichier)** ou **Propose new file (Proposer un nouveau fichier)** pour créer une demande de tirage (pull request).

## <a name="test-your-workflow"></a>Tester votre workflow

Pour tester le flux de travail que vous avez créé, envoyez (**Push**) les modifications de votre stratégie personnalisée. Une fois l’exécution de votre travail démarrée, vous pouvez voir un graphique de visualisation de la progression de l’exécution, ainsi que l’activité de chaque étape sur GitHub.

1. Sur GitHub, accédez à la page principale de votre dépôt.
1. Sous le nom de votre dépôt, sélectionnez **Settings (Paramètres)** .
1. Dans le volet gauche, sélectionnez le flux de travail que vous avez créé.
1. Sous **Workflow runs (Exécutions de flux de travail)** , sélectionnez le nom de l’exécution que vous souhaitez afficher.

   ![Capture d’écran montrant comment sélectionner l’activité de flux de travail](media/deploy-custom-policies-github-action/workflow-report.png)

1. Sous **Jobs (Travaux)** ou dans le graphique de visualisation, sélectionnez le travail que vous souhaitez afficher.
1. Affichez les résultats de chaque étape. La capture d’écran suivante montre le journal de l’étape **Charger une stratégie personnalisée**.
 
   ![Journal de l’étape de chargement d’une stratégie personnalisée](media/deploy-custom-policies-github-action/job-activity.png)


## <a name="optional-schedule-your-workflow"></a>Facultatif : Planifier votre flux de travail

Le flux de travail que vous avez créé est déclenché par l’événement d’[envoi (push)](https://docs.github.com/actions/reference/events-that-trigger-workflows#push). Si vous préférez, vous pouvez choisir un autre événement pour déclencher le flux de travail, tel qu’une [demande de tirage (pull request)](https://docs.github.com/actions/reference/events-that-trigger-workflows#pull_request).

Vous pouvez également planifier l’exécution d’un flux de travail à des heures UTC spécifiques à l’aide de la [syntaxe cron POSIX](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07). L’événement de planification (schedule) vous permet de déclencher un flux de travail à une heure planifiée. Pour plus d’informations, consultez [Événements planifiés](https://docs.github.com/actions/reference/events-that-trigger-workflows#scheduled-events).

L’exemple suivant déclenche le flux de travail chaque jour à 5h30 et à 17h30 UTC :

```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

Pour modifier votre flux de travail :

1. Dans GitHub, accédez à la page principale de votre dépôt.
1. Sous le nom de votre dépôt, sélectionnez **Settings (Paramètres)** .
1. Dans le volet gauche, sélectionnez le flux de travail que vous avez créé.
1. Sous **Workflow runs (Exécutions de flux de travail)** , sélectionnez le nom de l’exécution que vous souhaitez afficher.
1. Dans le menu, sélectionnez les trois points de suspension **...** , puis **View the workflow file (Afficher le fichier de flux de travail)** .

   ![Capture d’écran montrant comment afficher le fichier de flux de travail](media/deploy-custom-policies-github-action/edit-workflow.png)
   
1. Dans l’éditeur web de GitHub, sélectionnez **Edit (Modifier)** .
1. Définissez `on: push` sur l’exemple ci-dessus.
1. **Validez** (Commit) vos modifications.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer des [événements qui déclenchent des flux de travail](https://docs.github.com/actions/reference/events-that-trigger-workflows)


