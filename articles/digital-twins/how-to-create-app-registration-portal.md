---
title: Créer une inscription d’application (portail)
titleSuffix: Azure Digital Twins
description: Découvrez comment créer une inscription d’application Azure AD en tant qu’option d’authentification pour les applications clientes en utilisant le portail Azure.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 17f671a945c569d5492f84967a81931606d4cea1
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110989464"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-portal"></a>Créer une inscription d’application à utiliser avec Azure Digital Twins (portail)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Lors de l’utilisation d’une instance Azure Digital Twins, il est courant d’interagir avec cette instance par le biais d’applications clientes, comme l’application cliente personnalisée générée dans [Tutoriel : Coder une application cliente](tutorial-code.md). Ces applications doivent s’authentifier auprès d’Azure Digital Twins pour interagir avec, et certains des [mécanismes d’authentification](how-to-authenticate-client.md) que les applications peuvent utiliser impliquent une **inscription d’application** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md).

Cela n’est pas obligatoire pour tous les scénarios d’authentification. Toutefois, si vous utilisez une stratégie d’authentification ou un exemple de code qui nécessite une inscription d’application, cet article vous montre comment en définir une avec le [portail Azure](https://portal.azure.com). Il explique également comment [collecter les valeurs importantes](#collect-important-values) dont vous aurez besoin pour utiliser l’inscription d’application afin de vous authentifier.

## <a name="azure-ad-app-registrations"></a>Inscriptions d’applications Azure AD

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) est le service Microsoft basé sur le cloud qui gère les identités et les accès. La configuration d’une **inscription d’application** dans Azure AD est un moyen d’accorder à une application cliente l’accès à Azure Digital Twins.

Cette inscription d’application est l’emplacement où vous configurez les autorisations d’accès aux [API d’Azure Digital Twins](concepts-apis-sdks.md). Plus tard, les applications clientes peuvent s’authentifier auprès de l’inscription d’application à l’aide des **valeurs d’ID client et de locataire** de l’inscription, et ainsi recevoir les autorisations d’accès configurées aux API.

>[!TIP]
> Vous préférerez peut-être configurer une nouvelle inscription d’application chaque fois que vous en aurez besoin, *ou* pour effectuer cette opération une seule fois, en établissant une inscription d’application unique qui sera partagée entre tous les scénarios qui l’exigent.

## <a name="create-the-registration"></a>Création de l’inscription

Commencez par accéder à [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) dans le portail Azure (vous pouvez utiliser ce lien ou le trouver à l’aide de la barre de recherche du portail). Sélectionnez *Inscriptions d’applications* dans le menu service, puis *+ Nouvelle inscription*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Capture d’écran de la page du service Azure AD dans le portail Azure, montrant les étapes de la création d’une inscription dans la page « Inscriptions d’applications ».":::

Dans la page *Inscrire une application* qui suit, renseignez les valeurs demandées :
* **Name** : Nom d’affichage d’application Azure AD à associer à l’inscription.
* **Types de comptes pris en charge** : Select *Comptes dans cet annuaire organisationnel uniquement (Annuaire par défaut uniquement – Locataire unique)*
* **URI de redirection** : *URL de réponse de l’application Azure AD* pour l’application Azure AD. Ajoutez un URI *Client public/natif (mobile et bureau)* pour `http://localhost`.

Lorsque vous avez terminé, sélectionnez le bouton *S’inscrire*.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Capture d’écran de la page « Inscrire une application » dans le portail Azure avec les valeurs décrites renseignées.":::

Une fois la configuration de l’inscription terminée, le portail vous redirige vers la page de détails correspondante.

## <a name="collect-important-values"></a>Collecter les valeurs importantes

Ensuite, collectez certaines valeurs importantes sur l’inscription d’application dont vous aurez besoin pour utiliser l’inscription d’application afin d’authentifier une application cliente. Ces valeurs incluent :
* **Nom de la ressource**
* **ID client**
* **ID locataire**
* **Secret client**

