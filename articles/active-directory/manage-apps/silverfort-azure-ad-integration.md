---
title: Sécuriser un accès hybride avec Azure AD et Silverfort
description: Ce tutoriel explique comment intégrer Silverfort à Azure AD pour un accès hybride sécurisé
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/13/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce68bde35f817d17f1278f6826ffe38284bf6c1a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700679"
---
# <a name="tutorial-configure-silverfort-with-azure-active-directory-for-secure-hybrid-access"></a>Tutoriel : Configurer Silverfort avec Azure Active Directory pour un accès hybride sécurisé

Dans ce tutoriel, vous allez apprendre à intégrer Silverfort à Azure Active Directory (Azure AD). [Silverfort](https://www.silverfort.com/) utilise une technologie novatrice sans agent et sans proxy pour connecter toutes vos ressources locales et dans le cloud à Azure AD. Cette solution permet aux organisations d’appliquer la protection des identités, la visibilité et l’expérience utilisateur dans tous les environnements dans Azure AD. Elle rend possible la supervision et l’évaluation basées sur les risques universels de l’activité d’authentification pour les environnements locaux et cloud, et empêche les menaces de manière proactive.  

Silverfort peut connecter sans interruption n’importe quel type de ressource dans Azure AD, comme s’il s’agissait d’une application web moderne. Par exemple :

- Applications héritées et personnalisées

- Bureau à distance et Secure Shell (SSH)

- Outils en ligne de commande et autre accès administrateur

- Partages de fichiers et bases de données

- Infrastructures et systèmes industriels

Ces ressources reliées **par un pont** apparaissent comme des applications normales dans Azure AD et peuvent être protégées avec l’accès conditionnel, l’authentification unique (SSO), l’authentification multifacteur, l’audit et plus encore.

Cette solution combine toutes les ressources d’entreprise et les plateformes de gestion des identités et des accès (IAM) tierces. Par exemple, Active Directory, les services de fédération Active Directory (AD FS) et le protocole RADIUS (Remote Authentication Dial-In User Service) sur Azure AD, ce qui inclut les environnements hybrides et multiclouds.

## <a name="scenario-description"></a>Description du scénario

Dans ce guide, vous allez configurer et tester le pont Azure AD Silverfort dans votre locataire Azure AD.

Après l’avoir configuré, vous pouvez créer des stratégies d’authentification Silverfort qui relient les demandes d’authentification de différentes sources d’identité vers Azure AD pour l’authentification unique. Une fois qu’une application est reliée par un pont, elle peut être gérée dans Azure AD.

Le diagramme suivant montre les composants inclus dans la solution et la séquence d’authentification orchestrée par Silverfort.

![l’image montre le diagramme d’architecture](./media/silverfort-azure-ad-integration/silverfort-architecture-diagram.png)

| Étape | Description|
|:---------|:------------|
| 1. | L’utilisateur envoie la demande d’authentification au fournisseur d’identité (IdP) d’origine via des protocoles tels que Kerberos, SAML, NTLM, OIDC et LDAP.|
| 2. | La réponse est routée en l’état vers Silverfort pour validation afin de vérifier l’état de l’authentification.|
| 3. | Silverfort fournit la visibilité, la découverte et le pontage vers Azure AD.|
| 4. | Si l’application est configurée comme étant **reliée par un pont**, la décision d’authentification est passée à Azure AD. Azure AD évalue les stratégies d’accès conditionnel et valide l’authentification.|
| 5. | La réponse de l’état d’authentification est ensuite libérée et envoyée en l’état au fournisseurs d’identité (IdP) par Silverfort. |
| 6.| Le fournisseurs d’identité autorise ou refuse l’accès à la ressource.|
| 7. | L’utilisateur est averti de l’acception ou du refus de la demande d’accès. |

## <a name="prerequisites"></a>Prérequis

Pour configurer l’authentification unique d’une application que vous avez ajoutée à votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- L’un des rôles suivants dans votre compte Azure : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou Propriétaire du principal de service.

- Une application prenant en charge l’authentification unique et qui a déjà été préconfigurée et ajoutée à la galerie Azure AD. L’application Silverfort dans la Galerie Azure AD est déjà préconfigurée. Vous devez l’ajouter en tant qu’application d’entreprise à partir de la galerie.

## <a name="onboard-with-silverfort"></a>Intégration avec Silverfort

Pour déployer Silverfort dans votre locataire ou infrastructure, [contactez Silverfort](https://www.silverfort.com/). Installez l’application de bureau Silverfort sur les stations de travail appropriées.

## <a name="configure-silverfort-and-create-a-policy"></a>Configurer Silverfort et créer une stratégie

1. Dans un navigateur, connectez-vous à la **console d’administration Silverfort**.

2. Dans le menu principal, accédez à **Settings** (Paramètres), puis faites défiler l’écran jusqu’à **Azure AD Bridge Connector** (Connecteur de pont Azure AD) dans la section General. Confirmez votre ID de locataire, puis sélectionnez **Authorize**.

   ![Image montrant le Connecteur de pont Azure AD](./media/silverfort-azure-ad-integration/azure-ad-bridge-connector.png)

   ![Image montrant la confirmation de l’inscription](./media/silverfort-azure-ad-integration/grant-permission.png)

3. Une confirmation de l’inscription s’affiche sous un nouvel onglet. Fermez cet onglet.

   ![Image montrant l’inscription terminée](./media/silverfort-azure-ad-integration/registration-completed.png)

4. Dans la page **Settings** (Paramètres), cliquez sur **Save changes** (Enregistrer les changements).

   ![Image montrant l’adaptateur Azure AD](./media/silverfort-azure-ad-integration/silverfort-azure-ad-adapter.png)

    Connectez-vous à votre console Azure AD. Vous voyez alors l’application **Adaptateur Azure AD Silverfort** inscrite en tant qu’application d’entreprise.

   ![Image montrant l’application d’entreprise](./media/silverfort-azure-ad-integration/enterprise-application.png)

5. Dans la console d’administration Silverfort, accédez à la page **Policies** (Stratégies), puis sélectionnez **Create Policy** (Créer une stratégie).

6. La boîte de dialogue **New Policy** (Nouvelle stratégie) s’affiche. Entrez un nom de stratégie (**Policy Name**), qui indique le nom de l’application qui sera créée dans Azure. Par exemple, si vous ajoutez plusieurs serveurs ou applications dans le cadre de cette stratégie, nommez-la de façon à refléter les ressources couvertes par la stratégie. Dans l’exemple, nous allons créer une stratégie pour le serveur *SL-APP1*.

   ![Image montrant la stratégie définie](./media/silverfort-azure-ad-integration/define-policy.png)

7. Sélectionnez le type d’**authentification** et le **protocole** appropriés.

8. Dans le champ **User and Groups** (Utilisateur et groupes), sélectionnez l’icône de modification pour configurer les utilisateurs qui seront affectés par la stratégie. L’authentification de ces utilisateurs est reliée par un pont à Azure AD.

   ![Image montrant le champ User and Groups](./media/silverfort-azure-ad-integration/user-groups.png)

9. Recherchez et sélectionnez des utilisateurs, des groupes ou des unités d’organisation.

   ![Image montrant la recherche d’utilisateurs](./media/silverfort-azure-ad-integration/search-users.png)

   Les utilisateurs sélectionnés sont listés dans la zone SELECTED (SÉLECTIONNÉ).

   ![Image montrant l’utilisateur sélectionné](./media/silverfort-azure-ad-integration/select-user.png)

10. Sélectionnez la **Source** pour laquelle la stratégie s’appliquera. Dans cet exemple, tous les appareils (*All Devices*) sont sélectionnés.

    ![Image montrant la source](./media/silverfort-azure-ad-integration/source.png)

11. Définissez la **Destination** sur *SL-App1*. Vous pouvez sélectionner le bouton de modification pour changer ou ajouter d’autres ressources ou groupes de ressources (facultatif).

    ![Image montrant la destination](./media/silverfort-azure-ad-integration/destination.png)

12. Sélectionnez l’Action sur **AZURE AD BRIDGE** (PONT AZURE AD).

    ![Image montrant l’enregistrement du pont Azure AD](./media/silverfort-azure-ad-integration/save-azure-ad-bridge.png)

13. Sélectionnez **SAVE** (Enregistrer) pour enregistrer la nouvelle stratégie. Vous êtes alors invité à l’activer.

    ![Image montrant l’état de modification](./media/silverfort-azure-ad-integration/change-status.png)

    La stratégie s’affiche dans la page des stratégies, dans la section Azure AD Bridge (Pont Azure AD) :

    ![Image montrant l’ajout de la stratégie](./media/silverfort-azure-ad-integration/add-policy.png)

14. Retournez dans la console Azure AD, puis accédez à **Applications d’entreprise**. La nouvelle application Silverfort doit maintenant s’afficher. Cette application peut maintenant être incluse dans les [stratégies d’autorité de certification](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?bc=/azure/active-directory/conditional-access/breadcrumb/toc.json&toc=/azure/active-directory/conditional-access/toc.json%23create-a-conditional-access-policy).

## <a name="next-steps"></a>Étapes suivantes

- [Adaptateur Azure AD Silverfort](https://azuremarketplace.microsoft.com/marketplace/apps/aad.silverfortazureadadapter?tab=overview)

- [Ressources Silverfort](https://www.silverfort.com/resources/)
