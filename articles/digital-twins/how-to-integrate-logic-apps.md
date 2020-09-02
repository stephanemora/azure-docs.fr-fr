---
title: Intégrer à Logic Apps
titleSuffix: Azure Digital Twins
description: Découvrez comment connecter Logic Apps à Azure Digital Twins à l’aide d’un connecteur personnalisé
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 2fc2db54217756ba0f4f7d643b1bc12ad2668209
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88848632"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Intégrer à Logic Apps à l’aide d’un connecteur personnalisé

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) est un service cloud qui vous permet d’automatiser les flux de travail entre les applications et les services. En connectant Logic Apps aux API Azure Digital Twins, vous pouvez créer de tels flux automatisés autour d’Azure Digital Twins et des données connexes.

Azure Digital Twins ne dispose pas actuellement d’un connecteur certifié (pré-construit) pour Logic Apps. Au lieu de cela, le processus actuel d’utilisation de Logic Apps avec Azure Digital Twins consiste à créer un [**connecteur Logic Apps personnalisé**](../logic-apps/custom-connector-overview.md), à l’aide d’un [fichier Swagger Azure Digital Twins personnalisé](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json) qui a été modifié pour fonctionner avec Logic Apps.

Dans cet article, vous allez utiliser le [portail Azure](https://portal.azure.com) pour **créer un connecteur personnalisé** qui peut être utilisé pour connecter Logic Apps à une instance Azure Digital Twins. Vous allez ensuite **créer une application logique** qui utilise cette connexion pour obtenir un exemple de scénario, dans lequel des événements déclenchés par un minuteur mettent automatiquement à jour un jumeau dans votre instance Azure Digital Twins. 

## <a name="prerequisites"></a>Prérequis

Si vous ne disposez pas d’abonnement Azure, **créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** avant de commencer.

Connectez-vous au [portail Azure](https://portal.azure.com) avec ce compte.

### <a name="set-up-azure-digital-twins-instance"></a>Configurer l’instance Azure Digital Twins

Pour connecter une instance Azure Digital Twins à Logic Apps dans cet article, **l’instance Azure Digital Twins** doit déjà être configurée. 

Si vous devez configurer une nouvelle instance maintenant, la façon la plus simple consiste à exécuter un exemple de script de déploiement automatisé. Suivez les instructions fournies dans [*Procédure : Configurer une instance et l’authentification (procédure scriptée)* ](how-to-set-up-instance-scripted.md) pour configurer une nouvelle instance et l’inscription de l’application Azure AD requise. Ces instructions contiennent également les étapes permettant de vérifier que vous avez correctement effectué chaque étape et que vous êtes prêt à passer à l’utilisation de votre nouvelle instance.

Dans ce tutoriel, vous aurez besoin des valeurs suivantes utilisées pour configurer votre instance. S’il vous faut récupérer ces valeurs, utilisez les liens ci-dessous pour accéder aux sections correspondantes de l’article d’installation et les rechercher dans le [portail Azure](https://portal.azure.com).
* **_Nom d’hôte_** de l’instance Azure Digital Twins ([à rechercher dans le portail](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* ID d’application (client) **_d’inscription de l’application Azure AD_** ([à rechercher dans le portail](how-to-set-up-instance-portal.md#collect-important-values))
* ID de répertoire (locataire) **_d’inscription de l’application Azure AD_** ([à rechercher dans le portail](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Obtenir le secret client d’inscription d’application

Vous devrez également créer un **_secret client_** pour l’inscription de votre application Azure AD. Pour ce faire, accédez à la page [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) dans le portail Azure (vous pouvez utiliser ce lien ou rechercher la page dans la barre de recherche du portail). Sélectionnez votre inscription dans la liste pour en afficher les détails. 

Appuyez sur *Certificats et secrets* dans le menu de l’inscription, puis sélectionnez *+ Nouveau secret client*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Vue dans le portail de l’inscription d’une application Azure AD. L’option « Certificats et secrets » est mise en surbrillance dans le menu de la ressource, tout comme la page « Nouveau secret client »":::

Entrez les valeurs de votre choix pour la description et l’expiration, puis appuyez sur *Ajouter*.
Le secret sera ajouté à la liste des secrets clients dans la page *Certificats et secrets*. Prenez note de la valeur pour l’utiliser ultérieurement (vous pouvez également la copier dans le presse-papiers avec l’icône Copier).

### <a name="add-a-digital-twin"></a>Ajouter un jumeau numérique

Cet article utilise Logic Apps pour mettre à jour un jumeau dans votre instance Azure Digital Twins. Pour continuer, vous devez ajouter au moins un jumeau dans votre instance. 

Vous pouvez ajouter des jumeaux à l’aide des [API DigitalTwins](how-to-use-apis-sdks.md), du [SDK .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) ou de la [CLI Azure Digital Twins](how-to-use-cli.md). Pour obtenir des instructions détaillées sur la façon de créer des jumeaux à l’aide de ces méthodes, consultez [*Procédure : Gérer des jumeaux numériques*](how-to-manage-twin.md).

Vous aurez besoin de **_l’ID du jumeau_** dans votre instance que vous avez créée.

## <a name="create-custom-logic-apps-connector"></a>Créer un connecteur Logic Apps personnalisé

Au cours de cette étape, vous allez créer un [connecteur Logic Apps personnalisé](../logic-apps/custom-connector-overview.md) pour les API Azure Digital Twins. Après cela, vous pourrez raccorder Azure Digital Twins lors de la création d’une application logique dans la section suivante.

Accédez à la page [Connecteur personnalisé Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) dans le portail Azure (vous pouvez utiliser ce lien ou rechercher la page dans la barre de recherche du portail). Appuyez sur *+ Ajouter*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Page « Connecteur personnalisé Logic Apps » dans le portail Azure. Bouton « Ajouter » mis en surbrillance":::

Dans la page *Créer un connecteur personnalisé Logic Apps* qui suit, sélectionnez votre abonnement et votre groupe de ressources, ainsi qu’un nom et un emplacement de déploiement pour votre nouveau connecteur. Appuyez sur *Vérifier + créer*. Cela vous permet d’atteindre l’onglet *Vérifier + créer*, où vous pouvez appuyer sur *Créer* en bas pour créer votre ressource.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Onglet « Vérifier + créer » de la page « Créer un connecteur personnalisé Logic Apps » dans le portail Azure. Bouton « Créer » mis en surbrillance":::

Vous serez dirigé vers la page de déploiement pour le connecteur. Une fois le déploiement terminé, cliquez sur le bouton *Accéder à la ressource* pour afficher les détails du connecteur dans le portail.

### <a name="configure-connector-for-azure-digital-twins"></a>Configurer le connecteur pour Azure Digital Twins

Ensuite, vous allez configurer le connecteur que vous avez créé pour atteindre Azure Digital Twins.

Tout d’abord, téléchargez un fichier Swagger Azure Digital Twins personnalisé qui a été modifié pour fonctionner avec Logic Apps. Téléchargez *digitaltwins.json* à partir de [ce lien](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json).

Ensuite, à partir de la page Vue d’ensemble de votre connecteur dans le portail Azure, appuyez sur *Modifier*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Page « Vue d’ensemble » pour le connecteur créé à l’étape précédente. Bouton « Modifier » mis en surbrillance":::

Dans la page *Modifier le connecteur personnalisé Logic Apps* qui suit, configurez les informations suivantes :
* **Connecteurs personnalisés**
    - Point de terminaison d’API : REST (conservez la valeur par défaut)
    - Mode d’importation : Fichier OpenAPI (conservez la valeur par défaut)
    - Fichier : Il s’agit du fichier Swagger personnalisé que vous avez téléchargé précédemment. Appuyez sur *Importer*, localisez le fichier sur votre ordinateur, puis cliquez sur *Ouvrir*.
* **Informations générales**
    - Icône, Couleur d’arrière-plan de l’icône, Description : Remplissez les valeurs de votre choix.
    - Schéma : HTTPS (conservez la valeur par défaut)
    - Hôte : Le *nom d’hôte* de votre instance Azure Digital Twins.
    - URL de base : / (conservez la valeur par défaut)

Ensuite, appuyez sur le bouton *Sécurité* au bas de la fenêtre pour passer à l’étape de configuration suivante.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Capture d’écran du bas de la page « Modifier le connecteur personnalisé Logic Apps ». Mise en surbrillance du bouton permettant d’accéder à Sécurité":::

À l’étape Sécurité, appuyez sur *Modifier* et configurez les informations suivantes :
* **Type d'authentification** : OAuth 2.0
* **OAuth 2.0** :
    - Fournisseur d’identité : Azure Active Directory
    - ID client : *ID d’application (client)* pour l’inscription de votre application Azure AD
    - Clé secrète client : *Secret client* que vous avez créé dans les [*Prérequis*](#prerequisites) pour l’inscription de votre application Azure AD
    - URL de connexion : https://login.windows.net (conservez la valeur par défaut)
    - ID de locataire : *ID d’annuaire (locataire)* pour l’inscription de votre application Azure AD
    - URL de la ressource : 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Étendue : Directory.AccessAsUser.All
    - URL de redirection : (conservez la valeur par défaut pour l’instant)

Notez que le champ URL de redirection indique *Enregistrer le connecteur personnalisé pour générer l’URL de redirection*. Pour cela, appuyez sur *Mettre à jour le connecteur* en haut du volet pour confirmer les paramètres du connecteur.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Capture d’écran du haut de la page « Modifier le connecteur personnalisé Logic Apps ». Bouton « Mettre à jour le connecteur » mis en surbrillance":::

<!-- Success message? didn't see one -->

Revenez au champ URL de redirection et copiez la valeur qui a été générée. Vous l’utiliserez à l’étape suivante.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Le champ URL de redirection de la page « Modifier le connecteur personnalisé Logic Apps » a désormais la valeur « https://logic-apis-westus2.consent.azure-apim.net/redirect  ». Le bouton permettant de copier la valeur est mis en surbrillance.":::

Il s’agit de toutes les informations nécessaires à la création de votre connecteur (inutile de passer de l’étape Sécurité à l’étape Définition). Vous pouvez fermer le volet *Modifier le connecteur personnalisé Logic Apps*.

>[!NOTE]
>De retour dans la page Vue d’ensemble de votre connecteur, où vous avez appuyez sur *Modifier*, notez que le fait d’appuyer à nouveau sur *Modifier* redémarre tout le processus de saisie de vos choix de configuration. Cette opération ne renseigne pas vos valeurs entrées la dernière fois. Par conséquent, si vous souhaitez enregistrer une configuration mise à jour avec des valeurs modifiées, vous devez entrer à nouveau toutes les autres valeurs également pour éviter qu’elles ne soient remplacées par les valeurs par défaut.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Accorder des autorisations de connecteur dans l’application Azure AD

Ensuite, utilisez la valeur *URL de redirection* du connecteur personnalisé que vous avez copiée à la dernière étape pour accorder les autorisations de connecteur dans l’inscription de votre application Azure AD.

Accédez à la page [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) du portail Azure et sélectionnez votre inscription dans la liste. 

Sous *Authentification* dans le menu de l’inscription, ajoutez un URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Page Authentification pour l’inscription de l’application dans le portail Azure. « Authentification » dans le menu est mis en surbrillance, et dans la page, le bouton « Ajouter un URI » est mis en surbrillance."::: 

Entrez *l’URL de redirection* du connecteur personnalisé dans le nouveau champ, puis cliquez sur l’icône *Enregistrer*.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Page Authentification pour l’inscription de l’application dans le portail Azure. La nouvelle URL de redirection est mise en surbrillance, ainsi que le bouton « Enregistrer » pour la page.":::

Vous avez maintenant terminé la configuration d’un connecteur personnalisé qui peut accéder aux API Azure Digital Twins. 

## <a name="create-logic-app"></a>Créer une application logique

Ensuite, vous allez créer une application logique qui utilisera votre nouveau connecteur pour automatiser les mises à jour Azure Digital Twins.

Accédez à la page [Logic Apps](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) dans le portail Azure (vous pouvez utiliser ce lien ou rechercher la page dans la barre de recherche du portail). Appuyez sur *Créer une application logique*.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Page « Logic Apps » dans le portail Azure. Le bouton « Créer une application logique » est mis en surbrillance":::

Dans la page *Logic Apps* qui suit, sélectionnez votre abonnement et votre groupe de ressources, ainsi qu’un nom et un emplacement de déploiement pour votre nouvelle application logique. Appuyez sur *Vérifier + créer*. Cela vous permet d’atteindre l’onglet *Vérifier + créer*, où vous pouvez appuyer sur *Créer* en bas pour créer votre ressource.

Vous serez dirigé vers la page de déploiement pour l’application logique. Une fois le déploiement terminé, appuyez sur le bouton *Accéder à la ressource* pour accéder à *Concepteur Logic Apps*, où vous allez renseigner la logique du flux de travail.

### <a name="design-workflow"></a>Flux de travail de conception

Dans le *Concepteur Logic Apps*, sous *Démarrer avec un déclencheur courant*, sélectionnez _**Récurrence**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Page « Concepteur Logic Apps » dans le portail Azure. Déclencheur courant « Récurrence » mis en surbrillance":::

Dans la page *Concepteur Logic Apps* qui suit, définissez la fréquence de **Récurrence** sur *Seconde*, afin que l’événement soit déclenché toutes les 3 secondes. Cela vous permettra de voir plus facilement les résultats, sans avoir à attendre très longtemps.

Appuyez sur *+ Nouvelle étape*.

Cela ouvre une zone *Choisir une action*. Basculez vers l’onglet *Personnalisé*. Votre connecteur personnalisé précédemment doit s’afficher dans la zone supérieure.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Création d’un flux dans le Concepteur Logic Apps dans le portail Azure. Dans la zone « Choisir une action », l’onglet « Personnalisé » est sélectionné. Le connecteur personnalisé créé précédemment par l’utilisateur est affiché dans la zone, mis en surbrillance.":::

Sélectionnez-le pour afficher la liste des API contenues dans ce connecteur. Utilisez la barre de recherche ou faites défiler la liste pour sélectionner **DigitalTwins_Add**. (Il s’agit de l’API utilisée dans cet article, mais vous pouvez également sélectionner n’importe quelle autre API comme choix valide pour une connexion Logic Apps).

Vous serez peut-être invité à entrer vos informations d’identification Azure pour vous connecter au connecteur. Si une boîte de dialogue *Autorisations demandées* s’affiche, suivez les invites afin d’accorder le consentement pour votre application et d’accepter.

Dans la nouvelle zone *DigitalTwinsAdd*, renseignez les champs comme suit :
* id : Renseignez *l’ID de jumeau* du jumeau numérique dans votre instance que l’application logique doit mettre à jour.
* Item - 1 : Ce champ vous permet d’entrer le corps requis par la demande d’API choisie. Pour *DigitalTwinsUpdate*, ce corps est sous la forme d’un code de correctif JSON. Pour plus d’informations sur la structuration d’un correctif JSON afin de mettre à jour votre jumeau, consultez la section [Mettre à jour un jumeau numérique](how-to-manage-twin.md#update-a-digital-twin) dans *Procédure : Gestion des jumeaux numériques*.
* api-version : Dans la préversion publique actuelle, cette valeur est *2020-05-31-preview*

Appuyez sur *Enregistrer* dans le Concepteur Logic Apps.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Vue finale de l’application dans le connecteur Logic App. La zone DigitalTwinsAdd est renseignée avec les valeurs décrites ci-dessus, notamment un exemple de corps de correctif JSON. Le bouton « Enregistrer » de la fenêtre est mis en surbrillance.":::

## <a name="query-twin-to-see-the-update"></a>Interroger le jumeau pour voir la mise à jour

Maintenant que votre application logique a été créée, l’événement de mise à jour du jumeau que vous avez défini dans le Concepteur Logic Apps doit se produire avec une récurrence toutes les trois secondes. Cela signifie qu’au bout de trois secondes, vous devriez être en mesure d’interroger votre jumeau et de voir les nouvelles valeurs corrigées.

Vous pouvez interroger votre jumeau par le biais de la méthode de votre choix (par exemple, une [application cliente personnalisée](tutorial-command-line-app.md), [l’exemple d’application Azure Digital Twins Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), les [SDK et les API](how-to-use-apis-sdks.md) ou la [CLI](how-to-use-cli.md)). 

Pour plus d’informations sur l’interrogation de votre instance Azure Digital Twins, consultez [*Procédure : Interroger le graphique de jumeaux*](how-to-query-graph.md).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une application logique qui met régulièrement à jour un jumeau dans votre instance Azure Digital Twins avec un correctif que vous avez fourni. Vous pouvez essayer de sélectionner d’autres API dans le connecteur personnalisé afin de créer des applications Logic Apps pour effectuer diverses actions sur votre instance.

Pour en savoir plus sur les opérations d’API disponibles et les détails requis, consultez [*Procédure : Utiliser les API et les kits de développement logiciel (SDK) Azure Digital Twins*](how-to-use-apis-sdks.md).