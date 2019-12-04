---
title: Déployer un outil de gestion - Azure
description: Guide pratique pour installer un outil d’interface utilisateur permettant de gérer les ressources Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: ad0c67cea6a5a9b487cd47aa7c10d10da1438050
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384279"
---
# <a name="tutorial-deploy-a-management-tool"></a>Didacticiel : Déployer un outil de gestion

L’outil de gestion fournit une interface utilisateur (IU) pour la gestion des ressources Microsoft Virtual Desktop. Dans ce tutoriel, vous allez découvrir comment déployer et vous connecter à l’outil de gestion.

>[!NOTE]
>Ces instructions concernent une configuration spécifique à Windows Virtual Desktop, que vous pouvez utiliser avec les processus existants de votre organisation.

## <a name="important-considerations"></a>Points importants à prendre en compte

Étant donné que l’application nécessite un consentement pour interagir avec Windows Virtual Desktop, cet outil ne prend pas en charge les scénarios B2B (Business-to-Business). Chaque abonnement du locataire Azure Active Directory (AAD) nécessite son propre déploiement de l’outil de gestion.

Cet outil de gestion est un exemple. Microsoft fournira des mises à jour importantes pour la qualité et la sécurité. Le [code source est disponible sur GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Les clients et les partenaires sont encouragés à personnaliser l’outil pour l’adapter à leurs besoins métier.

Les navigateurs suivants sont compatibles avec l’outil de gestion :
- Google Chrome version 68 ou ultérieure
- Microsoft Edge version 40.15063 ou ultérieure
- Mozilla Firefox version 52.0 ou ultérieure
- Safari version 10 ou ultérieure (macOS uniquement)

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Exécution du modèle Azure Resource Manager

Avant de déployer le modèle Azure Resource Manager, un utilisateur Azure Active Directory doit déployer l’interface utilisateur de gestion. Cet utilisateur doit remplir les conditions suivantes :

- Azure Multi-Factor Authentication (MFA) doit être désactivé.
- Il doit avoir l’autorisation de créer des ressources dans votre abonnement Azure.
- Il doit avoir l’autorisation de créer une application Azure AD. Suivez ces étapes pour vérifier si votre utilisateur a les [autorisations nécessaires](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Après avoir déployé le modèle Azure Resource Manager, vous pouvez lancer l’interface utilisateur de gestion pour la vérifier. Cet utilisateur doit remplir les conditions suivantes :
- Avoir une attribution de rôle pour afficher ou modifier votre locataire Windows Virtual Desktop

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Exécuter le modèle Azure Resource Manager pour provisionner l’interface utilisateur de gestion

Avant de commencer, vérifiez que les applications serveur et client ont le consentement nécessaire en vous rendant sur la [page de consentement de Windows Virtual Desktop](https://rdweb.wvd.microsoft.com) pour l’annuaire Azure Active Directory (AAD) représenté.

Suivez ces instructions pour déployer le modèle Azure Resource Management :

1. Accédez à la [page Azure RDS-Templates de GitHub ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Déployez le modèle sur Azure.
    - Si vous déployez sur un abonnement Entreprise, faites défiler vers le bas et sélectionnez **Déployer sur Azure**. Consultez [Conseils pour les paramètres des modèles](#guidance-for-template-parameters).
    - Si vous déployez sur un abonnement de fournisseur de solutions cloud, suivez ces instructions pour déployer sur Azure :
        1. Faites défiler vers le bas et cliquez sur **Déployer sur Azure**, puis sélectionnez **Copier l’adresse du lien**.
        2. Ouvrez un éditeur de texte tel que le bloc-notes et collez le lien.
        3. Juste après <https://portal.azure.com/> et avant le dièse (#), entrez un arobase (@) suivi du nom de domaine du locataire. Voici un exemple du format : <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Connectez-vous au portail Azure en tant qu’utilisateur avec des autorisations d’administrateur/contributeur pour l’abonnement du fournisseur de solutions cloud.
        5. Collez le lien que vous avez copié dans l’éditeur de texte dans la barre d’adresse.

### <a name="guidance-for-template-parameters"></a>Conseils pour les paramètres des modèles
Voici comment entrer des paramètres pour la configuration de l’outil :

- Pour le paramètre **isServicePrincipal**, sélectionnez **false**.
- Pour les informations d’identification, entrez vos informations d’identification Azure Active Directory avec l’authentification multifacteur désactivée. Ces informations d’identification sont celles que vous utilisez pour vous connecter à Azure, et pour créer l’application Azure AD et les ressources de l’application web Azure. Pour plus d’informations, consultez [Conditions nécessaires pour exécuter le modèle Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Pour **applicationName**, utilisez un nom unique pour l’application qui sera inscrite dans votre annuaire Azure Active Directory. Ce nom sera également utilisé pour l’URL de l’application web. Par exemple, vous pouvez utiliser un nom comme « Apr3UX ».

## <a name="provide-consent-for-the-management-tool"></a>Donner son consentement pour l’outil de gestion

Après avoir exécuté le modèle Azure Resource Manager de GitHub, vous trouverez un groupe de ressources contenant deux services d’application ainsi qu’un plan App Service dans le portail Azure.

Avant de vous connecter et d’utiliser l’outil de gestion, vous devez donner un consentement pour la nouvelle application Azure Active Directory qui est associée à l’outil de gestion. En fournissant ce consentement, vous autorisez l’outil de gestion à effectuer des appels de gestion de Windows Virtual Desktop pour le compte de l’utilisateur qui est connecté à l’outil.

![Capture d’écran montrant les autorisations fournies quand vous donnez un consentement à l’outil de gestion de l’interface utilisateur.](media/management-ui-delegated-permissions.png)

Pour déterminer quel utilisateur vous pouvez utiliser pour vous connecter à l’outil, accédez à votre [page des paramètres utilisateur d’Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) et prenez note de la valeur de **Les utilisateurs peuvent autoriser les applications à accéder aux données de l’entreprise en leur nom**.

![Capture d’écran montrant si les utilisateurs peuvent accorder leur consentement à des applications seulement pour leur utilisateur.](media/management-ui-user-consent-allowed.png)

- Si la valeur est définie sur **Oui**, vous pouvez vous connecter avec n’importe quel compte d’utilisateur de l’annuaire Azure Active Directory et donner un consentement seulement pour cet utilisateur. Cependant, si vous vous connectez ultérieurement à l’outil de gestion avec un autre compte d’utilisateur, vous devez donner à nouveau le même consentement.
- Si la valeur est définie sur **Non**, vous devez vous connecter en tant qu’administrateur général dans l’annuaire Azure Active Directory et donner le consentement de l’administrateur pour tous les utilisateurs de l’annuaire. Aucun autre utilisateur ne recevra une invite de consentement.


Une fois que vous décidez quel utilisateur vous allez utiliser pour donner le consentement, suivez ces instructions pour donner le consentement à l’outil :

1. Accédez à vos ressources Azure, sélectionnez la ressource Azure App Services avec le nom fourni dans le modèle (par exemple Apr3UX), puis accédez à l’URL associée, par exemple <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Connectez-vous en utilisant le compte d’utilisateur Azure Active Directory approprié.
3. Si vous vous êtes authentifié avec un administrateur général, vous pouvez maintenant cochez la case **Consentement pour le compte de votre organisation**. Sélectionnez **Accepter** pour donner votre consentement.
   
   ![Capture d’écran montrant la page de consentement complète que l’utilisateur ou l’administrateur voit.](media/management-ui-consent-page.png)

Vous accédez maintenant à l’outil de gestion.

## <a name="use-the-management-tool"></a>Utiliser l’outil de gestion

Après avoir donné un consentement pour l’organisation ou pour un utilisateur spécifié, vous pouvez accéder à tout moment à l’outil de gestion.

Suivez ces instructions pour lancer l’outil :

1. Sélectionnez la ressource Azure App Services avec le nom fourni dans le modèle (par exemple Apr3UX), puis accédez à l’URL associée, par exemple <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Connectez-vous avec vos informations d’identification Windows Virtual Desktop.
3. Quand vous êtes invité à choisir un groupe de locataires, sélectionnez **Groupe de locataires par défaut** dans la liste déroulante.
4. Lorsque vous sélectionnez Groupe de locataires par défaut, un menu doit s’afficher du côté droit de votre fenêtre. Dans ce menu, recherchez le nom de votre groupe de locataires et sélectionnez-le.

> [!NOTE]
> Si vous avez un groupe de locataires personnalisé, entrez le nom manuellement au lieu de choisir dans la liste déroulante.

## <a name="report-issues"></a>Signaler des problèmes

Si vous rencontrez des problèmes avec l’outil de gestion ou d’autres outils Windows Virtual Desktop, suivez les instructions dans [Modèles ARM pour les services Bureau à distance](https://github.com/Azure/RDS-Templates/blob/master/README.md) pour les signaler sur GitHub.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment déployer et vous connecter à l’outil de gestion, vous pouvez découvrir comment utiliser Azure Service Health pour superviser les problèmes des services et les alertes d’intégrité.

> [!div class="nextstepaction"]
> [Tutoriel sur la configuration des alertes des services](./set-up-service-alerts.md)
