---
title: Tutoriel pour migrer des stratégies de connexion Okta vers l’accès conditionnel Azure Active Directory
titleSuffix: Active Directory
description: Découvrez comment migrer des stratégies de connexion Okta vers l’accès conditionnel Azure Active Directory
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: c36ad9b56ce49234d2ee28f53073267944152db0
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080432"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>Tutoriel : Migrer des stratégies de connexion Okta vers l’accès conditionnel Azure Active Directory

Dans ce tutoriel, découvrez comment les organisations peuvent migrer des stratégies d’authentification globale ou de connexion au niveau de l’application dans Okta vers des stratégies d’accès conditionnel (CA) Azure Active Directory (AD) pour sécuriser l’accès des utilisateurs Azure AD et les applications connectées.

Ce tutoriel part du principe que vous disposez d’un locataire Office 365 fédéré à Okta pour l’authentification et l’authentification multifacteur (MFA). Vous devez également disposer des agents d’approvisionnement du serveur Azure AD Connect ou du cloud Azure AD Connect configurés pour l’approvisionnement des utilisateurs sur Azure AD.

## <a name="prerequisites"></a>Prérequis

Lors du passage d’une authentification Okta à une autorité de certification Azure AD, il est important de comprendre les exigences en matière de licences. L’autorité de certification Azure AD exige que les utilisateurs disposent d’une licence Azure AD Premium P1 attribuée avant l’inscription pour l’authentification multifacteur Azure AD.

Avant d’effectuer les étapes de jointure hybride Azure AD, vous devez disposer d’informations d’identification d’administrateur d’entreprise dans la forêt locale pour configurer l’enregistrement de point de connexion de service (SCP).

## <a name="step-1---catalog-current-okta-sign-on-policies"></a>Étape 1 : Répertorier les stratégies d’authentification Okta actuelles

Pour effectuer une transition réussie vers l’autorité de certification, les stratégies d’authentification Okta existantes doivent être évaluées pour déterminer les cas d’utilisation et les exigences qui seront migrés vers Azure AD.

