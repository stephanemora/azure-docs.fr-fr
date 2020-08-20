---
title: Configurer une instance et l’authentification (portail)
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer une instance du service Azure Digital Twins à l’aide du portail Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 33772d46f363b161c1faa5c953f48a702ae2b8bf
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009646"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurer une instance Azure Digital Twins et l’authentification (portail)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Cet article explique comment **configurer une nouvelle instance Azure Digital Twins**, notamment la création de l’instance et la configuration de l’authentification. À l’issue de cet article, vous aurez une instance Azure Digital Twins prête pour la programmation.

Cette version de cet article suit ces étapes manuellement, une par une, à l’aide du portail Azure. Le portail Azure est une console web unifiée qui offre une alternative aux outils en ligne de commande.
* Pour suivre ces étapes manuellement à l’aide de CLI, consultez la version CLI de cet article : [*Guide pratique : Configurer une instance et l’authentification (CLI)* ](how-to-set-up-instance-cli.md).
* Pour exécuter une configuration automatisée à l’aide d’un exemple de script de déploiement, consultez la version avec script de cet article : [*Guide pratique : Configurer une instance et l’authentification (procédure scriptée)* ](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Créer l’instance Azure Digital Twins

Dans cette section, vous allez **créer une nouvelle instance d’Azure Digital Twins** à l’aide du [portail Azure](https://ms.portal.azure.com/). Accédez au portail et connectez-vous avec vos informations d’identification.

Une fois dans le portail, commencez par sélectionner _Créer une ressource_ dans le menu de la page d’accueil des services Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Sélection de l’option « créer une ressource » dans la page d’accueil du portail Azure":::

Recherchez *Azure Digital Twins* dans la zone de recherche, puis choisissez le service **Azure Digital Twins (préversion)** dans les résultats. Sélectionnez le bouton _Créer_ pour créer une instance du service.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Sélection de l’option « Créer » dans la page du service Azure Digital Twins":::

Sur la page *Créer une ressource*, renseignez les valeurs ci-dessous :
* **Abonnement**: Abonnement Azure que vous utilisez.
  - **Groupe de ressources** : Groupe de ressources dans lequel déployer l’instance. Si vous n’avez pas de groupe de ressources existant à l’esprit, vous pouvez en créer un ici en sélectionnant le lien *Créer* et en entrant un nom pour le nouveau groupe de ressources.
* **Emplacement** : Région prenant en charge Azure Digital Twins pour le déploiement. Pour plus d’informations sur la prise en charge régionale, visitez [*Produits Azure disponibles par région (Azure Digital Twins)* ](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nom de la ressource** : Nom de votre instance Azure Digital Twins. Le nom de la nouvelle instance doit être unique dans la région pour votre abonnement (ce qui signifie que si votre abonnement a une autre instance Azure Digital Twins dans cette région, qui utilise déjà le nom que vous choisissez, vous devrez choisir un autre nom).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Remplissage des valeurs décrites pour créer une ressource Azure Digital Twins":::

Quand vous avez terminé, sélectionnez _Vérifier + créer_. Une page de résumé s’affiche, dans laquelle vous pouvez consulter les détails de l’instance que vous avez entrés et appuyer sur _Créer_. 

### <a name="verify-success-and-collect-important-values"></a>Vérifier la réussite de l’exécution et collecter les valeurs importantes

Après avoir appuyé sur *Créer*, vous pouvez afficher l’état du déploiement de votre instance dans vos notifications Azure le long de la barre d’icônes du portail. La notification indique quand le déploiement a réussi, et vous pourrez sélectionner le bouton _Accéder à la ressource_ pour afficher votre instance créée.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Vue des notifications Azure montrant un déploiement réussi et mettant en surbrillance le bouton « Accéder à la ressource »":::

Autrement, si le déploiement échoue, la notification indique pourquoi. Lisez le conseil du message d’erreur, puis réessayez de créer l’instance.

>[!TIP]
>Une fois votre instance créée, vous pouvez revenir à sa page à tout moment en recherchant son nom dans la barre de recherche du portail Azure.

À partir de la page *Vue d’ensemble* de l’instance, notez son *Nom*, son *Groupe de ressources* et son *Nom d’hôte*. Il s’agit de toutes les valeurs importantes dont vous pouvez avoir besoin quand vous continuez à travailler avec votre instance Azure Digital Twins. Si d’autres utilisateurs doivent programmer pour l’instance, vous devez partager ces valeurs avec eux.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Mise en surbrillance des valeurs importantes de la page de présentation de l’instance":::

Vous disposez maintenant d’une instance Azure Digital Twins opérationnelle. Ensuite, vous allez accorder les autorisations utilisateur Azure appropriées pour la gérer.

## <a name="set-up-user-access-permissions"></a>Configurer les autorisations d’accès utilisateur

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Commencez par ouvrir la page de votre instance Azure Digital Twins dans le portail Azure. Dans le menu de l’instance, sélectionnez *Contrôle d’accès (IAM)* . Sélectionnez le bouton *Ajouter* sous *Ajouter une attribution de rôle*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Sélection de l’ajout d’une attribution de rôle à partir de la page « Contrôle d’accès (IAM) »":::

Sur la page *Ajouter une attribution de rôle* ci-dessous, renseignez les valeurs (elles doivent être complétées par un utilisateur disposant des [autorisations suffisantes](#prerequisites-permission-requirements) dans l’abonnement Azure) :
* **Rôle** : Sélectionnez *Azure Digital Twins Owner (préversion)* dans le menu déroulant.
* **Attribuer l’accès à** : Sélectionnez *Utilisateur, groupe ou principal de service Azure AD* dans le menu déroulant.
* **Select** : Recherchez le nom ou l’adresse e-mail de l’utilisateur à attribuer. Lorsque vous sélectionnez le résultat, l’utilisateur s’affiche dans une section *Membres sélectionnés*.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Remplissage des champs répertoriés dans la boîte de dialogue « Ajouter une attribution de rôle »":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Lorsque vous avez fini d’entrer les détails, cliquez sur le bouton *Enregistrer*.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

Vous pouvez afficher l’attribution de rôle que vous avez configurée sous *Contrôle d’accès (IAM) > Attributions de rôle*. L’utilisateur doit s’afficher dans la liste avec un rôle de *Propriétaire Azure Digital Twins (préversion)* . 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vue des attributions de rôles pour une instance Azure Digital Twins dans le portail Azure":::

Vous disposez maintenant d’une instance Azure Digital Twins prête à l’emploi et des autorisations pour la gérer. Ensuite, vous allez configurer des autorisations pour qu’une application cliente y accède.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurer des autorisations d’accès pour les applications clientes

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Commencez par accéder à [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) dans le portail Azure (vous pouvez utiliser ce lien ou le trouver à l’aide de la barre de recherche du portail). Sélectionnez *Inscriptions d’applications* dans le menu service, puis *+ Nouvelle inscription*.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Vue de la page du service Azure AD dans le portail Azure, mettant en surbrillance l’option de menu « inscriptions d’applications » et le bouton « + Nouvelle inscription »":::

Dans la page *Inscrire une application* qui suit, renseignez les valeurs demandées :
* **Name** : Nom d’affichage d’application Azure AD à associer à l’inscription.
* **Types de comptes pris en charge** : Select *Comptes dans cet annuaire organisationnel uniquement (Annuaire par défaut uniquement – Locataire unique)*
* **URI de redirection** : *URL de réponse de l’application Azure AD* pour l’application Azure AD. Vous pouvez utiliser `http://localhost`.

Lorsque vous avez terminé, cliquez sur le bouton *S’inscrire*.

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Vue de la page « inscrire une application » avec les valeurs décrites renseignées":::

Une fois la configuration de l’inscription terminée, le portail vous redirige vers la page de détails correspondante.

### <a name="provide-azure-digital-twins-api-permission"></a>Fournir une autorisation d’API Azure Digital Twins

Ensuite, configurez l’inscription d’application que vous avez créée avec les autorisations de base pour les API Azure Digital Twins.

À partir de la page du portail pour l’inscription de votre application, sélectionnez *Autorisations des API* dans le menu. Sur la page d’autorisations suivantes, cliquez sur le bouton *+ Ajouter une autorisation*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Vue de l’inscription de l’application dans le portail Azure, mettant en surbrillance l’option de menu « Autorisations des API » et le bouton « + Ajouter une autorisation »":::

Dans la page *Demander des autorisations d’API* qui suit, basculez vers l’onglet *API utilisées par mon organisation* et recherchez *Azure Digital Twins*. Sélectionnez *Azure Digital Twins* dans les résultats de la recherche pour continuer à attribuer des autorisations pour les API Azure Digital Twins.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Vue du résultat de recherche de la page « Demander des autorisations d’API » montrant Azure Digital Twins":::

Ensuite, vous allez sélectionner les autorisations à accorder pour ces API. Développez l’autorisation **Lecture (1)** , et activez la case *lecture.Écriture* pour accorder cette inscription d’application et les autorisations de lecture et d’écriture.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Vue de la page « Demander des autorisations d’API » en sélectionnant les autorisations « Lecture.Écriture » pour les API Azure Digital Twins":::

Lorsque vous avez terminé, appuyez sur *Ajouter des autorisations*.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

De retour sur la page *Autorisations des API*, vérifiez qu’il existe désormais une entrée pour Azure Digital Twins reflétant les autorisations Lecture/Écriture :

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Vue du portail des autorisations d’API pour l’inscription d’application Azure AD, affichant « Accès en lecture/écriture » pour Azure Digital Twins":::

Vous pouvez également vérifier la connexion à Azure Digital Twins dans le fichier *manifest.json* de l’inscription de l’application, qui a été automatiquement mise à jour avec les informations Azure Digital Twins lorsque vous avez ajouté les autorisations des API.

Pour ce faire, sélectionnez *Manifeste* dans le menu pour afficher le code du manifeste de l’inscription de l’application. Faites défiler la fenêtre de code vers le bas et recherchez ces champs sous `requiredResourceAccess`. Les valeurs doivent correspondre à celles de la capture d’écran ci-dessous :

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Affichage du portail du manifeste pour l’inscription d’application Azure AD. Nested under ’requiredResourceAccess’, there’s a ’resourceAppId’ value of 0b07f429-9f4b-4714-9392-cc5e8e80c8b0, and a ’resourceAccess > id’ value of 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Collecter les valeurs importantes

Ensuite, sélectionnez *Vue d’ensemble* dans la barre de menus pour afficher les détails de l’inscription de l’application :

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Vue du portail des valeurs importantes pour l’inscription de l’application":::

Prenez note de *l’ID d’application (client)* et de *l’ID de répertoire (locataire)* affichés sur **votre** page. Ces valeurs seront requises ultérieurement pour [authentifier une application cliente par rapport aux API Azure Digital Twins](how-to-authenticate-client.md). Si vous n’êtes pas la personne chargée d’écrire du code pour de telles applications, vous devez partager ces valeurs avec la personne qui en sera chargée.

### <a name="other-possible-steps-for-your-organization"></a>Autres étapes possibles pour votre organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment connecter votre application cliente à votre instance en écrivant le code d’authentification de l’application cliente :
* [*Guide pratique : Écrire le code d’authentification de l’application*](how-to-authenticate-client.md)