Pour utiliser Azure Digital Twins, le **nom de la ressource** est `http://digitaltwins.azure.net`.

Les sections suivantes décrivent comment trouver les autres valeurs.

### <a name="collect-client-id-and-tenant-id"></a>Collecter l’ID de client et l’ID de locataire

Vous pouvez collecter les valeurs de l’**ID client** et de l’**ID locataire** à partir de la page de détails de l’inscription d’application dans le portail Azure :

:::image type="content" source="media/how-to-create-app-registration/client-id-tenant-id.png" alt-text="Capture d’écran du portail Azure montrant les valeurs importantes pour l’inscription d’application.":::

Prenez note de **l’ID d’application (client)** et de **l’ID de répertoire (locataire)** affichés sur **votre** page.

### <a name="collect-client-secret"></a>Collecter le secret client

Pour configurer un **secret client** pour votre inscription d’application, accédez d’abord à la page d’inscription d’application dans le portail Azure. 

1. Sélectionnez **Certificats et secrets** dans le menu de l’inscription, puis **+ Nouveau secret client**.

    :::image type="content" source="media/how-to-create-app-registration/client-secret.png" alt-text="Capture d’écran du portail Azure avec une inscription d’application Azure AD et l’option « Nouveau secret client » mise en évidence.":::

1. Entrez les valeurs de votre choix pour Description et Expire le, puis sélectionnez **Ajouter**.

    :::image type="content" source="media/how-to-create-app-registration/add-client-secret.png" alt-text="Capture d’écran du portail Azure lors de l’ajout d’un secret client.":::

1. Vérifiez que le secret client est visible dans la page **Certificats et secrets** avec les champs Expire et Valeur. 

1. Notez l’**ID du secret** et la **Valeur** en vue de les utiliser plus tard (vous pouvez également les copier dans le Presse-papiers avec les icônes Copier).

    :::image type="content" source="media/how-to-create-app-registration/client-secret-value.png" alt-text="Capture d’écran du portail Azure montrant comment copier la valeur du secret client.":::

>[!IMPORTANT]
>Veillez à copier les valeurs tout de suite et à les stocker en lieu sûr, car vous ne pourrez plus y accéder par la suite. Si vous ne les retrouvez pas, vous devrez créer un autre secret.

## <a name="provide-azure-digital-twins-api-permission"></a>Fournir une autorisation d’API Azure Digital Twins

Ensuite, configurez l’inscription d’application que vous avez créée avec les autorisations de base pour les API Azure Digital Twins.

À partir de la page du portail pour l’inscription de votre application, sélectionnez *Autorisations des API* dans le menu. Sur la page d’autorisations suivantes, sélectionnez le bouton *+Ajouter une autorisation*.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Capture d’écran de l’inscription d’application dans le portail Azure, avec l’option de menu « Autorisations de l’API » et le bouton « Ajouter une autorisation » mis en évidence.":::

Dans la page *Demander des autorisations d’API* qui suit, basculez vers l’onglet *API utilisées par mon organisation* et recherchez *Azure Digital Twins*. Sélectionnez _**Azure Digital Twins**_ dans les résultats de la recherche pour continuer à attribuer des autorisations pour les API Azure Digital Twins.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Capture d’écran du résultat de recherche de la page « Demander des autorisations d’API » dans le portail Azure montrant Azure Digital Twins.":::

>[!NOTE]
> Si votre abonnement dispose encore d’une instance Azure Digital Twins de la préversion publique précédente du service (avant juillet 2020), vous devez rechercher et sélectionner _**Azure Smart Spaces Services**_ à la place. Il s’agit d’un ancien nom pour le même ensemble d’API (notez que l’*ID d’application (client)* est le même que dans la capture d’écran ci-dessus), et votre expérience ne sera pas modifiée au-delà de cette étape.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Capture d’écran du résultat de recherche de la page « Demander des autorisations d’API » avec Azure Smart Spaces Service dans le portail Azure.":::

