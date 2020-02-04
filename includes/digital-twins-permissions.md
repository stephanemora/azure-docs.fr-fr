---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748872"
---
>[!NOTE]
>Cette section fournit les instructions sur [l’inscription de l'application Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Dans le [Portail Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le menu de gauche développable, puis ouvrez le volet **Inscriptions d’applications**. 

    [![Sélectionnez le volet Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Sélectionnez le bouton **+ Nouvelle inscription**.

    [![Sélectionner le bouton Nouvelle inscription](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Dans la zone **Nom**, donnez un nom convivial à l’inscription de cette application. Sous la section **URI de redirection (facultatif)** , choisissez **Client public/natif (mobile et bureau)** dans le menu déroulant de gauche, puis entrez `https://microsoft.com` dans la zone de texte à droite. Sélectionnez **Inscription**.

    [![Volet Créer](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Pour vous assurer que [l’application est inscrite comme **client public**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), ouvrez le volet **Authentification** de votre inscription d’application, et faites défiler vers le bas dans ce volet. Dans la section **Type de client par défaut**, choisissez **Oui** pour **Considérer l’application comme un client public** et appuyez sur **Enregistrer**.

    1. **L’URI de redirection** doit correspondre à l’adresse fournie par la requête d’authentification :

        * Pour les applications hébergées dans un environnement de développement local, sélectionnez **Client public (mobile et bureau)** . Veillez à définir **Type de client par défaut** sur Oui.
        * Pour les applications à page unique hébergées sur Azure App Service, sélectionnez **Web**.

        Sélectionnez **Client public (mobile et bureau)** , puis entrez `http://localhost:8080/`.

        [![Configurer les URI de redirection](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. Cochez la case **Jetons d’accès** pour affecter au paramètre **oauth2AllowImplicitFlow** la valeur `true` dans le fichier **manifeste** JSON de votre ressource.

        [![Paramètres de configuration du client public](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  Ouvrez le volet **Vue d’ensemble** de votre application inscrite, et copiez les valeurs des entités suivantes dans un fichier temporaire. Ces valeurs vous permettront de configurer votre exemple d’application dans les sections suivantes.

    - **ID d’application (client)**
    - **ID de l’annuaire (locataire)**

    [![ID d’application Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Ouvrez le volet **API autorisées** pour l’inscription de votre application. Sélectionnez le bouton **+ Ajouter une autorisation**. Dans le volet **Demander des autorisations d’API**, sélectionnez l’onglet **API utilisées par mon organisation**, puis recherchez un des éléments suivants :
    
    1. `Azure Digital Twins`. Sélectionnez l’API **Azure Digital Twins**.

        [![Rechercher Azure Digital Twins dans l’API](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Vous pouvez également rechercher `Azure Smart Spaces Service`. Sélectionnez l’API **Azure Smart Spaces Service**.

        [![Rechercher Azure Smart Spaces dans l’API](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Le nom et l’ID de l’API Azure AD qui s’affichent dépendent de votre locataire :
    > * Les comptes de locataire et client utilisés pour les tests doivent rechercher `Azure Digital Twins`.
    > * Les autres comptes Microsoft doivent rechercher `Azure Smart Spaces Service`.

1. L'une ou l’autre API apparaît sous **Azure Digital Twins** dans le même volet **Demander des autorisations d’API**. Sélectionnez l'option de liste déroulante **Read** (Lire), puis activez la case à cocher **Read.Write** (Lire.Écrire). Sélectionnez le bouton **Ajouter des autorisations**.

    [![Ajouter des autorisations d’API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Selon les paramètres de votre organisation, vous devrez peut-être prendre des mesures supplémentaires pour autoriser l’accès de l’administrateur à cette API. Contactez votre administrateur pour obtenir plus d’informations. Une fois l’accès administrateur approuvé, la colonne **Consentement administrateur requis** du volet **Autorisations des API** affichera vos autorisations. 

    [![Approbation de consentement administrateur](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Vérifiez que **Azure Digital Twins** s’affiche.
