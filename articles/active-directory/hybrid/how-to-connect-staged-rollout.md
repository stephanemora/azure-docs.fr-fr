---
title: 'Azure AD Connect : Authentification cloud – Lancement intermédiaire | Microsoft Docs'
description: Explique comment migrer de l’authentification fédérée à l’authentification cloud à l’aide d’un lancement intermédiaire.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847231"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Authentification cloud : Lancement intermédiaire (Préversion publique)

Cette fonctionnalité vous permet de migrer de l’authentification fédérée à l’authentification cloud à l’aide d’une approche intermédiaire.

Abandonner l’authentification fédérée a des implications. Par exemple, si vous présentez l’une des caractéristiques suivantes :
    
-  un serveur MFA local 
-  vous utilisez des cartes à puce pour l’authentification 
-  autres fonctionnalités propres à la fédération

Ces fonctionnalités doivent être prises en compte avant de passer à l’authentification cloud.  Avant d’essayer cette fonctionnalité, nous vous suggérons de consulter notre guide sur le choix de la méthode d’authentification appropriée. Pour plus d’informations, consultez [ce tableau](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prérequis

-   Vous disposez d’un locataire Azure AD avec des domaines fédérés.

-   Vous avez décidé de passer à la synchronisation de hachage de mot de passe + authentification unique transparente **(option A)** ou à l’authentification directe + authentification unique transparente **(option B)** . Bien que l’authentification unique transparente soit facultative, nous vous recommandons de l’activer pour obtenir une expérience de connexion silencieuse pour les utilisateurs qui utilisent des ordinateurs joints au domaine à partir du réseau d’entreprise.

-   Vous avez configuré toutes les stratégies appropriées d’accès conditionnel et de marque de locataire dont vous avez besoin pour les utilisateurs migrés vers l’authentification cloud.

-   Si vous envisagez d’utiliser Azure Multi-Factor Authentication, nous vous recommandons d’utiliser une [inscription convergée pour la réinitialisation de mot de passe en libre-service (SSPR) et Azure MFA](../authentication/concept-registration-mfa-sspr-combined.md) pour permettre à vos utilisateurs d’inscrire leurs méthodes d’authentification une seule fois.

-   Pour utiliser cette fonctionnalité, vous devez être l’administrateur général de votre locataire.

-   Pour activer l’authentification unique transparente sur une forêt AD spécifique, vous devez être l’administrateur de domaine.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Ces scénarios sont pris en charge pour le lancement intermédiaire :

- Cette fonctionnalité fonctionne uniquement pour les utilisateurs configurés pour Azure AD à l’aide d’Azure AD Connect et ne s’applique pas aux utilisateurs cloud uniquement.

- Cette fonctionnalité fonctionne uniquement pour le trafic de connexion utilisateur sur les navigateurs et les clients d’authentification modernes. Les applications ou les services cloud utilisant l’authentification héritée sont redirigés vers des flux d’authentification fédérés. (Exemple : Exchange Online avec l’authentification moderne désactivée, ou Outlook 2010, qui ne prend pas en charge l’authentification moderne.)

## <a name="unsupported-scenarios"></a>Scénarios non pris en charge

Ces scénarios ne sont pas pris en charge pour le lancement intermédiaire :

- Certaines applications envoient le paramètre de requête « domain\_hint » à Azure AD pendant l’authentification. Ces flux se poursuivront et les utilisateurs activés pour le lancement intermédiaire continueront d’utiliser la fédération pour l’authentification.

<!-- -->

- L’administrateur peut déployer l’authentification cloud à l’aide de groupes de sécurité. (Les groupes de sécurité cloud sont recommandés pour éviter la latence de synchronisation lors de l’utilisation de groupes de sécurité AD locaux.)

    - Vous pouvez utiliser un **maximum de 10 groupes par fonctionnalité** ; par exemple, pour chaque synchronisation de hachage de mot de passe / authentification directe / authentification unique transparente.

    - Les groupes imbriqués **ne sont pas pris en charge**. La Préversion publique a la même étendue.

    - Les groupes dynamiques **ne sont pas pris en charge** pour le lancement intermédiaire.

    - Les objets contact dans le groupe bloquent l’ajout du formulaire de groupe.

- Le basculement final de l’authentification fédérée à l’authentification cloud doit encore se produire à l’aide d’Azure AD Connect ou de PowerShell. Cette fonctionnalité ne fait pas basculer les domaines d’un état fédéré à managé.

- Lorsque vous ajoutez pour la première fois un groupe de sécurité pour le lancement intermédiaire, il est limité à 200 utilisateurs pour éviter que l’expérience utilisateur n’expire. Une fois le groupe ajouté dans l’expérience utilisateur, vous pouvez ajouter d’autres utilisateurs directement au groupe, le cas échéant.

## <a name="get-started-with-staged-rollout"></a>Prise en main du lancement intermédiaire

Si vous souhaitez tester la connexion de synchronisation de hachage de mot de passe (PHS) à l’aide du lancement intermédiaire, effectuez le travail préalable ci-dessous pour activer le lancement intermédiaire de la synchronisation de hachage de mot de passe.

Pour plus d’informations sur les applets de commande PowerShell utilisées, consultez [Préversion d’Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>Travail préalable pour la synchronisation de hachage de mot de passe

1. Activez la synchronisation de hachage de mot de passe à partir de la page  [Fonctionnalités facultatives](how-to-connect-install-custom.md#optional-features) dans Azure AD Connect. 

   ![Capture d’écran de la page Fonctionnalités facultatives d’Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Assurez-vous qu’un cycle complet de synchronisation de hachage de mot de passe a été exécuté pour que tous les hachages de mot de passe des utilisateurs aient été synchronisés avec Azure AD. Vous pouvez utiliser les diagnostics PowerShell [ici](tshoot-connect-password-hash-synchronization.md) pour vérifier l’état de la synchronisation de hachage de mot de passe.

   ![Capture d’écran du journal de résolution des problèmes AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Si vous souhaitez tester la connexion de l’authentification directe (PTA) à l’aide du lancement intermédiaire, effectuez le travail préalable ci-dessous pour activer la PTA pour le lancement intermédiaire.

## <a name="pre-work-for-pass-through-authentication"></a>Travail préalable pour l’authentification directe

1. Identifiez un serveur exécutant Windows Server 2012 R2 ou une version ultérieure sur lequel vous souhaitez que l’agent d’authentification directe s’exécute (**NE choisissez PAS le serveur Azure AD Connect**). Assurez-vous que le serveur est joint à un domaine, qu’il peut authentifier les utilisateurs sélectionnés à l’aide d’Active Directory et qu’il peut communiquer avec Azure AD sur les ports de sortie ou les URL (voir les [prérequis](how-to-connect-sso-quick-start.md) détaillés).

1. [Téléchargez](https://aka.ms/getauthagent) et installez l’agent d’authentification Microsoft Azure AD Connect sur le serveur. 

1. Pour activer la  [haute disponibilité](how-to-connect-sso-quick-start.md), installez des agents d’authentification supplémentaires sur d’autres serveurs.

1. Vérifiez que vous avez configuré vos [paramètres de verrouillage intelligent](../authentication/howto-password-smart-lockout.md) de manière appropriée. Cela permet de vous assurer que les comptes Active Directory locaux de vos utilisateurs ne sont pas verrouillés par les mauvais intervenants.

Nous vous recommandons d’activer l’authentification unique transparente, quelle que soit la méthode de connexion (PHS ou PTA) que vous sélectionnez pour le lancement intermédiaire. Veuillez effectuer le travail préalable ci-dessous pour activer l’authentification unique transparente pour le lancement intermédiaire.

## <a name="pre-work-for-seamless-sso"></a>Travail préalable pour l’authentification unique transparente

Activez l’authentification unique transparente sur les forêts AD à l’aide de PowerShell. Si vous avez plusieurs forêts AD, activez-la de manière individuelle pour chaque forêt. L’authentification unique transparente est déclenchée uniquement pour les utilisateurs sélectionnés pour le lancement intermédiaire et n’a aucun impact sur votre configuration de fédération actuelle.

**Résumé des étapes**

1. Tout d’abord, connectez-vous au serveur Azure AD Connect.

2. Accédez au dossier %programfiles%\\Microsoft Azure Active Directory Connect.

3. Importez le module PowerShell Authentification unique transparente à l’aide de la commande suivante :  `Import-Module .\AzureADSSO.psd1`.

4. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez  `New-AzureADSSOAuthenticationContext`. Cette commande doit afficher une boîte de dialogue dans laquelle vous pouvez entrer les informations d’identification d’administrateur général de votre locataire.

5. Appelez  `Get-AzureADSSOStatus | ConvertFrom-Json`. Cette commande vous fournit la liste des forêts AD (examinez la liste \"Domaines\") dans lesquelles cette fonctionnalité a été activée. Par défaut, elle est définie sur False au niveau du locataire.

   > **Exemple :** 
   > ![exemple de la sortie Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Appelez  `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt AD souhaitée.

7. Appelez `Enable-AzureADSSOForest -OnPremCredentials $creds`. Cette commande crée le compte d’ordinateur AZUREADSSOACC à partir du contrôleur de domaine local pour cette forêt Azure Directory spécifique requise pour l’authentification unique transparente.

8. L’authentification unique transparente requiert que les URL soient dans la zone intranet. Reportez-vous au guide de [démarrage rapide de l’authentification unique transparente](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) pour déployer ces URL à l’aide des stratégies de groupe.

9.  Pour obtenir une procédure pas à pas complète, vous pouvez également télécharger nos [plans de déploiement](https://aka.ms/SeamlessSSODPDownload) pour une authentification unique transparente.

## <a name="enable-staged-rollout"></a>Activer le lancement intermédiaire

Utilisez les étapes suivantes pour déployer une fonctionnalité spécifique (authentification directe / synchronisation de hachage de mot de passe / authentification unique transparente) à un certain ensemble d’utilisateurs dans un groupe :

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Activer le lancement intermédiaire d’une fonctionnalité spécifique sur votre locataire

Vous pouvez déployer l’une de ces options :

-   Synchronisation de hachage de mot de passe + authentification unique transparente **(option A)**

-   Authentification directe + authentification unique transparente **(option B)**

-   Synchronisation de hachage de mot de passe + authentification directe + authentification unique transparente **-\>** ***Non prise en charge***

Procédez comme suit :

1. Connectez-vous au portail Azure AD à l’aide de l’URL ci-dessous pour accéder à la préversion de l’expérience utilisateur.

   > <https://aka.ms/stagedrolloutux>

2. Cliquez sur Activer le lancement intermédiaire pour la connexion utilisateur managée (préversion).

   *Par exemple :* (**OPTION B**) Si vous souhaitez activer la synchronisation de hachage de mot de passe et l’authentification unique transparente, veuillez basculer ces fonctionnalités **« Activé »** , comme indiqué ci-dessous.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Ajoutez les groupes respectifs à la fonctionnalité pour activer l’authentification directe et l’authentification unique transparente. Assurez-vous que les groupes de sécurité n’ont pas plus de 200 membres initialement pour éviter l’expiration de l’expérience utilisateur.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Les membres d’un groupe sont automatiquement activés pour le lancement intermédiaire. Les groupes dynamiques et imbriqués ne sont pas pris en charge pour le lancement intermédiaire.

## <a name="auditing"></a>Audit

Nous avons activé les événements d’audit pour les différentes actions que nous effectuons pour le lancement intermédiaire.

- Vérifiez l’événement lorsque vous activez le lancement intermédiaire pour la synchronisation de hachage de mot de passe / l’authentification directe / l’authentification unique transparente.

  >[!NOTE]
  >Vérifiez l’événement qui est consigné lorsque SeamlessSSO est **activé** à l’aide de StagedRollout.

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Vérifiez l’événement lorsqu’un groupe est ajouté à la synchronisation de hachage de mot de passe / l’authentification directe / l’authentification unique transparente.

  >[!NOTE]
  >Vérifiez l’événement consigné lorsqu’un groupe est ajouté à la synchronisation de hachage de mot de passe pour le lancement intermédiaire.

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Vérifiez l’événement lors de l’activation d’un utilisateur ajouté au groupe pour le lancement intermédiaire.

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validation

Pour tester la connexion avec la synchronisation de hachage de mot de passe ou l’authentification directe (connexion par nom d’utilisateur/mot de passe) :

1. Accédez à <https://myapps.microsoft.com> dans une session de navigation privée à partir de l’extranet et entrez le UserPrincipalName (UPN, nom d’utilisateur principal) du compte d’utilisateur sélectionné pour le lancement intermédiaire.

1. Si l’utilisateur a été ciblé pour un lancement intermédiaire, l’utilisateur ne sera pas redirigé vers votre page de connexion fédérée, mais invité à se connecter à la page de connexion Azure AD, à la marque du locataire.

1. Vérifiez que la connexion s’affiche bien dans le [rapport d’activité de connexion Azure AD](../reports-monitoring/concept-sign-ins.md) en filtrant par UserPrincipalName.

Pour tester la connexion avec l’authentification unique transparente :

1. Accédez à <https://myapps.microsoft.com>/ dans une session de navigation privée à partir de l’intranet et entrez le UserPrincipalName (UPN, nom d’utilisateur principal) du compte d’utilisateur sélectionné pour le lancement intermédiaire.

1. Si l’utilisateur a été ciblé pour le lancement intermédiaire de l’authentification unique transparente, l’utilisateur verra un message « Tentative de connexion… » avant la connexion silencieuse.

1. Vérifiez que la connexion s’affiche bien dans le [rapport d’activité de connexion Azure AD](../reports-monitoring/concept-sign-ins.md) en filtrant par UserPrincipalName.

Pour vérifier que les connexions utilisateur se produisent toujours sur les fournisseurs de fédération :

Voici comment vous pouvez suivre les connexions utilisateur qui se produisent toujours sur AD FS pour les utilisateurs de lancement intermédiaire sélectionnés en à l’aide de [ces instructions](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consultez la documentation du fournisseur pour savoir comment le vérifier sur les fournisseurs de fédération tiers.

## <a name="roll-back"></a>Annuler l’opération

### <a name="remove-a-user-from-staged-rollout"></a>Supprimer un utilisateur du lancement intermédiaire

1.  La suppression de l’utilisateur du groupe désactive le lancement intermédiaire pour l’utilisateur.

2.  Si vous souhaitez désactiver la fonctionnalité de lancement intermédiaire, veuillez rebasculer la fonctionnalité sur l’état **« Désactivé »** pour désactiver le la lancement intermédiaire.


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

-   **Q : Un client peut-il utiliser cette fonctionnalité en production ?**

-   R : Oui, cette fonctionnalité peut être utilisée dans votre locataire de production, mais nous vous recommandons d’essayer d’abord cette fonctionnalité dans votre locataire de test.

-   **Q : Cette fonctionnalité peut-elle être utilisée pour maintenir une « coexistence » permanente, où certains utilisateurs utilisent l’authentification fédérée, et d’autres l’authentification cloud ?**

-   R : Non, cette fonctionnalité est conçue pour migrer progressivement de l’authentification fédérée à l’authentification cloud, pour finalement n’avoir recours qu’à cette dernière. Nous déconseillons un état mixte permanent, car cela peut entraîner des flux d’authentification inattendus.

-   **Q : Puis-je utiliser PowerShell pour effectuer un lancement intermédiaire ?**

-   R : Oui, vous trouverez la documentation relative à l’utilisation de PowerShell pour effectuer un lancement intermédiaire [ici](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Étapes suivantes
- [Préversion d’Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
