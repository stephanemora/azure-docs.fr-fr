---
title: Résolution des problèmes liés à Azure Static Web Apps
description: Premières étapes de résolution des problèmes rencontrés lors de l'utilisation d'Azure Static Web Apps.
services: static-web-apps
author: geektrainer
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: chrhar
ms.openlocfilehash: 082c3af694932f4e864b99aa8c2cdc7d1f5fca30
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108210143"
---
# <a name="troubleshooting-deployment-and-runtime-errors"></a>Résolution des erreurs de déploiement et d'exécution

Cet article contient des guides pas à pas pour résoudre les problèmes de déploiement et d'autres problèmes liés à votre application web statique.

## <a name="retrieve-deployment-error-messages"></a>Récupérer les messages d'erreur de déploiement

Le workflow de déploiement d'Azure Static Web Apps utilise le [processus de génération Oryx](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md#build) Node.js qui exécute automatiquement les commandes suivantes :

```bash
npm install
npm run build # if specified in package.json
npm run build:azure # if specified in package.json
```

Toutes les erreurs générées par ce processus sont consignées dans l'exécution du workflow GitHub.

1. Accédez au référentiel GitHub de votre application web statique.
1. Sélectionnez **Actions**.

    > [!NOTE]
    > Toutes les exécutions de workflow qui ont échoué sont signalées par une *croix* rouge (au lieu d'une coche verte).

1. Sélectionnez le lien de l'exécution de workflow que vous souhaitez valider.
1. Sélectionnez **Créer et déployer le travail** pour accéder aux détails du déploiement.
1. Sélectionnez **Créer et déployer** pour afficher le journal.

    ![Capture d'écran du journal de déploiement d'une application web statique](./media/troubleshooting/build-deploy-log.png)

1. Examinez les journaux et les éventuels messages d'erreur.

    > [!NOTE]
    > Certains messages d'erreur ou d'avertissement peuvent apparaître en rouge, comme les notes relatives aux modules *.oryx_prod_node_modules* et à l'*espace de travail*. Ceux-ci font partie du processus normal de déploiement.

Si l'installation d'un package échoue ou si d'autres problèmes surviennent, les messages d'erreur d'origine apparaissent ici, comme si vous aviez exécuté les commandes `npm` localement.

### <a name="confirm-folder-configuration"></a>Valider la configuration des dossiers

Azure Static Web Apps doit savoir quels dossiers utiliser pour héberger votre application. Cette configuration est validée par le processus de génération à la fin du workflow. Toutes les erreurs sont consignées pendant les étapes de validation.

Si l'un des messages d'erreur suivants apparaît dans le journal des erreurs, cela signifie que la configuration des dossiers de votre workflow est incorrecte.

| Message d’erreur | Description |
| --- | --- |
|Emplacement du répertoire de l'application : « /*dossier* » n'est pas valide. Impossible de détecter ce répertoire. | Vérifiez que votre workflow est conforme à la structure de votre référentiel. |
| Le processus de génération de l'application n'a pas pu produire le dossier d'artefact : « *dossier* ». | Vérifiez que la propriété `folder` est correctement configurée dans votre fichier de workflow. |
| Aucun répertoire d'API n'a été spécifié ou le répertoire spécifié est introuvable. | Azure Functions ne sera pas créé, car le workflow ne définit pas de valeur pour le dossier `api`. |

Trois emplacements de dossier sont spécifiés dans le workflow. Avant le déploiement, assurez-vous que ces paramètres correspondent à la fois à votre projet et à tous les outils qui transforment votre code source.

| Paramètre de configuration | Description |
| --- | --- |
| `app_location` | Emplacement racine du code source à déployer. Ce paramètre correspond généralement à */* ou à l'emplacement du code JavaScript et HTML de votre projet. |
| `output_location` | Nom du dossier créé par un processus de génération à partir d'un bundler tel que webpack. Ce dossier doit être créé par le processus de génération, avec un sous-répertoire sous `app_location`. |
| `api_location` |Emplacement racine de votre application Azure Functions hébergée par Azure Static Web Apps. Celui-ci pointe vers le dossier racine de toutes les instances d'Azure Functions de votre projet, généralement *api*. |

> [!NOTE]
> Les messages d'erreur liés à une configuration incorrecte d'`api_location` peuvent néanmoins se traduire par une génération réussie, car aucun code serverless n'est requis par Azure Static Web Apps.

## <a name="review-server-errors"></a>Examiner les erreurs liées au serveur

Utilisez [Application Insights](../azure-monitor/app/app-insights-overview.md) pour rechercher les messages d'erreur d'exécution. Si vous n'avez pas encore créé d'instance, consultez [Surveiller Azure Static Web Apps](monitor.md). Application Insights consigne le message d'erreur complet ainsi que le rapport des appels de procédure généré par chaque erreur.

> [!NOTE]
> Seuls les messages d'erreur générés après l'installation d'Application Insights peuvent être consultés.

1. Sur le portail Azure, ouvrez le **groupe de ressources** associé à votre application web statique.
1. Sélectionnez l'instance d'Application Insights qui porte le même nom que votre application web statique (si elle a été créée en suivant les étapes ci-dessus).
1. Sous *Examiner*, sélectionnez **Échecs**.
1. Faites défiler l'écran jusqu'à *Explorer*, à droite.
1. Dans le coin inférieur droit, sous *Explorer*, un bouton affiche le nombre d'opérations qui ont récemment échoué.

    ![Capture d'écran illustrant l'écran des échecs.](./media/troubleshooting/app-insights-errors.png)

1. Sélectionnez le bouton **x Opérations** pour ouvrir un panneau affichant les dernières opérations qui ont échoué.

    ![Capture d'écran illustrant l'écran des opérations](./media/troubleshooting/app-insights-operations.png)

1. Explorez une erreur en la sélectionnant dans la liste.

    ![Capture d'écran illustrant l'écran des détails de l'erreur](./media/troubleshooting/app-insights-details.png)

## <a name="environment-variables"></a>Variables d'environnement

De nombreuses applications web utilisent des variables d'environnement pour stocker des paramètres sensibles ou spécifiques à l'environnement. Azure Static Web Apps prend en charge les variables d'environnement via les paramètres d'application.

Les paramètres d'application sont des paires clé/valeur qui définissent les variables d'environnement de votre application. Les variables sont disponibles en utilisant la même syntaxe que pour l'accès aux variables d'environnement.

Lors du déploiement, vérifiez que les variables d'environnement sont définies en tant que paramètres d'application.

1. Sur le portail Azure, ouvrez votre application web statique.
1. Sélectionnez **Configuration**. L'écran *Configuration* affiche la liste de tous les paramètres d'application.

    ![Capture d'écran illustrant l'écran Configuration d'une application web statique](media/troubleshooting/app-settings.png)

Suivez les étapes ci-dessous pour ajouter une nouvelle variable.

1. Sélectionnez **Ajouter**.
1. Définissez le **Nom**.
1. Définissez la **Valeur**.
1. Sélectionnez **OK**.
1. Sélectionnez **Enregistrer**.