Ensuite, vous allez sélectionner les autorisations à accorder pour ces API. Développez l’autorisation **Lecture (1)** , et activez la case *lecture.Écriture* pour accorder cette inscription d’application et les autorisations de lecture et d’écriture.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Capture d’écran de la page « Demander des autorisations d’API » et sélection des autorisations « Read.Write » pour les API Azure Digital Twins dans le portail Azure.":::

Lorsque vous avez terminé, sélectionnez *Ajouter des autorisations*.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

Sur la page *Autorisations des API*, vérifiez qu’il existe désormais une entrée pour Azure Digital Twins reflétant les autorisations Lecture/Écriture :

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Capture d’écran des autorisations d’API pour l’inscription d’application Azure AD dans le portail Azure, avec « Accès en lecture/écriture » pour Azure Digital Twins.":::

Vous pouvez également vérifier la connexion à Azure Digital Twins dans le fichier *manifest.json* de l’inscription de l’application, qui a été automatiquement mise à jour avec les informations Azure Digital Twins lorsque vous avez ajouté les autorisations des API.

Pour ce faire, sélectionnez **Manifeste** dans le menu pour afficher le code du manifeste de l’inscription de l’application. Faites défiler la fenêtre de code vers le bas et recherchez les champs et valeurs suivants sous `requiredResourceAccess` : 
* `"resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0"`
* `"resourceAccess"` > `"id": "4589bd03-58cb-4e6c-b17f-b580e39652f8"`

Ces valeurs sont indiquées dans la capture d’écran ci-dessous :

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Capture d’écran du manifeste pour l’inscription d’application Azure AD dans le portail Azure.":::

Si ces valeurs sont manquantes, recommencez les étapes de la [section relative à l’ajout d’autorisations d’API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Autres étapes possibles pour votre organisation

Il est possible que votre organisation exige des actions supplémentaires de la part des propriétaires/administrateurs d’abonnement pour configurer correctement une inscription d’application. Les étapes requises peuvent varier en fonction des paramètres spécifiques de votre organisation.

Voici quelques activités courantes qu’un propriétaire/administrateur pour l’abonnement peut devoir effectuer. Ces opérations et d’autres peuvent être effectuées à partir de la page [Inscriptions d’applications Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) dans le portail Azure.
* Accordez le consentement administrateur pour l’inscription d’application. Votre organisation peut avoir activé globalement l’option *Consentement administrateur requis* dans Azure AD pour toutes les inscriptions d’applications au sein de votre abonnement. Si c’est le cas, le propriétaire/l’administrateur devra sélectionner ce bouton pour votre société dans la page *Autorisations de l’API* de l’inscription d’application pour que celle-ci soit valide :

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Capture d’écran du portail Azure montrant le bouton « Accorder un consentement administrateur » sous les autorisations d’API.":::
  - Si le consentement a été accordé avec succès, l’entrée pour Azure Digital Twins doit alors indiquer une valeur d’*État* _Accordé pour **(votre société)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Capture d’écran du portail Azure montrant le consentement administrateur accordé pour l’entreprise sous les autorisations d’API.":::
* Activer l’accès client public
* Définir des URL de réponse spécifiques pour l’accès web et au bureau
* Autoriser les flux d’authentification OAuth2 implicites

Pour plus d’informations sur l’inscription d’applications et ses différentes options d’installation, consultez [Inscrire une application avec la plateforme d’identités Microsoft](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous configurez une inscription d’application Azure AD qui peut être utilisée pour authentifier des applications clientes avec les API Azure Digital Twins.

Vous lirez ensuite des informations sur les mécanismes d’authentification, y compris ceux qui utilisent les inscriptions d’applications et d’autres qui ne le font pas :
* [Guide pratique : Écrire le code d’authentification de l’application](how-to-authenticate-client.md)