1. Vérifiez les stratégies de connexion globales en accédant à **Sécurité** et en sélectionnant **Authentification**, puis **Abonnement**.

   ![image présentant les stratégies d’authentification globale](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   Dans cet exemple, notre stratégie d’authentification globale applique l’authentification multifacteur à toutes les sessions en dehors de nos zones réseau configurées.

   ![image présentant les stratégies d’authentification globale avec authentification multifacteur](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. Ensuite, accédez à **Applications**, et vérifiez les stratégies d’authentification au niveau des applications. Sélectionnez **Applications** dans le sous-menu, puis votre instance connectée Office 365 dans la liste **Applications actives**.

3. Enfin, sélectionnez **Authentification** et faites défiler jusqu’au bas de la page.

Dans l’exemple suivant, notre stratégie d’authentification d’application Office 365 présente quatre règles distinctes.

- **Appliquer l’authentification multifacteur pour les sessions mobiles** - Exige l’authentification multifacteur pour chaque session d’authentification moderne ou de navigateur sur iOS ou Android.

- **Autoriser les appareils Windows de confiance** - Empêche les appareils Okta de confiance d’être sollicités pour une vérification ou des facteurs supplémentaires.

- **Exiger l’authentification multifacteur sur les appareils Windows non approuvés** - Exige l’authentification multifacteur pour chaque session d’authentification moderne ou de navigateur sur les appareils Windows non approuvés.

- **Bloquer l’authentification héritée** - Empêche tout client d’authentification hérité de se connecter au service.

  ![image présentant les règles d’authentification O365](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="step-2---configure-condition-pre-requisites"></a>Étape 2 - Configurer les prérequis de la condition

Les stratégies d’autorité de certification Azure AD peuvent être configurées pour correspondre aux conditions d’Okta pour la plupart des scénarios sans configuration supplémentaire.

Dans certains scénarios, vous pouvez avoir besoin d’une configuration supplémentaire avant de configurer les stratégies d’autorité de certification. Les deux scénarios connus au moment de la rédaction de cet article sont les suivants :

- **Les emplacements réseau Okta deviennent des emplacements nommés dans Azure AD** - Suivez [cet article](../conditional-access/location-condition.md#named-locations) pour configurer des emplacements nommés dans Azure AD.

- **Confiance des appareils Okta vers l’autorité de certification basée sur les appareils** - L’autorité de certification offre deux options possibles lors de l’évaluation de l’appareil d’un utilisateur.

  - [Jointure hybride Azure AD](#hybrid-azure-ad-join-configuration) - Fonctionnalité activée au sein du serveur Azure AD Connect qui synchronise les appareils Windows actuels, comme Windows 10, Server 2016 et 2019, avec Azure AD.

  - [Inscrivez l’appareil dans Microsoft Endpoint Manager](#configure-device-compliance) et attribuez une stratégie de conformité.

### <a name="hybrid-azure-ad-join-configuration"></a>Configuration de la jointure hybride Azure AD

L’activation de la jointure hybride Azure AD peut être effectuée sur votre serveur Azure AD Connect en exécutant l’assistant de configuration. Après la configuration, vous devez effectuer les étapes nécessaires pour inscrire automatiquement les appareils.

>[!NOTE]
>La jointure hybride Azure AD n’est pas prise en charge avec les agents d’approvisionnement Azure AD Connect cloud.

1. Suivez ces [instructions](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join) pour activer la jointure hybride Azure AD.

2. Sur la page de configuration SCP, sélectionnez le menu déroulant **Service d’authentification**. Choisissez l’URL de votre fournisseur de fédération Okta, puis cliquez sur **Ajouter**. Saisissez les informations d’identification de votre administrateur d’entreprise local, puis sélectionnez **Suivant**.

   ![image montrant la configuration SCP](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

3. Si vous avez bloqué l’authentification héritée sur les clients Windows dans la stratégie d’authentification globale ou au niveau de l’application, créez une règle pour permettre l’achèvement du processus de jointure hybride Azure AD.

4. Vous pouvez autoriser l’intégralité de la pile d’authentification héritée pour tous les clients Windows ou contacter le support Okta pour activer leur chaîne client personnalisée sur vos stratégies d’application existantes.

### <a name="configure-device-compliance"></a>Configurer la conformité des appareils

Bien que la jointure hybride Azure AD remplace directement la confiance des appareils Okta sur Windows, les stratégies d’autorité de certification peuvent également examiner la conformité des appareils pour les appareils qui ont été inscrits entièrement dans Microsoft Endpoint Manager.

- **Aperçu de la conformité** - Reportez-vous aux [stratégies de conformité des appareils dans Microsoft Intune](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows).

- **Conformité des appareils** - Créez des [stratégies dans Microsoft Intune](/mem/intune/protect/create-compliance-policy).

- **Inscription à Windows** - Si vous avez choisi de déployer la jointure hybride Azure AD, une stratégie de groupe supplémentaire peut être déployée pour achever le [processus d’inscription automatique de ces appareils dans Microsoft Intune](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy).

- **Inscription iOS/iPadOS** - Avant l’inscription d’un appareil iOS, [des configurations supplémentaires](/mem/intune/enrollment/ios-enroll) doivent être effectuées dans la console de gestion des points de terminaison.

- **Inscription Android** - Avant l’inscription d’un appareil Android, [des configurations supplémentaires](/mem/intune/enrollment/android-enroll) doivent être effectuées dans la console de gestion des points de terminaison.

## <a name="step-3---configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Étape 3 : Configurer les paramètres de locataire d’authentification multifacteur Azure AD

Avant de procéder à la conversion vers l’autorité de certification, vérifiez les paramètres de base du locataire d’authentification multifacteur Azure AD pour votre organisation.

1. Accédez au portail [Azure](https://portal.azure.com) et connectez-vous avec un compte d’administrateur global.

1. Sélectionnez **Azure Active Directory**, puis **Utilisateurs**, et enfin **Authentification multifacteur**, ce qui vous amènera au portail hérité de l’authentification multifacteur Azure.

   ![image montrant le portail de l’authentification multifacteur Azure AD héritée](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   À la place, vous pouvez utiliser **<https://aka.ms/mfaportal>** .

1. Dans le menu **Authentification multifacteur Azure héritée**, modifiez le menu d’état en surveillant **activée** et **appliquée** pour confirmer qu’aucun utilisateur n’est activé pour l’authentification multifacteur héritée. Si votre locataire a des utilisateurs dans les vues du dessous, vous devez les désactiver dans le menu hérité. Seules les stratégies d’autorité de certification prendront effet sur leur compte.

   ![l’image montre la désactivation de l’utilisateur dans le portail d’authentification multifacteur Azure AD héritée](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   Le champ **Appliquée** doit également être vide.

   ![image montrant que le champ Appliquée est vide dans le portail d’authentification multifacteur Azure AD héritée](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enforced-empty-legacy-azure-ad-portal.png)

1. Après avoir confirmé qu’aucun utilisateur n’est configuré pour l’authentification multifacteur héritée, sélectionnez l’option **Paramètres du service**. Changez la sélection **Mots de passe d’application** en **Ne pas autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur**.

1. Assurez-vous que les cases **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet** et **Permettre aux utilisateurs de mémoriser l’authentification multifacteur sur des appareils de confiance (entre 1 et 365 jours)** ne sont pas cochées, puis sélectionnez **Enregistrer**.

   >[!NOTE]
   >Consultez [Meilleures pratiques pour la configuration des paramètres d’invite de l’authentification multifacteur](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

   ![l’image montre les champs décochés dans le portail d’authentification multifacteur Azure AD héritée](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="step-4---configure-ca-policies"></a>Étape 4 : Configurer des stratégies d’autorité de certification

Vous avez configuré les composants requis et établi les paramètres de base pour créer la première stratégie de l’autorité de certification.

1. Pour configurer les stratégies d’autorité de certification dans Azure AD, accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Afficher** dans Gérer Azure Active Directory.

2. La configuration des stratégies d’autorité de certification doit tenir compte des [meilleures pratiques pour le déploiement et la conception de l’autorité de certification](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components).

3. Pour imiter la stratégie d’authentification multifacteur globale d’Okta, [créez une stratégie](../conditional-access/howto-conditional-access-policy-all-users-mfa.md).

4. Créer une [règle d’autorité de certification basée sur une approbation d’appareil](../conditional-access/require-managed-devices.md).

5. Dans ce tutoriel, cette stratégie peut être ciblée vers une application spécifique, un groupe de test d’utilisateurs ou les deux.

   ![image montrant l’utilisateur de test](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![image montrant la réussite de l’utilisateur de test](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

6. Après avoir configuré la stratégie basée sur l’emplacement et la stratégie d’approbation d’appareil, il est temps de configurer la stratégie [**Bloquer l’authentification héritée**](../conditional-access/howto-conditional-access-policy-block-legacy.md) équivalente.

Avec ces trois stratégies d’autorité de certification, les stratégies d’authentification Okta d’origine ont été répliquées dans Azure AD. Les étapes suivantes impliquent l’inscription des utilisateurs à l’authentification multifacteur Azure et au test des stratégies.

## <a name="step-5---enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Étape 5 : Inscrire les membres du pilote dans l’authentification multifacteur Azure AD

Une fois que les stratégies d’autorité de certification ont été configurées, les utilisateurs doivent s’inscrire aux méthodes d’authentification multifacteur Azure. Les utilisateurs peuvent être amenés à s’inscrire par le biais de différentes méthodes.

1. Pour l’enregistrement individuel, vous pouvez diriger les utilisateurs vers <https://aka.ms/mfasetup> pour saisir manuellement les informations d’enregistrement.

2. L’utilisateur peut aller sur <https://aka.ms/mysecurityinfo> pour entrer des informations ou gérer le formulaire d’enregistrement de l’authentification multifacteur.

Consultez [ce guide](../authentication/howto-registration-mfa-sspr-combined.md) pour bien comprendre la procédure d’inscription à l’authentification multifacteur.  

Naviguez vers <https://aka.ms/mfasetup> après vous être connecté avec l’authentification multifacteur Okta. Vous êtes invité à vous inscrire pour l’authentification multifacteur avec Azure AD.

>[!NOTE]
>Si l’inscription a déjà eu lieu dans le passé pour cet utilisateur, il sera dirigé vers la page d’informations **Ma sécurité** après avoir répondu à l’invite d’authentification multifacteur.

Voir la [documentation utilisateur final pour l’inscription à l’authentification multifacteur](../user-help/security-info-setup-signin.md).

## <a name="step-6---enable-ca-policies"></a>Étape 6 - Activer les stratégies d’autorité de certification

1. Pour déployer les tests, modifiez les stratégies créées dans les exemples précédents pour **Connexion d’utilisateur de test activée**. 

   ![image montrant l’activation de l’utilisateur de test](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

2. À la prochaine connexion à Office 365, l’utilisateur de test John Smith est invité à se connecter avec l’authentification multifacteur Okta et l’authentification multifacteur Azure AD.

   ![image montrant l’authentification via Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

3. Effectuez la vérification de l’authentification multifacteur par le biais d’Okta.

   ![image montrant la vérification de l’authentification multifacteur via Okta](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

4. Une fois que l’utilisateur a terminé l’invite d’authentification multifacteur Okta, il est invité à fournir une autorité de certification. Vérifiez que les stratégies ont été correctement configurées et qu’elles se trouvent dans les conditions pour être déclenchées pour l’authentification multifacteur.

   ![image montrant la vérification de l’authentification multifacteur via Okta pour l’autorité de certification](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="step-7---cutover-from-sign-on-to-ca-policies"></a>Étape 7 - Basculement de l’authentification sur les stratégies d’autorité de certification

Après avoir effectué un test approfondi sur les membres du pilote afin de vous assurer que l’autorité de certification s’applique comme prévu, les autres membres de l’organisation peuvent être ajoutés aux stratégies d’autorité de certification une fois l’inscription terminée.

Pour éviter les invites en double entre l’authentification multifacteur Azure et Okta, vous devez vous désabonner de l’authentification multifacteur Okta en modifiant les stratégies d’authentification.

La dernière étape de migration vers l’autorité de certification peut être effectuée de manière échelonnée ou par basculement.

1. Allez dans la console d’administration d’Okta, sélectionnez **Sécurité**, puis **Authentification**, et enfin **Stratégie d’authentification**.

>[!NOTE]
>Les stratégies globales doivent être rendues inactives uniquement si toutes les applications Okta sont protégées par leurs propres stratégies d’authentification.

2. Définissez la stratégie Appliquer l’authentification multifacteur sur **Inactive** ou affectez la stratégie à un nouveau groupe qui n’inclut pas nos utilisateurs Azure AD.

   ![image montrant la stratégie d’authentification multifacteur inactive](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

3. Sur la stratégie d’authentification au niveau de l’application, mettez à jour les stratégies pour qu’elles soient inactives en sélectionnant l’option **Désactiver la règle**. Vous pouvez également affecter la stratégie à un nouveau groupe qui n’inclut pas les utilisateurs Azure AD.

4. Assurez-vous qu’au moins une stratégie d’authentification au niveau de l’application est activée pour l’application qui autorise l’accès sans authentification multifacteur.

   ![image montrant l’accès à l’application sans authentification multifacteur](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

5. Après la désactivation des stratégies de connexion Okta ou l’exclusion des utilisateurs Azure AD migrés des groupes d’application, les utilisateurs doivent être invités **uniquement** par l’autorité de certification lors de leur prochaine connexion.

## <a name="next-steps"></a>Étapes suivantes

- [Migrer des applications d’Okta vers Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)

- [Migrer la fédération Okta vers Azure AD](migrate-okta-federation-to-azure-active-directory.md)

- [Migrer le provisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)
