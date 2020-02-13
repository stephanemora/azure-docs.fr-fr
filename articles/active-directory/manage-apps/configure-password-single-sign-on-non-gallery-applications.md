---
title: Comment configurer l’authentification unique par mot de passe pour des applications Azure AD | Microsoft Docs
description: Comment configurer l’authentification unique par mot de passe (SSO) sur vos applications d’entreprise Azure AD dans la plateforme d’identité Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063524"
---
# <a name="configure-password-single-sign-on"></a>Configurer l’authentification unique par mot de passe

Lorsque vous [ajoutez une application galerie](add-gallery-app.md) ou une [application web non galerie](add-non-gallery-app.md) à vos applications d'entreprise Azure AD, l'une des options de connexion unique disponibles est [authentification unique par mot de passe](what-is-single-sign-on.md#password-based-sso). Cette option est disponible pour tout site web doté d’une page de connexion HTML. L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile dans les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d’applications de médias sociaux de votre organisation. 

L’authentification unique par mot de passe (SSO) est un excellent moyen pour commencer à intégrer rapidement des applications dans Azure AD et vous permet d’effectuer les opérations suivantes :

-   activer **l’authentification unique pour vos utilisateurs** par le stockage et la relecture en toute sécurité des noms d’utilisateur et mots de passe pour l’application que vous avez intégrée à Azure AD ;

-   **prendre en charge les applications qui requièrent plusieurs champs de connexion** pour les applications qui requièrent plus que les champs de nom d’utilisateur et de mot de passe pour la connexion ;

-   **personnaliser les étiquettes** des champs d’entrée de nom d’utilisateur et de mot de passe que vos utilisateurs voient dans le [volet d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) lorsqu’ils entrent leurs informations d’identification ;

-   autoriser vos **utilisateurs** à fournir leurs propres noms d’utilisateur et mots de passe de tout compte d’application existant qu’ils saisissent manuellement dans le [volet d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ;

-   autoriser un **membre du groupe d’entreprise** à spécifier les noms d’utilisateur et mots de passe affectés à un utilisateur à l’aide de la fonctionnalité [Accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) ;

-   Autoriser un **administrateur** à spécifier un nom d'utilisateur et un mot de passe à utiliser par des individus ou des groupes lors de la connexion à l'application à l’aide de la fonctionnalité Mettre à jour les informations d'identification 

## <a name="before-you-begin"></a>Avant de commencer

Si l’application n’a pas été ajoutée à votre locataire Azure AD, consultez [Ajouter une application figurant dans la galerie](add-gallery-app.md) ou [Ajouter une application ne figurant pas dans la galerie](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Ouvrir l'application et sélectionner l’authentification unique par mot de passe

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’application cloud ou administrateur d’application pour votre locataire Azure AD.

2. Accédez à **Azure Active Directory** > **Applications d’entreprise**. Un échantillon aléatoire des applications dans votre locataire Azure AD s’affiche. 

3. Dans le menu **Type d’Application**, sélectionnez **Toutes les Applications**, puis sélectionnez **Appliquer**.

4. Entrez le nom de l’application dans la zone de recherche, puis sélectionnez l’application dans les résultats.

5. Sous la section **Gérer**, sélectionnez **Authentification unique**. 

6. Sélectionnez **Par mot de passe**.

7. Saisissez l'URL de la page de connexion web de l'application. Il doit s’agir de la page qui contient le champ d’entrée du nom d’utilisateur.

   ![Authentification unique par mot de passe](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Sélectionnez **Enregistrer**. Azure AD tente d’analyser la page de connexion à la recherche d’entrées de nom d’utilisateur et de mot de passe. Si la tentative réussit, vous avez terminé. 
 
> [!NOTE]
> La prochaine étape consiste à [affecter des utilisateurs et des groupes à l’application](methods-for-assigning-users-and-groups.md). Après avoir affecté des utilisateurs et des groupes, vous pouvez fournir des informations d'identification à utiliser pour le compte d'un utilisateur lorsqu'il se connecte à l'application. Sélectionnez **Utilisateurs et groupes**, cochez la case de la ligne de l'utilisateur ou du groupe, puis cliquez sur **Mettre à jour les informations d'identification**. Ensuite, entrez le nom d'utilisateur et le mot de passe à utiliser pour le compte de l'utilisateur ou du groupe. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.
 

## <a name="manual-configuration"></a>Configuration manuelle

Si la tentative d'analyse Azure AD échoue, vous pouvez configurer l’authentification manuellement.

1. Sous **\<Configuration nom de l’application>** , sélectionnez **Configurer les paramètres d’authentification unique par mot de passe \<nom de l’application>** pour afficher la page **Configuration l’authentification**. 

2. Sélectionnez **Détecter manuellement les champs de connexion**. Des instructions supplémentaires décrivant la détection manuelle des champs de connexion s’affichent.

   ![Configuration manuelle de l’authentification unique par mot de passe](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Sélectionnez **Capturer les champs de connexion**. Une page d’état de la capture s’ouvre dans un nouvel onglet affichant le message **Capture des métadonnées en cours**.

4. Si la mention **Extension Volet d’accès requise** s’affiche dans un nouvel onglet, sélectionnez **Installer maintenant** pour installer l’extension de navigateur **My Apps Secure Sign-in Extension**. (Cette extension de navigateur est compatible avec Microsoft Edge, Chrome ou Firefox.) Ensuite, installez, lancez et activez l’extension, puis actualisez la page d’état de la capture.

   L’extension de navigateur ouvre ensuite un nouvel onglet qui affiche l’URL entrée.
5. Dans l’onglet contenant l’URL saisie, suivez le processus de connexion. Renseignez les champs de nom d’utilisateur et de mot de passe et essayez de vous connecter. (Vous n’êtes pas obligé de fournir le mot de passe correct.)

   Un message vous invite à enregistrer les champs de connexion capturés.
6. Sélectionnez **OK**. L’extension de navigateur met à jour la page d’état de la capture avec le message **Les métadonnées ont été mises à jour pour l’application**. L'onglet du navigateur se ferme.

7. Dans la page Azure AD **Configurer l’authentification**, sélectionnez **J’ai pu me connecter à l’application**.

8. Sélectionnez **OK**.

Après la capture de la page de connexion, vous pouvez affecter des utilisateurs et des groupes, et vous pouvez configurer des stratégies d’informations d’identification comme pour les [applications avec authentification unique par mot de passe](what-is-single-sign-on.md) standard.

> [!NOTE]
> Vous pouvez télécharger un logo de la mosaïque pour l'application avec le bouton **Télécharger un logo** sur l'onglet **Configurer** de l'application.

## <a name="next-steps"></a>Étapes suivantes

- [Affecter des utilisateurs et des groupes à l’application](methods-for-assigning-users-and-groups.md)
- [Configurer le provisionnement automatique d’un compte utilisateur](../app-provisioning/configure-automatic-user-provisioning-portal.md)
