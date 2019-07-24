---
title: Configurer les paramètres Azure Function App | Microsoft Docs
description: Apprenez à configurer les paramètres d’application Azure Functions.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 92ca09040836dfc55a9d709b12a0ee01192d6bac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957389"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Comment gérer une Function App dans le portail Azure 

Dans Azure Functions, une Function App fournit le contexte d’exécution de vos fonctions individuelles. Les comportements de la Function App s’appliquent à toutes les fonctions hébergées par une Function App donnée. Cette rubrique décrit comment configurer et gérer vos Function Apps dans le portail Azure.

Commencez par accéder au [portail Azure](https://portal.azure.com) et connectez-vous à votre compte Azure. Dans la barre de recherche en haut du portail, tapez le nom de votre Function App et sélectionnez-la dans la liste. Après avoir sélectionné votre Function App, la page suivante s’affiche :

![Vue d’ensemble de Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Vous pouvez accéder à tout ce dont vous avez besoin pour gérer votre application de fonction à partir de la page Vue d’ensemble, en particulier les **[paramètres de l’application](#settings)** et les **[fonctionnalités de la plateforme](#platform-features)** .

## <a name="settings"></a>Paramètres de l’application

L’onglet **Paramètres de l’application** conserve les paramètres qui sont utilisés par votre application de fonction.

![Paramètres de l’application de fonction sur le portail Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Ces paramètres sont chiffrés et stockés, et vous devez sélectionner **Afficher les valeurs** pour afficher les valeurs dans le portail.

Pour ajouter un paramètre, sélectionnez **Nouveau paramètre d’application** et ajoutez la nouvelle paire clé-valeur.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Lorsque vous développez une application de fonction localement, ces valeurs sont conservées dans le fichier de projet local.settings.json.

## <a name="platform-features"></a>Fonctionnalités de la plate-forme

![Onglet Fonctionnalités de la plate-forme de Function App.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Les Function Apps s’exécutent dans la plateforme Azure App Service et y sont entretenues. Par conséquent, vos Function Apps ont accès à la plupart des fonctionnalités de la plateforme d’hébergement web principale d’Azure. Dans l’onglet **Fonctionnalités de la plate-forme**, vous pouvez vous accéder aux nombreuses fonctionnalités de la plateforme App Service que vous pouvez utiliser dans vos Function Apps. 

> [!NOTE]
> Toutes les fonctionnalités App Service ne sont pas disponibles quand une Function App s’exécute dans le cadre du plan d’hébergement Consommation.

Le reste de cette rubrique se concentre sur les fonctionnalités App Service du portail Azure qui sont utiles pour Functions :

+ [Éditeur App Service](#editor)
+ [Console](#console)
+ [Outils avancés (Kudu)](#kudu)
+ [Options de déploiement](#deployment)
+ [CORS](#cors)
+ [Authentification](#auth)
+ [Définition de l’API](#swagger)

Pour plus d’informations sur l’utilisation des paramètres App Service, consultez [Configurer des applications web dans Azure App Service](../app-service/configure-common.md).

### <a name="editor"></a>Éditeur App Service

| | |
|-|-|
| ![Éditeur App Service de Function App.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | L’éditeur App Service est un éditeur avancé intégré au portail. Vous pouvez l’utiliser pour modifier les fichiers de configuration JSON et les fichiers de code. L’activation de cette option entraîne l’ouverture d’un onglet distinct du navigateur avec un éditeur de base. Vous pouvez ainsi l’intégrer au référentiel GitHub, exécuter et déboguer du code et modifier les paramètres de Function App. Cet éditeur fournit un environnement de développement amélioré pour vos fonctions en comparaison avec le panneau Function App par défaut.    |

![L’éditeur App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Console

| | |
|-|-|
| ![Console Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | La console intégrée au portail est un outil de développement idéal lorsque vous souhaitez interagir avec Function App à partir de la ligne de commande. Les commandes courantes incluent la création de fichiers et de répertoires et la navigation, ainsi que l’exécution de scripts et de fichiers de commandes. |

![Console Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Outils avancés (Kudu)

| | |
|-|-|
| ![Kudu Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Les outils avancés pour App Service (également appelé Kudu) donnent accès aux fonctionnalités d’administration avancées de votre Function App. Dans Kudu, vous pouvez gérer les informations système, les paramètres d’application, les variables d’environnement, les extensions de site, les en-têtes HTTP et les variables de serveur. Vous pouvez également lancer **Kudu** en naviguant vers le point de terminaison SCM pour votre Function App, comme `https://<myfunctionapp>.scm.azurewebsites.net/`. |

![Configurer Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Options de déploiement

| | |
|-|-|
| ![Options de déploiement Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Functions vous permet de développer votre code de fonctions sur votre machine locale. Vous pouvez ensuite charger votre projet Function App local vers Azure. En plus du chargement FTP traditionnel, Functions vous permet de déployer votre Function App à l’aide de solutions d’intégration continue populaires, telles que GitHub, Azure DevOps, Dropbox, Bitbucket, etc. Pour plus d’informations, consultez [Déploiement continu pour Azure Functions](functions-continuous-deployment.md). Pour charger manuellement à l’aide de FTP ou Git local, vous devez également [configurer vos informations d’identification de déploiement](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Pour empêcher l’exécution de code malveillant dans vos services, App Service bloque les appels vers vos Function Apps provenant de sources externes. Functions prend en charge des ressources cross-origin (CORS) pour vous permettre de définir une « liste approuvée » d’origines autorisées à partir desquelles vos fonctions peuvent accepter les demandes distantes.  |

![Configurer l’élément CORS de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Authentification

| | |
|-|-|
| ![Authentification Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Lorsque les fonctions utilisent un déclencheur HTTP, vous pouvez exiger l’authentification préalable des appels. App Service prend en charge l’authentification Azure Active Directory et la connexion avec des fournisseurs sociaux tels que Facebook, Microsoft et Twitter. Pour plus d’informations sur la configuration de fournisseurs d’authentification spécifiques, consultez [Azure App Service authentication overview](../app-service/overview-authentication-authorization.md) (Vue d’ensemble de l’authentification Azure App Service). |

![Configurer l’authentification pour une Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Définition de l’API

| | |
|-|-|
| ![Définition Swagger de l’API Function App dans le portail Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions prend en charge Swagger pour permettre aux clients de consommer plus facilement vos fonctions déclenchées via HTTP. Pour plus d’informations sur la création de définitions d’API avec Swagger, consultez [Héberger une API RESTful avec CORS dans Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Vous pouvez également utiliser les Functions Proxies pour définir une surface d’API unique pour plusieurs fonctions. Pour plus d’informations, consultez [Utilisation de Azure Functions Proxies (préversion)](functions-proxies.md). |

![Configurer l’API de Function App](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Étapes suivantes

+ [Configurer des applications web dans Azure App Service](../app-service/configure-common.md)
+ [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)



