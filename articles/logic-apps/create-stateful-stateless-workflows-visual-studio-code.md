---
title: Créer des flux de travail d’automatisation (préversion) dans Visual Studio Code
description: Créez des flux de travail d’automatisation avec ou sans état grâce à l’extension Azure Logic Apps (préversion) dans Visual Studio Code pour intégrer des applications, des données, des services cloud et des systèmes locaux
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 94d970390f62107a82dc586605d34dd61cae0c26
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992557"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Créer des flux de travail avec état ou sans état dans Visual Studio Code avec l’extension Azure Logic Apps (préversion)

> [!IMPORTANT]
> Cette fonctionnalité en préversion publique est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour créer des flux de travail d’application logique qui s’intègrent à l’ensemble des applications, données, services cloud et systèmes, vous pouvez utiliser Visual Studio Code et l’extension Azure Logic Apps (préversion) pour générer et exécuter des flux de travail d’application logique [*avec état* et *sans état*](#stateful-stateless).

![Capture d’écran montrant Visual Studio Code et un flux de travail d’application logique.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Les applications logiques que vous créez avec l’extension en préversion publique utilisent le nouveau type de ressource **Logic Apps (préversion)** , et sont optimisées par le runtime [Azure Functions](../azure-functions/functions-overview.md). Ce nouveau type de ressource peut inclure plusieurs flux de travail et présente à certains égards des similitudes avec le type de ressource **Application de fonction**, qui peut inclure plusieurs fonctions.

En même temps, le type de ressource **Logic Apps** reste disponible. Vous pouvez l’utiliser pour créer des applications dans Visual Studio Code et le portail Azure. Toutefois, l’interface du type de ressource d’origine est distincte de celle du nouveau type de ressource. À ce stade, les deux types de ressources, **Logic Apps** et **Logic Apps (préversion)** , peuvent coexister dans Visual Studio Code et le portail Azure. Vous pouvez afficher toutes les applications logiques déployées dans votre abonnement Azure, ainsi qu’y accéder, mais elles apparaissent et sont conservées séparément dans leurs propres catégories et sections.

Cet article fournit une [vue d’ensemble de cette préversion publique](#whats-new), décrit différents aspects du type de ressource **Logic Apps (préversion)** , et explique comment créer cette ressource à l’aide de Visual Studio Code :

* différence entre applications logiques [avec et sans état](#stateful-stateless) ;

* [configuration requise pour l’installation](#prerequisites) et [configuration de Visual Studio Code](#set-up) pour l’extension en préversion public ;

* génération de nouveaux flux de travail **Logic Apps (préversion)** en [créant un projet et en sélectionnant un modèle de flux de travail](#create-project) ;

* publications des nouvelles applications logiques directement à partir de Visual Studio Code [vers Azure](#publish-azure) ou [vers un conteneur Docker](#deploy-docker) que vous pouvez exécuter en tout lieu. Pour plus d’informations sur Docker, consultez [Qu’est-ce que Docker ?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Que contient cette préversion publique ?

L’extension Azure Logic Apps (préversion) apporte de nombreuses fonctionnalités Logic Apps actuelles et supplémentaires à votre expérience de développement local dans Visual Studio Code, par exemple :

* Générez des applications logiques pour des flux de travail d’intégration et d’automatisation à partir de [plus de 300 connecteurs](/connectors/connector-reference/connector-reference-logicapps-connectors) pour les applications et services SaaS (Software-as-a-service) et PaaS (Platform-as-a-service), ainsi que de connecteurs pour les systèmes locaux.

  * Certains connecteurs managés, tels Azure Service Bus, Azure Event Hubs et SQL Server, s’exécutent de la même façon que les actions et déclencheurs natifs intégrés, telle l’action HTTP.

  * Créez et déployez des applications logiques pouvant s’exécuter en tout lieu, car le service Azure Logic Apps génère des chaînes de connexion avec signature d’accès partagé (SAP) que ces applications logiques peuvent utiliser pour envoyer des demandes au point de terminaison du runtime de connexion cloud. Le service Logic Apps enregistre ces chaînes de connexion avec d’autres paramètres de l’application pour vous permettre de stocker facilement ces valeurs dans Azure Key Vault quand vous opérez un déploiement sur Azure.

    > [!NOTE]
    > Par défaut, l’[identité attribuée par le système](../logic-apps/create-managed-service-identity.md) d’une ressource **Logic Apps (préversion)** est automatiquement activée pour authentifier les connexions au moment de l’exécution. Cette identité diffère des informations d’identification d’authentification ou de la chaîne de connexion que vous utilisez lors de la création d’une connexion. Si vous désactivez cette identité, les connexions ne fonctionneront pas au moment de l’exécution.

* Créez des applications logiques sans état qui s’exécutent uniquement en mémoire, de sorte qu’elles opèrent plus rapidement, répondent plus promptement, présentent un débit plus élevé et réduisent le coût d’exécution, car les historiques et données d’exécution entre les actions ne sont pas conservés dans un stockage externe. Si vous le souhaitez, vous pouvez activer l’historique d’exécution pour faciliter le débogage. Pour plus d’informations, consultez [Applications logiques avec et sans état](#stateful-stateless).

* Testez vos applications logiques localement dans l’environnement de développement de Visual Studio Code.

* Publiez et déployez vos applications logiques à partir de Visual Studio Code directement dans différents environnements d’hébergement, par exemple, [Azure App Service](../app-service/environment/intro.md) et [Conteneurs Docker](/dotnet/core/docker/introduction).

> [!NOTE]
> Pour plus d’informations sur les problèmes connus actuels, consultez la [page GitHub Problèmes connus](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) de l’extension en préversion.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Applications logiques avec et sans état

* *Avec état*

  Créez des applications logiques avec état lorsque vous devez conserver, examiner ou référencer des données d’événements précédents. Ces applications logiques conservent l’entrée et la sortie de chaque action, ainsi que les états de leurs flux de travail, dans un stockage externe qui permet d’examiner les détails et l’historique d’exécution à l’issue de chaque exécution. Les applications logiques avec état offrent une haute résilience en cas d’interruption. Une fois les services et systèmes restaurés, vous pouvez reconstituer les exécutions interrompues d’application logique à partir de l’état enregistré, et réexécuter les applications logiques jusqu’à leur terme. Les flux de travail avec état peuvent continuer de s’exécuter pendant jusqu’à un an.

* *Sans état*

  Créez des applications logiques sans état lorsque vous ne devez pas enregistrer, examiner ou référencer des données d’événements précédents. Ces applications logiques conservent l’entrée et la sortie de chaque action, ainsi que les états de leurs flux de travail, uniquement en mémoire, au lieu de transférer ces informations vers un stockage externe. Par conséquent, les applications logiques sans état offrent des temps d’exécution plus courts généralement inférieurs à 5 minutes, des performances plus rapides avec des temps de réponse plus brefs, un débit plus élevé, et des coûts d’exécution réduits car les détails et l’historique d’exécution ne sont pas conservés dans un stockage externe. Toutefois, quand des interruptions se produisent, les exécutions interrompues ne sont pas automatiquement restaurées, de sorte que l’appelant doit relancer manuellement les exécutions interrompues. Pour faciliter le débogage, vous pouvez [activer l’historique d’exécution](#run-history) pour les applications logiques sans état.

  Actuellement, les flux de travail sans état prennent uniquement en charge les actions pour des [connecteurs managés](../connectors/apis-list.md#managed-api-connectors), pas pour des déclencheurs. Pour démarrer votre flux de travail, sélectionnez le [déclencheur de demande, Event Hubs ou Service Bus intégré](../connectors/apis-list.md#built-ins). Pour plus d’informations sur les déclencheurs, actions et connecteurs non pris en charge, consultez [Fonctionnalités non prises en charge](#unsupported).

Pour plus d’informations sur la façon dont les applications logiques imbriquées se comportent selon qu’elles sont avec et sans état, consultez [Différences de comportement entre les applications logiques imbriquées avec et sans état](#nested-behavior).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modèle de tarification

Lorsque vous déployez le nouveau type de ressource **Logic Apps (préversion)** , vous êtes invité à sélectionner un plan d’hébergement, spécifiquement [App Service ou Premium](../azure-functions/functions-scale.md), à utiliser comme modèle de tarification. Si vous sélectionnez le plan App Service, vous êtes également invité à sélectionner un [niveau tarifaire](../app-service/overview-hosting-plans.md). Pendant la préversion publique, l’exécution d’applications logiques sur App Service n’occasionne pas de frais *supplémentaires* en plus du coût du plan sélectionné.

Les applications logiques avec état utilisant un [stockage externe](../azure-functions/functions-scale.md#storage-account-requirements), le modèle de tarification de Stockage Azure s’applique aux transactions de stockage qu’exécute le runtime Azure Logic Apps. Par exemple, des files d’attente sont utilisées pour la planification, tandis que des tables et blobs sont utilisés pour stocker les états de flux de travail.

Pour plus d’informations sur les modèles de tarification qui s’appliquent à ce nouveau type de ressource, consultez les rubriques suivantes :

* [Échelle et hébergement dans Azure Functions](../azure-functions/functions-scale.md)
* [Effectuer un scale-up d’application dans Azure App Service](../app-service/manage-scale-up.md)
* [Détails de tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Détails de tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Détails de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/)

## <a name="prerequisites"></a>Prérequis

### <a name="access-and-connectivity"></a>Accès et connectivité

* Accédez à Internet afin de pouvoir télécharger la configuration requise, connectez-vous à partir de Visual Studio Code à votre compte Azure, et publiez à partir de Visual Studio Code sur Azure, un conteneur Docker ou un autre environnement.

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Pour générer le même exemple d’application logique que celui présenté dans cet article, vous avez besoin d’un compte de courrier Office 365 Outlook qui utilise un compte Microsoft professionnel ou scolaire pour se connecter.

  Si vous choisissez d’utiliser un autre [connecteur de messagerie pris en charge par Azure Logic Apps](/connectors/), tel qu’Outlook.com ou [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), vous pouvez toujours suivre l’exemple. Les étapes générales sont les mêmes, mais l’interface utilisateur et les options peuvent différer de plusieurs manières. Par exemple, si vous utilisez le connecteur Outlook.com, utilisez votre compte Microsoft personnel au lieu de vous connecter.

### <a name="tools"></a>Outils

* [Visual Studio Code versions 1.30.1 (janvier 2019) ou ultérieures](https://code.visualstudio.com/), qui est gratuit. Vous pouvez également télécharger et installer ces outils supplémentaires pour Visual Studio Code si vous ne les avez pas encore :

  * [Extension de compte Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), qui offre une expérience Azure commune de connexion et de filtrage des abonnements pour toutes les autres extensions Azure dans Visual Studio Code.

  * [Extension C# pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), qui permet à la fonctionnalité F5 d’exécuter votre application logique.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), version [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) ou [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), via le programme d’installation Microsoft (MSI). Ces outils incluent une version du même runtime qui optimise le runtime Azure Functions qui s’exécute dans Visual Studio Code.

    > [!IMPORTANT]
    > Si vous disposez d’une installation antérieure à ces versions, commencez par la désinstaller, ou assurez-vous que la variable d’environnement PATH pointe vers la version que vous téléchargez et installez.
    >
    > Si vous souhaitez utiliser l’[action **Inline Code**](../logic-apps/logic-apps-add-run-inline-code.md) pour l’exécution de code JavaScript, vous devez utiliser la version 3x du runtime Azure Functions, car l’action ne prend pas en charge la version 2x. En outre, cette action n’est pas prise en charge actuellement sur les systèmes d’exploitation Linux.

  * [Extension Azure Logic Apps (préversion) pour Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Cette extension en préversion publique offre la possibilité de créer des applications logiques avec et sans état, ainsi que de les tester localement dans Visual Studio Code.

    À l’heure actuelle, vous pouvez avoir l’extension **Azure Logic Apps** d’origine et la nouvelle extension **Azure Logic Apps (préversion)** installées en même temps dans Visual Studio Code. En sélectionnant l’icône Azure dans la barre d’outils de Visual Studio Code, vous pouvez afficher toutes les applications logiques déployées dans Azure, mais chaque type de ressource apparaît dans ses propres sections d’extension, **Logic Apps** et **Azure Logic Apps (préversion)** .

    > [!IMPORTANT]
    > Si vous avez créé des applications logiques à l’aide de l’extension **Azure Logic Apps (préversion privée)** , ces applications logiques ne fonctionnent pas avec l’extension en préversion publique. Toutefois, vous pouvez migrer ces applications logiques en désinstallant l’extension en préversion privée, en effectuant le nettoyage requis, et en installant l’extension en préversion publique. Vous pouvez ensuite créer votre projet dans Visual Studio Code et copier le fichier **workflow.definition** de votre application logique précédemment créée dans votre nouveau projet.
    >
    > Par conséquent, avant d’installer l’extension en préversion publique, veillez à désinstaller les versions antérieures et à supprimer les artefacts suivants :
    >
    > * Le dossier **Microsoft.Azure.Functions.ExtensionBundle.Workflows** contenant les offres groupées d’extension précédentes, qui se trouve sur l’un des chemins d’accès suivants :
    >
    >   * `C:\Users\<username>\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\<username>.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * Le dossier **microsoft.azure.workflows.webjobs.extension**, qui est le cache [NuGet](/nuget/what-is-nuget) pour l’extension en préversion privée et se trouve sur dans le chemin d’accès suivant :
    >
    >   `C:\Users\<username>\.nuget\packages`

    Pour installer l’extension **Azure Logic Apps (préversion)** , procédez comme suit :

    1. Dans Visual Studio Code, dans la barre d’outils de gauche, sélectionnez **Extensions**.

    1. Dans la zone de recherche d’extensions, entrez `azure logic apps preview`. Dans la liste des résultats, sélectionnez **Azure Logic Apps (préversion)** **>** **Installer**.

       Une fois l’installation terminée, l’extension en préversion publique apparaît dans la liste **Extensions : Installées**.

       ![Capture d’écran montrant la liste des extensions installées de Visual Studio Code avec l’extension « Azure Logic Apps (préversion) » soulignée.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Pour tester l’exemple d’application logique que vous créez dans cet article, vous avez besoin d’un outil capable d’envoyer des appels au déclencheur de demande, ce qui constitue la première étape de l’exemple d’application logique. Si vous n’avez pas d’outil de ce type, vous pouvez télécharger, installer et utiliser [Postman](https://www.postman.com/downloads/).

* Pour faciliter la journalisation des diagnostics et la fonctionnalité de suivi, vous pouvez ajouter et utiliser une ressource [Application Insights](../azure-monitor/app/app-insights-overview.md). Vous pouvez créer cette ressource pendant le déploiement de l’application logique, ou dans le portail Azure après le déploiement de celle-ci.

### <a name="storage-requirements"></a>Exigences de stockage

Actuellement, la création d’une ressource **Logic Apps (préversion)** n’est pas disponible sur Mac OS. Toutefois, pour Windows ou tout autre système d’exploitation, configurez cette exigence de stockage.

1. Téléchargez et installez l’[Émulateur de Stockage Azure 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Pour exécuter l’émulateur, vous devez disposer d’une installation locale de SQL DB, telle que l’édition gratuite [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658). Pour plus d’informations, consultez [Utilisation de l’émulateur de Stockage Azure pour le développement et le test](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Avant d’ouvrir le Concepteur d’application logique pour créer votre flux de travail d’application logique, veillez à démarrer l’émulateur. Dans le cas contraire, vous recevez le message suivant : `Workflow design time could not be started`.
   >
   > ![Capture d’écran montrant l’Émulateur de stockage Azure en cours d’exécution.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurer Visual Studio Code

1. Pour vous assurer que toutes les extensions sont correctement installées, rechargez ou redémarrez Visual Studio Code.

1. Activez ou vérifiez que Visual Studio Code détecte et installe automatiquement les mises à jour des extensions, afin que votre extension en préversion publique reçoive les dernières mises à jour.

   Pour vérifier ce paramètre, procédez comme suit :

   1. Dans le menu **Fichier**, accédez à **Préférences** **>** **Paramètres**.

   1. Sous l’onglet **Utilisateur**, accédez à **Fonctionnalités** **>** **Extensions**.

   1. Vérifiez que les options **Vérification automatique des mises à jour** et **Mise à jour automatique** sont sélectionnées.

1. Activez les paramètres d’extension **Azure Logic Apps (préversion)** ou vérifiez qu’ils sont configurés dans Visual Studio Code :

   * **Azure Logic Apps V2 : Mode Panneau**
   * **Azure Logic Apps V2 : Runtime du projet**

   1. Dans le menu **Fichier**, accédez à **Préférences** **>** **Paramètres**.

   1. Sous l’onglet **Utilisateur**, accédez à **>** **Extensions** **>** **Azure Logic Apps (préversion)** .

   1. Sous **Azure Logic Apps V2 : Mode Panneau**, vérifiez que l’option **Activer le mode panneau** est sélectionnée. Sous **Azure Logic Apps V2 : Runtime du projet**, définissez la version sur **~3** ou **~2**, en fonction de la [version d’Azure Functions Core Tools](#prerequisites) que vous avez installée précédemment.

      > [!IMPORTANT]
      > Si vous souhaitez utiliser l’[action **Inline Code**](../logic-apps/logic-apps-add-run-inline-code.md) pour l’exécution de code JavaScript, veillez à utiliser la version 3x du runtime du projet, car l’action ne prend pas en charge la version 2x. En outre, cette action n’est pas prise en charge actuellement sur les systèmes d’exploitation Linux.

      ![Capture d’écran montrant les paramètres de Visual Studio Code pour l’extension « Azure Logic Apps (préversion) ».](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

1. Dans la barre d’outils de Visual Studio Code, sélectionnez l’icône Azure.

   ![Capture d’écran montrant la barre d’outils de Visual Studio Code et l’icône Azure sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Dans le volet Azure, sous **Azure : Logic Apps (préversion)** , sélectionnez **Se connecter à Azure**. Quand la page d’authentification de Visual Studio Code s’affiche, connectez-vous avec votre compte Azure.

   ![Capture d’écran montrant le volet Azure et le lien sélectionné pour la connexion à Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Une fois que vous être connecté, le volet Azure affiche les abonnements dans votre compte Azure. Si les abonnements attendus n’apparaissent pas, ou si vous souhaitez que le volet affiche uniquement des abonnements spécifiques, procédez comme suit :

   1. Dans la liste des abonnements, positionnez votre pointeur en regard du premier abonnement jusqu’à ce que le bouton **Sélectionner les abonnements** (icône de filtre) s’affiche. Sélectionnez l’icône de filtre.

      ![Capture d’écran montrant le volet Azure et l’icône de filtre sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Ou, dans la barre d’état de Visual Studio Code, sélectionnez votre compte Azure. 

   1. Quand une autre liste d’abonnements s’affiche, sélectionnez les abonnements de votre choix, puis n’oubliez pas de sélectionner **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Créer un projet local

Avant de pouvoir créer votre application logique, créez un projet local afin de pouvoir gérer et déployer votre application logique à partir de Visual Studio Code. Le projet sous-jacent est très similaire à un projet Azure Functions, également appelé projet d’application de fonction.

1. Sur votre ordinateur, créez un dossier local *vide* à utiliser pour le projet que vous créerez par la suite dans Visual Studio Code.

1. Dans Visual Studio Code, fermez tous les dossiers ouverts.

1. Dans le volet Azure, en regard de **Azure : Logic Apps (préversion)** , sélectionnez **Créer un projet** (icône représentant un dossier et un éclair).

   ![Capture d’écran montrant la barre d’outils du volet Azure avec l’option « Créer un projet » activée.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Si le pare-feu Windows Defender vous invite à accorder l’accès au réseau pour `Code.exe`, c’est-à-dire Visual Studio Code, ainsi que pour `func.exe`, c’est-à-dire Azure Functions Core Tools, sélectionnez **Réseaux privés, tels qu’un réseau domestique ou un réseau d’entreprise** **>** **Autoriser l’accès**.

1. Accédez à l’emplacement où vous avez créé votre dossier de projet, sélectionnez ce dossier et continuez.

   ![Capture d’écran montrant la boîte de dialogue « Sélectionner un dossier » avec un dossier de projet nouvellement créé et le bouton « Sélectionner » sélectionné.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Dans la liste des modèles qui s’affiche, sélectionnez **Flux de travail avec état** ou **Flux de travail sans état**. Cet exemple sélectionne **Flux de travail avec état**.

   ![Capture d’écran montrant la liste des modèles de flux de travail avec l’option « Flux de travail avec état » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Spécifiez un nom pour le flux de travail de votre application logique, puis appuyez sur Entrée. L’exemple suivant utilise `example-workflow` en tant que nom.

   ![Capture d’écran montrant la zone « Créer un flux de travail avec état (3/4) » et « example-workflow » en tant que nom de flux de travail.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. Dans la liste qui s’affiche ensuite, sélectionnez **Ouvrir dans la fenêtre active**.

   ![Capture d’écran montrant une liste avec l’option « Ouvrir dans la fenêtre active » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code se recharge, ouvre le volet de l’Explorateur, et affiche votre projet qui comprend désormais les fichiers de projet générés automatiquement. Par exemple, le projet contient un dossier qui affiche le nom du flux de travail de votre application logique. À l’intérieur de ce dossier, le fichier `workflow.json` contient la définition JSON sous-jacente du flux de travail de votre application logique.

   ![Capture d’écran montrant la fenêtre Explorateur avec le dossier de projet, le dossier de flux de travail et le fichier « workflow.json ».](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

Ensuite, ouvrez le fichier **workflow.json** dans le Concepteur d’application logique.

### <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Ouvrir le fichier de définition de flux de travail dans le Concepteur d’application logique

Avant d’essayer d’ouvrir votre fichier de définition de flux de travail dans le concepteur, si Visual Studio Code est en cours d’exécution sur Windows ou Linux, assurez-vous que l’Émulateur de stockage Azure est en cours d’exécution. Pour plus d’informations, consultez les [Prérequis](#prerequisites).

1. Développez le dossier du projet pour votre flux de travail. Ouvrez le menu contextuel du fichier **workflow.json**, puis sélectionnez **Ouvrir dans le concepteur**.

   ![Capture d’écran montrant le volet de l’Explorateur et la fenêtre contextuelle du fichier workflow.json avec l’option « Ouvrir dans le concepteur » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Si vous recevez le message d’erreur indiquant `Workflow design time could not be started`, assurez-vous que l’Émulateur de stockage Azure est en cours d’exécution. Dans le cas contraire, essayez les suggestions de résolution des problèmes suivantes :

   Dans Visual Studio Code, vérifiez la sortie de l’extension en préversion.

   1. Dans le menu **Affichage**, sélectionnez **Sortie**.

   1. Dans la barre de titre **Sortie** barre de titre, sélectionnez **Azure Logic Apps** afin de pouvoir afficher la sortie de l’extension en préversion, par exemple :

      ![Capture d’écran montrant la fenêtre de sortie de Visual Studio Code avec l’option « Azure Logic Apps » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Examinez la sortie et vérifiez si ce message d’erreur s’affiche :

      ```text
      A host error has occurred during startup operation '<operation-ID>'.
      System.Private.CoreLib: The file 'C:\Users\<your-username>\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Cette erreur peut se produire si vous avez précédemment essayé d’ouvrir le concepteur, puis abandonné ou supprimé votre projet. Pour résoudre cette erreur, supprimez le dossier **ExtensionBundles** à cet emplacement **...\Users\\{votre-nom-utilisateur}\AppData\Local\Temp\Functions\ExtensionBundles**, puis réessayez d’ouvrir le fichier **workflow.json** dans le concepteur.

1. Dans la liste **Activer les connecteurs dans Azure**, sélectionnez l’option **Utiliser les connecteurs d’Azure** qui s’applique à tous les connecteurs managés disponibles dans le portail Azure, pas seulement aux connecteurs destinés aux services Azure.

   ![Capture d’écran montrant le volet de l’Explorateur avec la liste « Activer les connecteurs dans Azure » ouverte et l’option « Utiliser les connecteurs d’Azure » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. Dans la liste des groupes de ressources, sélectionnez **Créer un groupe de ressources**.

   ![Capture d’écran montrant le volet de l’Explorateur avec une liste de groupes de ressources et l’option « Créer un groupe de ressources » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Spécifiez un nom pour votre groupe de ressources, puis appuyez sur Entrée. Cet exemple utilise `example-logic-app-preview-rg`.

   ![Capture d’écran montrant le volet de l’Explorateur et la zone du nom du groupe de ressources.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Dans la liste des emplacements, recherchez et sélectionnez une région Azure à utiliser pour la création de votre groupe de ressources et de vos ressources. Cet exemple utilise la région **USA Centre-Ouest**.

   > [!NOTE]
   > Actuellement, certaines régions ne sont pas prises en charge, bien que des mises à jour soient en cours. Pour plus d’informations, consultez la [page GitHub Problèmes connus](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) de l’extension en préversion.

   ![Capture d’écran montrant le volet de l’Explorateur avec la liste des emplacements et la région « USA Centre-Ouest » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Une fois cette étape accomplie, Visual Studio Code ouvre le Concepteur d’application logique.

   > [!NOTE]
   > Lorsque Visual Studio Code démarre l’API de flux de travail au moment de la conception, un message s’affiche indiquant que le démarrage peut prendre quelques secondes. Vous pouvez ignorer ce message ou sélectionner **OK**.

   Une fois le Concepteur d’application logique affiché, l’invite **Choisir une opération** apparaît sur le concepteur. Elle est sélectionnée par défaut, ce qui a pour effet d’afficher le volet **Ajouter une action**.

   ![Capture d’écran montrant le Concepteur d’application logique.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Ensuite, [ajoutez un déclencheur et des actions](#add-trigger-actions) à votre flux de travail.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Ajouter un déclencheur et des actions

Une fois que vous avez ouvert le Concepteur d’application logique à partir du menu contextuel de votre fichier **workflow.json**, l’invite **Choisir une opération** s’affiche sur le concepteur. Elle est sélectionnée par défaut. Vous pouvez maintenant commencer à créer votre flux de travail en ajoutant un déclencheur et des actions.

Dans cet exemple, le flux de travail de l’application logique utilise le déclencheur et les actions suivantes :

* [Déclencheur de demande](../connectors/connectors-native-reqres.md) intégré, **Lors de la réception d’une requête HTTP**, qui reçoit les appels ou requêtes entrants, et crée un point de terminaison que d’autres services ou applications logiques peuvent appeler.

* [Action Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), **Envoyer un e-mail**.

* [Action Réponse](../connectors/connectors-native-reqres.md) intégrée, qui permet d’envoyer une réponse et de retourner des données à l’appelant.

### <a name="add-the-request-trigger"></a>Ajout du déclencheur Request

1. En regard du concepteur, dans le volet **Ajouter un déclencheur**, dans la zone de recherche **Choisir une opération**, assurez-vous que l’option **Intégrée** est activée de sorte que vous pouvez sélectionner un déclencheur qui s’exécute en mode natif.

1. Dans la zone de recherche **Choisir une opération**, entrez `when a http request`, puis sélectionnez le déclencheur de demande intégré nommé **Lors de la réception d’une requête HTTP**.

   ![Capture d’écran montrant le Concepteur d’application logique et le volet **Ajouter un volet déclencheur** avec le déclencheur « Lors de la réception d’une requête HTTP » sélectionné.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Lorsque le déclencheur s’affiche sur le concepteur, le volet d’informations du déclencheur s’ouvre pour afficher les propriétés, les paramètres et d’autres actions du déclencheur.

   ![Capture d’écran montrant le Concepteur d’application logique avec le déclencheur « Lors de la réception d’une requête HTTP » sélectionné et le volet d’informations du déclencheur ouvert.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Si le volet d’informations ne s’affiche pas, assurez-vous que le déclencheur est sélectionné sur le concepteur.

1. Si vous devez supprimer un élément du concepteur, procédez comme suit :

   1. Dans le concepteur, sélectionnez l’élément.

   1. Dans le volet d’informations de l’élément qui s’ouvre sur le côté droit, sélectionnez le bouton ( **...** ) **>** **Supprimer**. Sélectionnez **OK** pour confirmer la suppression.

      ![Capture d’écran montrant l’élément sélectionné dans le concepteur avec le volet d’informations ouvert, le bouton des points de suspension sélectionné et l’option « Supprimer ».](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Ajouter l’action Office 365 Outlook

1. Dans le concepteur, sous le déclencheur que vous avez ajouté, sélectionnez **Nouvelle étape**.

   L’invite **Choisir une opération** s’affiche sur le concepteur, et le volet **Ajouter une action** se rouvre pour vous permettre de sélectionner l’action suivante.

1. Dans le volet **Ajouter une action**, dans la zone de recherche **Choisir une opération**, sélectionnez **Azure** afin de pouvoir rechercher et sélectionner une action pour un connecteur managé déployé dans Azure.

   Cet exemple sélectionne et utilise l’action Office 365 Outlook **Envoyer un e-mail (V2)** .

   ![Capture d’écran montrant le Concepteur d’application logique et le volet **Ajouter une action** avec l’action Office 365 Outlook « Envoyer un e-mail » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Dans le volet d’informations de l’action, sélectionnez **Se connecter** pour pouvoir créer une connexion à votre compte de courrier.

   ![Capture d’écran montrant le Concepteur d’application logique et le volet **Envoyer un e-mail (V2)** avec l’option « Se connecter » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Lorsque Visual Studio Code vous invite à donner votre consentement pour l’accès à votre compte de courrier, sélectionnez **Ouvrir**.

   ![Capture d’écran montrant l’invite de Visual Studio Code pour autoriser l’accès.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Pour éviter les invites futures, sélectionnez **Configurer les domaines approuvés** afin de pouvoir ajouter la page d’authentification en tant que domaine approuvé.

1. Suivez les invites suivantes pour vous connecter, autoriser l’accès et autoriser le retour à Visual Studio Code.

   > [!NOTE]
   > Si trop de temps s’écoule avant que vous répondiez aux invites, le processus d’authentification expire et échoue. Dans ce cas, revenez au concepteur et réessayez de vous connecter pour créer la connexion.

1. Lorsque l’extension en préversion Azure Logic Apps vous invite à donner votre consentement pour l’accès à votre compte de courrier, sélectionnez **Ouvrir**. Suivez l’invite suivante pour autoriser l’accès.

   ![Capture d’écran montrant l’invite d’extension en préversion pour autoriser l’accès.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Pour éviter les invites futures, sélectionnez **Ne plus demander cette extension**.

1. Dans le concepteur, si l’action **Envoyer un e-mail** n’est pas sélectionnée, sélectionnez-la.

1. Dans le volet d’informations de l’action, sous l’onglet **Paramètres**, fournissez les informations requises pour l’action, par exemple :

   ![Capture d’écran montrant le Concepteur d’application logique avec les détails de l’action Office 365 Outlook « Envoyer un e-mail ».](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **To** | Oui | <*your-email-address*> | Destinataire de l’e-mail, qui peut être votre adresse de messagerie à des fins de test. Cet exemple utilise l’e-mail fictif `sophiaowen@fabrikam.com`. |
   | **Subject** | Oui | `An email from your example workflow` | L’objet de l’e-mail |
   | **Corps** | Oui | `Hello from your example workflow!` | Contenu du corps de l’e-mail. |
   ||||

   > [!NOTE]
   > Si vous souhaitez apporter des modifications dans le volet d’informations dans **Paramètres**, **Exécuter après** ou sous l’onglet **Résultat statique**, veillez à sélectionner **Terminé** pour valider ces modifications avant de changer d’onglet ou de positionner le focus sur le concepteur. Dans le cas contraire, Visual Studio Code ne conserve pas vos modifications. Pour plus d’informations, consultez la [page GitHub Problèmes connus](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md) de l’extension en préversion.

1. Dans le concepteur, sélectionnez **Enregistrer**.

Ensuite, déboguez et testez votre flux de travail localement dans Visual Studio Code.

<a name="debug-test-locally"></a>

## <a name="debug-and-test-your-logic-app"></a>Déboguer et tester votre application logique

1. Pour faciliter le débogage d’un flux de travail d’application logique sans état, vous pouvez [activer l’historique d’exécution pour ce flux de travail](#run-history).

1. Dans la barre d’outils de Visual Studio Code, ouvrez le menu **Exécuter**, puis sélectionnez **Démarrer le débogage** (F5).

   La fenêtre **Terminal** s’ouvre pour vous permettre d’examiner la session de débogage.

1. À présent, recherchez l’URL de rappel du point de terminaison sur le déclencheur de demande.

   1. Rouvrez le volet de l’Explorateur afin de pouvoir afficher votre projet.

   1. Dans le menu contextuel du fichier **workflow.json**, sélectionnez **Vue d’ensemble**.

      ![Capture d’écran montrant le volet de l’Explorateur et la fenêtre contextuelle du fichier workflow.json avec l’option « vue d’ensemble » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Recherchez la valeur **URL de rappel**, qui ressemble à cette URL pour l’exemple de déclencheur de demande :

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Capture d’écran montrant la page de présentation de votre flux de travail avec l’URL de rappel.](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Pour tester l’URL de rappel en déclenchant le flux de travail de l’application logique, ouvrez [Postman](https://www.postman.com/downloads/) ou votre outil préféré pour la création et l’envoi de requêtes.

   Cet exemple se poursuit en utilisant Postman. Pour plus d’informations, consultez [Prise en main de Postman](https://learning.postman.com/docs/getting-started/introduction/).

   1. Dans la barre d’outils de Postman, sélectionnez **Nouveau**.

      ![Capture d’écran montrant Postman avec le bouton Nouveau sélectionné.](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Dans le volet **Créer**, sous **Blocs de construction**, sélectionnez **Requête**.

   1. Dans la fenêtre **Enregistrer la requête**, sous **Nom de la requête**, entrez un nom pour la requête, par exemple `Test workflow trigger`.

   1. Sous **Sélectionner une collection ou un dossier dans lesquels enregistrer**, sélectionnez **Créer une collection**.

   1. Sous **Toutes les collections**, fournissez un nom pour la collection à créer afin d’organiser vos demandes, appuyez sur Entrée, puis sélectionnez **Enregistrer dans <*nom-collection*>** . Cet exemple utilise `Logic Apps requests` comme nom de collection.

      Le volet de demande de Postman s’ouvre, qui vous permet d’envoyer une demande à l’URL de rappel du déclencheur de demande.

      ![Capture d’écran montrant Postman avec le volet de demande ouvert](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Revenez à Visual Studio Code. À partir de la page de présentation du flux de travail, copiez la valeur de la propriété **URL de rappel**.

   1. Revenez à Postman. Dans le volet de demande, dans la liste de méthodes suivante qui affiche actuellement la méthode de demande par défaut **GET**, collez l’URL de rappel précédemment copiée dans la zone d’adresse, puis sélectionnez **Envoyer**.

      ![Capture d’écran montrant Postman et l’URL de rappel dans la zone d’adresse avec le bouton Envoyer sélectionné.](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      L’exemple de flux de travail d’application logique envoie un e-mail qui ressemble à ceci :

      ![Capture d’écran montrant l’e-mail Outlook décrit dans l’exemple.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Dans Visual Studio Code, revenez à la page de présentation de votre flux de travail.

   Si vous avez créé un flux de travail avec état, une fois que la demande envoyée déclenche le flux de travail, la page de présentation affiche l’historique et l’état d’exécution du flux de travail. Pour plus d’informations sur les états d’exécution, consultez [Examiner l’historique des exécutions](../logic-apps/monitor-logic-apps.md#review-runs-history).

   ![Capture d’écran montrant la page de présentation de votre flux de travail avec l’état et l’historique d’exécution.](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > Si l’état d’exécution ne s’affiche pas, essayez d’actualiser la page de présentation en sélectionnant **Actualiser**.

1. Pour examiner les états de chaque étape d’une exécution spécifique, ainsi que les entrées et sorties de l’étape, sélectionnez le bouton ( **...** ) correspondant à cette exécution, puis sélectionnez **Afficher l’exécution**.

   ![Capture d’écran montrant la ligne de l’historique d’exécution de votre flux de travail avec le bouton (...) et l’option « Afficher l’exécution » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code affiche les états d’exécution de chaque action.

1. Pour afficher les entrées et sorties de chaque étape, développez celle que vous souhaitez inspecter. Pour examiner plus en détail les entrées et sorties brutes de cette étape, sélectionnez **Afficher les entrées brutes** ou **Afficher les sorties brutes**.

   ![Capture d’écran montrant l’état de chaque étape du flux de travail, ainsi que les entrées et sorties de l’action « Envoyer un e-mail » développée.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Pour arrêter la session de débogage, dans le menu **Exécuter**, sélectionnez **Arrêter le débogage** (Maj + F5).

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Retourner une réponse à l’appelant

Pour retourner une réponse à l’appelant ayant envoyé une demande à votre application logique, vous pouvez utiliser l’[Action de réponse](../connectors/connectors-native-reqres.md) intégrée pour un flux de travail qui démarre avec le déclencheur de demande.

1. Dans le Concepteur d’application logique, sous l’action **Envoyer un e-mail**, sélectionnez **Nouvelle étape**.

   L’invite **Choisir une opération** s’affiche sur le concepteur, et le volet **Ajouter une action** se rouvre pour vous permettre de sélectionner l’action suivante.

1. Dans le volet **Ajouter une action**, dans la zone de recherche **Choisir une action**, assurez-vous que l’option **Intégrée** est sélectionnée. Dans la zone de recherche, entrez `response`, puis sélectionnez l’action **Réponse**.

   ![Capture d’écran montrant le Concepteur d’application logique avec l’action Réponse sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Lorsque l’action **Réponse** s’affiche dans le concepteur, le volet d’informations de l’action s’ouvre automatiquement.

   ![Capture d’écran montrant le Concepteur d’application logique avec le volet d’informations de l’action « Réponse » ouvert et la propriété « Corps » définie sur la valeur de la propriété « Corps » de l’action « Envoyer un e-mail ».](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Sous l’onglet **Paramètres**, entrez les informations requises pour la fonction que vous souhaitez appeler.

   Cet exemple retourne la valeur de la propriété **Corps** qui est la sortie de l’action **Envoyer un e-mail**.

   1. Cliquez à l’intérieur de la zone de propriété **Corps** afin que la liste de contenu dynamique s’affiche et indique les valeurs de sortie disponibles à partir des actions et du déclencheur précédents dans le flux de travail.

      ![Capture d’écran montrant le volet d’informations de l’action « Réponse » avec le pointeur de la souris à l’intérieur de la propriété « Corps » afin que la liste de contenu dynamique s’affiche.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Dans la liste contenu dynamique, sous **Envoyer un e-mail**, sélectionnez **Corps**.

      ![Capture d’écran montrant la liste de contenu dynamique ouverte. Dans la liste, sous l’en-tête « Envoyer un e-mail », la valeur de sortie « Corps » est sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Lorsque vous avez terminé, la propriété **Corps** de l’action Réponse est désormais définie sur la valeur de sortie **Corps** de l’action **Envoyer un e-mail**.

      ![Capture d’écran montrant l’état de chaque étape du flux de travail, ainsi que les entrées et sorties de l’action « Réponse » développée.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Dans le concepteur, sélectionnez **Enregistrer**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Tester votre application logique

Une fois que vous avez mis à jour votre application logique, vous pouvez effectuer un autre test en réexécutant le débogueur dans Visual Studio et en envoyant une autre demande pour déclencher votre application logique mise à jour, comme décrit dans [Déboguer et tester votre application logique](#debug-test-locally).

1. Dans la barre d’outils de Visual Studio Code, ouvrez le menu **Exécuter**, puis sélectionnez **Démarrer le débogage** (F5).

1. Dans Postman ou dans votre outil de création et d’envoi de demandes, envoyez une autre demande pour déclencher votre flux de travail.

1. Si vous avez créé un flux de travail avec état, sur la page de présentation du flux de travail, vérifiez l’état de la dernière exécution. Pour afficher l’état, les entrées et les sorties de chaque étape de cette exécution, sélectionnez le bouton ( **...** ) correspondant à cette exécution, puis sélectionnez **Afficher l’exécution**.

   Par exemple, voici l’état pas à pas d’une exécution une fois que l’exemple de flux de travail a été mis à jour avec l’action Réponse.

   ![Capture d’écran montrant l’état de chaque étape du flux de travail mis à jour, ainsi que les entrées et sorties de l’action « Réponse » développée.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Pour arrêter la session de débogage, dans le menu **Exécuter**, sélectionnez **Arrêter le débogage** (Maj + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publication dans Azure

À partir de Visual Studio Code, vous pouvez déployer votre projet directement dans Azure, ce qui a pour effet de publier votre application logique à l’aide du nouveau type de ressource **Logic Apps (préversion)** . À l’instar de la ressource application de fonction dans Azure Functions, le déploiement de ce nouveau type de ressource nécessite de sélectionner [un plan d’hébergement et un niveau tarifaire](../app-service/overview-hosting-plans.md) que vous pouvez configurer lors du déploiement. Pour plus d’informations sur les plans d’hébergement et la tarification, consultez les rubriques suivantes :

* [Effectuer un scale-up d’application dans Azure App Service](../app-service/manage-scale-up.md)
* [Échelle et hébergement dans Azure Functions](../azure-functions/functions-scale.md)

Vous pouvez publier votre application logique en tant que nouvelle ressource, ce qui a pour effet de créer automatiquement toute ressource supplémentaire nécessaire, par exemple, un [compte de stockage Azure, similaire à la configuration requise des applications de fonction](../azure-functions/storage-considerations.md). Vous pouvez également publier votre application logique sur une ressource **Logic Apps (préversion)** précédemment déployée, que le processus de déploiement remplace dans Azure.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publier en tant que nouvelle ressource Logic Apps (préversion)

1. Dans la barre d’outils de Visual Studio Code, sélectionnez l’icône Azure.

1. Dans la barre d’outils du volet **Azure : Logic Apps (préversion)** , sélectionnez **Déployer sur Logic Apps**.

   ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » et la barre d’outils du volet avec l’option « Déployer sur Logic Apps » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Dans la liste qu’ouvre Visual Studio Code, sélectionnez l’une des options suivantes :

   * **Créer une ressource Logic Apps (préversion) dans Azure** (rapide) ;
   * **Créer une ressource Logic Apps (préversion) dans Azure Advanced** ;
   * Une ressource **Logic Apps (préversion)** précédemment déployée éventuelle.

   Nous poursuivons cet exemple avec l’option **Créer une ressource Logic Apps (préversion) dans Azure Advanced**.

   ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » avec une liste dans laquelle l’option « Créer une ressource Logic Apps (préversion) dans Azure » est sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Pour créer votre ressource **Logic Apps (préversion)** , procédez comme suit :

   1. Fournissez un nom global unique pour votre nouvelle application logique, qui est le nom à utiliser pour la ressource **Logic Apps (préversion)** .

      ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » et une invite à fournir un nom pour la nouvelle application logique à créer.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Sélectionnez un plan d’hébergement pour votre nouvelle application logique, [**Plan App Service**](../azure-functions/functions-scale.md#app-service-plan) ou [**Premium**](../azure-functions/functions-scale.md#premium-plan). Cet exemple sélectionne **Plan App Service**.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » et une invite à sélectionner « Plan App Service » ou « Premium ».](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Créez un plan App Service ou sélectionnez-en un. Cet exemple sélectionne **Créer un plan App Service**.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » et une invite à « Créer un Plan App Service » ou à sélectionner un Plan App Service existant.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Entrez un nom pour votre plan App Service, puis sélectionnez un [niveau tarifaire](../app-service/overview-hosting-plans.md) pour le plan. Cet exemple sélectionne le plan **F1 Gratuit**.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » et une invite à sélectionner un niveau tarifaire.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Pour des performances optimales, recherchez et sélectionnez le même groupe de ressources que votre projet pour le déploiement.

      > [!NOTE]
      > Bien que vous puissiez créer ou utiliser un groupe de ressources différent, cela peut affecter les performances. Si vous créez ou choisissez un groupe de ressources différent, mais annulez l’opération après l’affichage de l’invite de confirmation, votre déploiement est également annulé.

   1. Pour les flux de travail avec état, sélectionnez **Créer un compte de stockage** ou choisissez un compte de stockage existant.

      ![Capture d’écran montrant le volet « Azure : Logic Apps (préversion) » et une invite à créer ou sélectionner un compte de stockage.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Pour faciliter la journalisation des diagnostics et la fonctionnalité de suivi, vous pouvez sélectionner une ressource Application Insights existante. Dans le cas contraire, vous pouvez sélectionner **Créer une ressource Application Insights** ou configurer Application Insights dans le portail Azure après avoir déployé votre application.

      Avant de déployer, assurez-vous d’ajouter l’objet `Host.Triggers.Workflow` à l’objet `Information` dans le fichier `logLevel`host.json`logging` existant au niveau racine de votre projet, puis définissez **sur**, par exemple :

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Le fichier **host.json** pourrait ressembler à ceci :

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Lorsque vous avez terminé, Visual Studio Code commence à créer et déployer les ressources nécessaires à la publication de votre application logique.

1. Pour examiner et surveiller le processus de déploiement, dans le menu **Afficher**, sélectionnez **Sortie**. Dans la liste des barres d’outils de la fenêtre Sortie, sélectionnez **Azure Logic Apps**.

   ![Capture d’écran montrant la fenêtre Sortie avec « Azure Logic Apps » sélectionné dans la liste des barres d’outils, ainsi que la progression et les états du déploiement.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Lorsque Visual Studio Code termine le déploiement de votre flux de travail d’application logique sur Azure, le message suivant s’affiche :

   ![Capture d’écran montrant un message indiquant que le déploiement sur Azure s’est terminé correctement.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Félicitations, votre application logique est maintenant active dans Azure et activée par défaut.

Ensuite, découvrez [comment trouver votre application logique déployée dans le portail Azure](#find-manage-deployed-workflows-portal) ou [dans Visual Studio Code](#find-manage-deployed-workflows-vs-code).

### <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Activer l’analyse des ressources Logic Apps (préversion) déployées

Pour activer l’historique d’exécution et la surveillance sur une ressource **Logic Apps (préversion)** déployée, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez la ressource **Logic Apps (préversion)** déployée.

1. Dans le menu de cette ressource, sous **API**, sélectionnez **CORS**.

1. Dans le volet **CORS**, sous **Origines autorisées**, ajoutez le caractère générique (*).

1. Lorsque c’est chose faite, dans la barre d’outils **CORS**, sélectionnez **Enregistrer**.

   ![Capture d’écran montrant le portail Azure avec une ressource Logic Apps (préversion) déployée. Dans le menu de ressources, « CORS » est sélectionné avec une nouvelle entrée pour « Origines autorisées » définie sur le caractère générique « * ».](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Rechercher et gérer des applications logiques déployées dans Visual Studio Code

Dans Visual Studio Code, vous pouvez afficher toutes les applications logiques déployées dans votre abonnement Azure, qu’il s’agisse du type de ressource **Logic Apps** d’origine ou **Logic Apps (préversion)** , puis sélectionnez les tâches qui vous aident à gérer ces applications logiques. Toutefois, pour accéder aux deux types de ressources, vous avez besoin des extensions **Azure Logic Apps** et **Azure Logic Apps (préversion)** pour Visual Studio Code.

1. Dans la barre d’outils de gauche, sélectionnez l’icône Azure. Dans le volet **Azure : Logic Apps (préversion)** , développez votre abonnement qui affiche toutes les applications logiques déployées pour cet abonnement.

1. Recherchez et sélectionnez l’application logique à gérer. Ouvrez le menu contextuel de l’application logique, puis sélectionnez la tâche à effectuer.

   Par exemple, vous pouvez sélectionner des tâches telles que l’arrêt, le démarrage, le redémarrage ou la suppression de votre application logique déployée.

   ![Capture d’écran montrant Visual Studio Code avec le volet d’extension « Azure Logic Apps (préversion) » ouvert et le flux de travail déployé.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Pour ouvrir l’application logique déployée dans le portail Azure, dans Visual Studio Code, ouvrez le menu contextuel de l’application logique, puis sélectionnez **Ouvrir dans portail**.

   Le portail Azure s’ouvre dans votre navigateur, vous connecte automatiquement au portail si vous êtes connecté à Visual Studio Code, et affiche votre application logique.

   ![Capture d’écran montrant la page du portail Azure de votre application logique dans Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Vous pouvez également vous connecter séparément au portail Azure, utiliser la zone de recherche du portail pour rechercher votre application logique, puis sélectionner votre application logique dans la liste des résultats.

   ![Capture d’écran montrant le portail Azure et la barre de recherche avec des résultats de recherche pour l’application logique déployée qui apparaît sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Rechercher et gérer les applications logiques déployées dans le portail

Dans le portail Azure, vous pouvez afficher toutes les applications logiques déployées qui se trouvent dans votre abonnement Azure, qu’il s’agisse du type de ressource **Logic Apps** d’origine ou du type de ressource **Logic Apps (préversion)** . Actuellement, chaque type de ressource est organisé et géré en tant que catégorie distincte dans Azure.

> [!NOTE]
> Pour la préversion publique, vous pouvez uniquement afficher les ressources **Logic Apps (préversion)** déployées dans le portail Azure, pas en créer. Vous ne pouvez créer ces applications logiques que dans Visual Studio Code. Toutefois, vous pouvez [ajouter des flux de travail](#add-workflows) à des applications logiques déployées avec ce type de ressource.

Pour rechercher les applications logiques dont le type de ressource est **Logic Apps (préversion)** , procédez comme suit :

1. Dans la zone de recherche du portail Azure, entrez `logic app preview`. Lorsque la liste des résultats s’affiche, sous **Services**, sélectionnez **Logic Apps (préversion)** .

   ![Capture d’écran montrant la zone de recherche du portail Azure contenant le texte de recherche « préversion d’application logique ».](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Dans le volet **Logic Apps (préversion)** , recherchez et sélectionnez l’application logique que vous avez déployée à partir de Visual Studio Code.

   ![Capture d’écran montrant le portail Azure et les ressources Logic Apps (préversion) déployées dans Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Le portail Azure ouvre la page de ressource individuelles pour l’application logique sélectionnée.

   ![Capture d’écran montrant la page de ressources du flux de travail de votre application logique dans le portail Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Pour afficher les flux de travail de cette application logique, dans le menu de celle-ci, sélectionnez **Flux de travail**.

   Le volet **Flux de travail** affiche tous les flux de travail de l’application logique actuelle. Cet exemple montre le flux de travail que vous avez créé dans Visual Studio Code.

   ![Capture d’écran montrant une page de ressources « Logic Apps (préversion) » avec le volet « flux de travail » ouvert et le flux de travail déployé.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Pour afficher un flux de travail, dans le volet **Flux de travail**, sélectionnez ce flux de travail.

   Le volet Flux de travail s’ouvre, affichant des informations supplémentaires, ainsi que des tâches que vous pouvez effectuer sur ce flux de travail.

   Par exemple, pour afficher les étapes du flux de travail, sélectionnez **Concepteur**.

   ![Capture d’écran montrant le volet « Vue d’ensemble » du flux de travail sélectionné, tandis que le menu du flux de travail affiche la commande « Concepteur » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Le Concepteur d’application logique s’ouvre, affichant le flux de travail que vous avez créé dans Visual Studio Code. Vous pouvez maintenant apporter des modifications à ce flux de travail dans le portail Azure.

   ![Capture d’écran montrant le Concepteur d’application logique et le flux de travail déployé à partir de Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Ajouter un flux de travail à des applications logiques déployées

Le portail Azure vous permet d’ajouter des flux de travail vides à une ressource **Logic Apps (préversion)** que vous avez déployée à partir de Visual Studio Code, ainsi que de créer ces flux de travail.

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre ressource **Logic Apps (préversion)** déployée.

1. Dans le menu de l’application logique, sélectionnez **Flux de travail**. Dans le volet **Flux de travail**, sélectionnez **Ajouter**.

   ![Capture d’écran montrant le volet et la barre d’outils « Flux de travail » de l’application logique choisie avec la commande « Ajouter » sélectionnée.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Dans le volet **Nouveau flux de travail**, entrez le nom du flux de travail. Sélectionnez **Avec état** ou **Sans état** **>** **Créer**.

   Une fois qu’Azure a déployé votre nouveau flux de travail, qui apparaît dans le volet **Flux de travail**, sélectionnez ce flux de travail pour effectuer la gestion ainsi que d’autres tâches telles qu’ouvrir le Concepteur d’application logique ou le mode Code.

   ![Capture d’écran montrant le flux de travail sélectionné avec les options de gestion et de révision.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Par exemple, l’ouverture du concepteur pour un nouveau flux de travail a pour effet d’afficher un canevas vide. Vous pouvez maintenant générer ce flux de travail dans le portail Azure.

   ![Capture d’écran montrant le Concepteur d’application logique et un flux de travail vide.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Déployer dans un conteneur Docker

L’[outil interface de ligne de commande (CLI) .NET Core](/dotnet/core/tools/) vous permet de générer votre projet, puis de publier votre build. Vous pouvez ensuite générer et utiliser un [conteneur Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) comme destination pour le déploiement du flux de travail de votre application logique. Pour plus d’informations, consultez les rubriques suivantes :

* [Introduction aux conteneurs et à Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduction à .NET et à Docker](/dotnet/core/docker/introduction)
* [Terminologie Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Tutoriel : Prise en main de Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Pour générer votre projet, ouvrez une invite de ligne de commande, puis exécutez la commande suivante :

   `dotnet build -c release`

   Pour plus d’informations, consultez la page de référence de [dotnet build](/dotnet/core/tools/dotnet-build/).

1. Publiez votre build en exécutant la commande suivante :

   `dotnet publish -c release`

   Pour plus d’informations, consultez la page de référence de [dotnet publish](/dotnet/core/tools/dotnet-publish/).

1. Générez un conteneur Docker en utilisant un fichier Docker pour un flux de travail .NET, et en exécutant la commande suivante :

   `docker build --tag local/workflowcontainer .`

   Voici un exemple de fichier Docker pour un flux de travail.NET. Remplacez simplement la valeur <*storage-account-connection-string*> par la chaîne de connexion de votre compte de stockage Azure que vous avez enregistrée précédemment, qui ressemble à l’exemple suivant :

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Pour plus d’informations, consultez [docker build](https://docs.docker.com/engine/reference/commandline/build/).

1. Exécutez le conteneur localement à l’aide de la commande suivante :

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Pour plus d’informations, consultez [docker run](https://docs.docker.com/engine/reference/commandline/run/).

1. Pour obtenir l’URL de rappel du déclencheur de demande, envoyez la demande suivante :

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   La valeur de <*master-key*> est définie dans le compte de stockage Azure que vous définissez pour `AzureWebJobsStorage` dans le fichier **azure-webjobs-secrets/{deployment-name}/host.json**, où vous pouvez trouver la valeur la section suivante :

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Historique des exécutions pour les flux de travail d’application logique sans état

Pour déboguer plus facilement un flux de travail d’application logique sans état, vous pouvez activer l’historique des exécutions de ce flux de travail, puis le désactiver lorsque vous avez terminé.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Pour un flux de travail d’application logique sans état dans Visual Studio Code

Si vous travaillez sur le flux de travail d’application logique sans état et l’exécutez localement dans Visual Studio Code, procédez comme suit :

1. Dans votre projet, recherchez et développez le dossier **workflow-designtime**. Recherchez et ouvrez le fichier **local.settings.json**.

1. Ajoutez la propriété `Workflow.<yourWorkflowName>.operationOptions` et définissez la valeur sur `WithStatelessRunHistory`, par exemple :

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflow.<yourWorkflowName>.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Pour désactiver l’historique des exécutions lorsque vous avez terminé, supprimez la propriété `Workflow.<yourWorkflowName>.OperationOptions` et sa valeur, ou définissez-la sur `None`.

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Pour un flux de travail d’application logique sans état dans le portail Azure

Si vous avez déjà déployé votre projet sur le portail Azure, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), recherchez et ouvrez votre ressource **Logic Apps (préversion)** .

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Configuration**.

1. Sous l’onglet **Paramètres de l’application**, sélectionnez **Nouveau paramètre d’application**.

1. Dans le volet **Ajouter/modifier un paramètre d’application**, dans la zone **Nom**, entrez le nom de l’option d’opération : 

   `Workflow.<yourWorkflowName>.OperationOptions`

1. Dans la zone **Valeur**, entrez la valeur suivante : `WithStatelessRunHistory`

   Par exemple :

   ![Capture d’écran montrant le portail Azure et la ressource Logic Apps (préversion) avec le volet « Configuration » > « Nouveau paramètre d’application » > « Ajouter/modifier un paramètre d’application », et l’option « Workflow.<yourWorkflowName>OperationOptions » définie sur « WithStatelessRunHistory ».](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Quand vous avez terminé, sélectionnez **OK**. Dans le volet **Configuration**, sélectionnez **Enregistrer**.

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Différences de comportement imbriqué entre les applications logiques avec et sans état

Vous pouvez [faire en sorte qu’un flux de travail d’application logique puis être appelé](../logic-apps/logic-apps-http-endpoint.md) à partir d’autres flux de travail d’application logique à l’aide du déclencheur de [demande](../connectors/connectors-native-reqres.md), du déclencheur de [Webhook HTTP](../connectors/connectors-native-webhook.md) ou de déclencheurs de connecteur managé de [type ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) pouvant recevoir des requêtes HTTPS.

Voici les modèles de comportement que les flux de travail d’application logique imbriquée peuvent suivre après qu’un flux de travail parent a appelé un flux de travail enfant :

* Modèle d’interrogation asynchrone

  Le flux de travail parent n’attend pas de réponse à son appel initial, mais vérifie continuellement l’historique des exécutions du flux de travail enfant jusqu’à la fin de l’exécution de celui-ci. Par défaut, les flux de travail avec état suivent ce modèle, qui est idéal pour les flux de travail enfants de longue durée susceptibles de dépasser les [limites de délai d’expiration de demande](../logic-apps/logic-apps-limits-and-config.md).

* Modèle synchrone (« fire and forget »)

  Le flux de travail enfant accuse réception de l’appel en retournant immédiatement une réponse `202 ACCEPTED`, et le flux de travail parent continue jusqu’à l’action suivante sans attendre les résultats du flux de travail enfant. Au lieu de cela, le flux de travail parent reçoit les résultats lorsque le flux de travail enfant termine son exécution. Les flux de travail enfants avec état qui n’incluent pas d’action Réponse suivent toujours le modèle synchrone. Pour les flux de travail enfants avec état, vous pouvez consulter l’historique des exécutions.

  Pour activer ce comportement, dans la définition JSON du flux de travail, définissez la propriété `OperationOptions` sur la valeur `DisableAsyncPattern`. Pour plus d’informations, consultez [Types d’action et de déclencheur – Options d’opération](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Déclencher et attendre

  Pour un flux de travail enfant sans état, le flux de travail parent attend une réponse retournant les résultats du flux de travail enfant. Ce modèle fonctionne de manière similaire à l’utilisation [du déclencheur ou de l’action HTTP](../connectors/connectors-native-http.md) intégrés pour appeler un flux de travail enfant. Les flux de travail enfants sans état qui n’incluent pas d’action Réponse retournent immédiatement une réponse `202 ACCEPTED`, mais le flux de travail parent attend que le flux de travail enfant se termine avant de passer à l’action suivante. Ces comportements s’appliquent uniquement aux flux de travail enfants sans état.

Ce tableau spécifie le comportement du flux de travail enfant selon que les flux de travail parent et enfant sont avec état, sans état ou mixtes :

| Flux de travail parent | Flux de travail enfant | Comportement du flux de travail enfant |
|-----------------|----------------|----------------|
| Avec état | Avec état | Asynchrone ou synchrone avec le paramètre `operationOptions=DisableSynPattern` |
| Avec état | Sans état | Déclencher et attendre |
| Sans état | Avec état | Synchrone |
| Sans état | Sans état | Déclencher et attendre |
||||

## <a name="limits"></a>Limites

Si de nombreuses [limites existantes pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) sont identiques pour ce type de ressource, les différences de cette extension en préversion publique sont les suivantes :

* Connecteurs managés : 50 demandes par minute par connexion.

* Pour l’[action de code Inline pour JavaScript](../logic-apps/logic-apps-add-run-inline-code.md), ces limites ont changé :

  * La limite de caractères de code augmente, passant de 1 024 caractères à 100 000 caractères.

  * La limite de temps pour exécuter le code augmente, passant de cinq à 15 secondes.

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Fonctionnalités non disponibles ou non prises en charge

Pour cette préversion publique, ces fonctionnalités ne sont pas disponibles ou ne sont pas prises en charge :

* La création de la ressource **Logic Apps (préversion)** n’est pas disponible actuellement sur Mac OS.

* Les connecteurs personnalisés, les déclencheurs basés sur webhook et le déclencheur de fenêtre glissante ne sont pas pris en charge dans cette préversion.

* Pour les flux de travail d’application logique sans état, vous pouvez ajouter des actions uniquement pour des [connecteurs managés](../connectors/apis-list.md#managed-api-connectors), pas pour des déclencheurs. Pour démarrer votre flux de travail, utilisez les [déclencheurs de demande, d’Event Hubs ou de Service Bus](../connectors/apis-list.md#built-ins).

* Dans le portail Azure, vous ne pouvez pas créer d’applications logiques avec le nouveau type de ressource **Logic Apps (préversion)** . Vous ne pouvez créer ces applications logiques que dans Visual Studio Code. Toutefois, une fois que vous avez déployé des applications logiques avec ce type de ressource à partir de Visual Studio Code vers Azure, vous pouvez [ajouter de nouveaux flux de travail à ces applications logiques](#add-workflows).

* les plans d’hébergement de **consommation** ne sont pas pris en charge pour le déploiement d’applications logiques.

## <a name="next-steps"></a>Étapes suivantes

Nous aimerions que vous nous fassiez part de votre expérience avec cette extension en préversion publique.

* Pour tout bogue ou problème que vous rencontreriez, [créez un problème dans GitHub](https://github.com/Azure/logicapps/issues).
* Pour toute question, demande, commentaire ou retour d’expérience, utilisez de [formulaire de commentaires](https://aka.ms/lafeedback).