---
title: Considérations relatives au déploiement d'Azure AD Multi-Factor Authentication
description: Familiarisez-vous avec les considérations et la stratégie de déploiement pour une implémentation réussie d'Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 120e97e62980427fb83c6bf7884da92dd9c5e1f7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602317"
---
# <a name="plan-an-azure-active-directory-multi-factor-authentication-deployment"></a>Planifier un déploiement de l’authentification multifacteur Azure Active Directory 

L’authentification multifacteur Azure AD (Azure Active Directory) MFA (Multi-Factor Authentication) permet de protéger l’accès aux données et aux applications en fournissant une autre couche de sécurité via une deuxième forme d’authentification. Les organisations peuvent activer l’authentification multifacteur avec l’[accès conditionnel](../conditional-access/overview.md) pour adapter la solution à leurs besoins spécifiques.

Ce guide de déploiement vous montre comment planifier et implémenter un déploiement d’[Azure AD MFA](concept-mfa-howitworks.md).

## <a name="prerequisites-for-deploying-azure-ad-mfa"></a>Prérequis pour le déploiement d’Azure AD MFA

Avant de commencer le déploiement, vérifiez que vous répondez aux prérequis suivants pour les scénarios appropriés.

| Scénario | Configuration requise |
|----------|--------------|
|Environnement d’identité **cloud uniquement** avec authentification moderne | **Aucune tâche prérequise** |
|Scénarios d’**identité hybride** | Déployez [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) et synchronisez les identités des utilisateurs entre les services AD DS (Active Directory Domain Services) locaux et Azure AD. |
| **Applications héritées locales** publiées pour l’accès au cloud| Déployer le [proxy d’application Azure AD](../app-proxy/application-proxy-deployment-plan.md) |

## <a name="choose-authentication-methods-for-mfa"></a>Choisir les méthodes d’authentification pour l’authentification MFA

De nombreuses méthodes peuvent être utilisées pour une authentification à deux facteurs. Vous pouvez effectuer votre choix parmi la liste des méthodes d’authentification disponibles, en évaluant chacune d’elles sur le plan de la sécurité, de la facilité d’utilisation et de la disponibilité.

>[!IMPORTANT]
>Activez plusieurs méthodes MFA pour que les utilisateurs disposent d’une méthode de secours au cas où leur méthode principale ne serait pas disponible. Méthodes incluses :

- [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-overview)
- [Application Microsoft Authenticator](concept-authentication-authenticator-app.md)
- [Clés de sécurité FIDO2 (préversion)](concept-authentication-passwordless.md#fido2-security-keys)
- [Jetons matériels OATH (version préliminaire)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
- [Jetons logiciels OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
- [Vérification par SMS](concept-authentication-phone-options.md#mobile-phone-verification)
- [Vérification par appel téléphonique](concept-authentication-phone-options.md)

Au moment de choisir les méthodes d’authentification à utiliser dans votre locataire, tenez compte de la sécurité et de la facilité d’utilisation des méthodes suivantes :

![Choisir la méthode d’authentification appropriée](media/concept-authentication-methods/authentication-methods.png)

Pour en savoir plus sur la force et la sécurité de ces méthodes ainsi que sur leur fonctionnement, consultez les ressources suivantes :

- [Quelles sont les méthodes d’authentification et de vérification disponibles dans Azure Active Directory ?](concept-authentication-methods.md)
- [Vidéo : Choisir les méthodes d’authentification appropriées pour garantir la sécurité de l’organisation](https://youtu.be/LB2yj4HSptc)

Vous pouvez utiliser ce [script PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) pour analyser les configurations MFA des utilisateurs, et suggérer la méthode d’authentification MFA appropriée. 

Pour une flexibilité et une facilité d’utilisation optimales, utilisez l’application Microsoft Authenticator. Cette méthode d’authentification offre une expérience utilisateur optimale, ainsi que plusieurs modes (authentification sans mot de passe, notifications push MFA et codes OATH, notamment). L’application Microsoft Authenticator répond également aux [impératifs de l’Authenticator Assurance Level 2](../standards/nist-authenticator-assurance-level-2.md) du National Institute of Standards and Technology (NIST).

Vous pouvez contrôler les méthodes d’authentification disponibles dans votre locataire. Ainsi, vous pouvez bloquer certaines méthodes moins sécurisées, par exemple l’envoi de SMS.

| Méthode d'authentification | Gérer à partir de | Scoping |
|-----------------------|-------------|---------|
| Microsoft Authenticator (notification Push et connexion par téléphone sans mot de passe)    | Paramètres MFA ou
Stratégie des méthodes d’authentification | La connexion par téléphone sans mot de passe à Authenticator peut être limitée à l’étendue des utilisateurs et des groupes |
| Clé de sécurité FIDO2 | Stratégie des méthodes d’authentification | Peut être limitée à l’étendue des utilisateurs et des groupes |
| Jetons OATH logiciels ou matériels | Paramètres d’authentification multifacteur |     |
| Vérification par SMS | Paramètres d’authentification multifacteur | Gestion de la connexion par SMS pour l’authentification principale dans la stratégie d’authentification. La connexion par SMS peut être limitée à l’étendue des utilisateurs et des groupes. |
| Les appels vocaux | Stratégie des méthodes d’authentification |       |


## <a name="plan-conditional-access-policies"></a>Planifier les stratégies d’accès conditionnel

Azure AD MFA est appliqué avec des stratégies d’accès conditionnel. Ces stratégies vous permettent de soumettre les utilisateurs à une authentification multifacteur quand cela est nécessaire pour des raisons de sécurité. Dans le cas contraire, elles ne sont pas utilisées.

![Workflow du processus d’accès conditionnel conceptuel](media/howto-mfa-getstarted/conditional-access-overview-how-it-works.png)

Dans le portail Azure, vous pouvez configurer les stratégies d’accès conditionnel sous **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.

Pour en savoir plus sur la création de stratégies d’accès conditionnel, consultez [Stratégie d’accès conditionnel permettant d’activer l’authentification Azure AD MFA quand un utilisateur se connecte au portail Azure](tutorial-enable-azure-mfa.md). Ainsi, vous pourrez :

- Vous familiariser avec l’interface utilisateur
- Obtenir une première impression du fonctionnement de l’accès conditionnel

Pour obtenir des conseils d’aide de bout en bout sur le déploiement de l’accès conditionnel avec Azure AD, consultez le [plan de déploiement de l’accès conditionnel](../conditional-access/plan-conditional-access.md).

### <a name="common-policies-for-azure-ad-mfa"></a>Stratégies courantes pour Azure AD MFA

Les cas d’usage courants pour imposer l’authentification Azure AD MFA sont les suivants :

- Pour les [administrateurs](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- Pour des [applications spécifiques](tutorial-enable-azure-mfa.md)
- Pour [tous les utilisateurs](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- Pour la [gestion d’Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- Pour les accès à partir d’[emplacements réseau auxquels vous ne faites pas confiance](../conditional-access/untrusted-networks.md)

### <a name="named-locations"></a>Emplacements nommés

Pour gérer vos stratégies d’accès conditionnel, la condition d’emplacement d’une stratégie d’accès conditionnel vous permet de lier les paramètres de contrôle d’accès aux emplacements réseau des utilisateurs. Nous vous recommandons d’utiliser des [emplacements nommés](../conditional-access/location-condition.md) pour pouvoir créer des regroupements logiques de plages d’adresses IP, ou de pays et de régions. Cela entraîne la création d’une stratégie qui bloque la connexion à partir de cet emplacement nommé pour toutes les applications. Veillez à exempter vos administrateurs de cette stratégie.

### <a name="risk-based-policies"></a>Stratégies basées sur les risques

Si votre organisation utilise [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) pour détecter les signaux de risque, utilisez des [stratégies basées sur les risques](../identity-protection/howto-identity-protection-configure-risk-policies.md) à la place des emplacements nommés. Vous pouvez créer des stratégies pour forcer le changement de mot de passe en cas de menace de compromission d’identité, ou pour imposer une authentification multifacteur quand une connexion est considérée comme [risquée en raison d’événements](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) tels que des informations d’identification fuitées, des connexions à partir d’adresses IP anonymes, etc. 

Les stratégies basées sur les risques comprennent les contrôles suivants :

- [Obliger tous les utilisateurs à s’inscrire à Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
- [Exiger un changement de mot de passe pour les utilisateurs à risque élevé](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)
- [Exiger l’authentification multifacteur pour les utilisateurs dont la connexion est à risque moyen ou élevé](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertir des utilisateurs de l’authentification multifacteur par utilisateur à l’authentification multifacteur basée sur l’accès conditionnel

Si vos utilisateurs ont été activés à l'aide d'une authentification Azure AD MFA appliquée par Azure ou reposant sur l'utilisateur, la commande PowerShell suivante peut vous aider à passer à une authentification Azure AD MFA basée sur l'accès conditionnel.

Exécutez ce script PowerShell dans une fenêtre ISE ou enregistrez-le en tant que fichier `.PS1` à exécuter localement. L’opération peut uniquement être effectuée à l’aide du [module MSOnline](/powershell/module/msonline/?view=azureadps-1.0#msonline). 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="plan-user-session-lifetime"></a>Planifier la durée de vie des sessions utilisateur

Durant la planification de votre déploiement de MFA, il est important de réfléchir à la fréquence à laquelle vous souhaitez solliciter l’authentification des utilisateurs. Demander aux utilisateurs d’entrer des informations d’identification semble souvent être une chose à faire, mais cela peut avoir l’effet inverse. Si les utilisateurs sont formés pour entrer leurs informations d’identification sans les penser, ils peuvent les fournir involontairement à une invite d’informations d’identification malveillante.
Azure AD a plusieurs paramètres qui déterminent la fréquence à laquelle vous devez vous réauthentifier. Déterminez les besoins de votre entreprise et de vos utilisateurs, puis configurez les paramètres qui offrent le meilleur compromis pour votre environnement.

Nous recommandons l’utilisation d’appareils avec des jetons d’actualisation PRT (Primary Refresh Tokens) permettant d’améliorer l’expérience de l’utilisateur final et de réduire la durée de vie de la session avec une stratégie de fréquence de connexion, uniquement pour les cas d’usage métier spécifiques.

Pour plus d’informations, consultez [Optimiser les invites de réauthentification et comprendre le fonctionnement de la durée de vie des sessions Azure AD MFA](concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="plan-user-registration"></a>Planifier l’inscription des utilisateurs

L’inscription des utilisateurs à l’authentification MFA est une étape majeure dans chaque déploiement de MFA. Les méthodes d’authentification telles que les appels vocaux et les SMS permettent d’effectuer une préinscription, alors que d’autres méthodes, par exemple l’application Authenticator, nécessitent une interaction de l’utilisateur. Les administrateurs doivent déterminer comment les utilisateurs inscrivent leurs méthodes. 

### <a name="combined-registration-for-sspr-and-azure-ad-mfa"></a>Inscription combinée pour SSPR et Azure AD MFA
Nous vous recommandons d’utiliser l’[expérience d’inscription combinée](howto-registration-mfa-sspr-combined.md) pour Azure AD MFA et [SSPR](concept-sspr-howitworks.md) (réinitialisation de mot de passe en libre-service Azure AD). SSPR permet aux utilisateurs de réinitialiser leur mot de passe de manière sécurisée à l’aide des mêmes méthodes que celles utilisées pour Azure AD MFA. L’inscription combinée représente une seule étape pour les utilisateurs finaux.

### <a name="registration-with-identity-protection"></a>Inscription avec Identity Protection
Azure AD Identity Protection fournit à la fois une stratégie d’inscription et des stratégies automatisées de détection de risque et de correction pour Azure AD MFA. Vous pouvez créer des stratégies pour forcer le changement de mot de passe en cas de menace de compromission d’identité, ou pour imposer une authentification MFA quand une connexion est considérée comme risquée.
Si vous utilisez Azure AD Identity Protection, [configurez la stratégie d’inscription Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) pour inviter les utilisateurs à s’inscrire la prochaine fois qu’ils se connecteront de manière interactive.

### <a name="registration-without-identity-protection"></a>Inscription sans Identity Protection
Si vous ne disposez pas de licences permettant d’activer Azure AD Identity Protection, les utilisateurs sont invités à s’inscrire la prochaine fois que l’authentification MFA est nécessaire au moment de la connexion. Pour imposer l’authentification MFA aux utilisateurs, vous pouvez utiliser des stratégies d’accès conditionnel et cibler les applications fréquemment utilisées, par exemple les systèmes de gestion RH. Si le mot de passe d’un utilisateur est compromis, il peut servir à s’inscrire à l’authentification MFA pour prendre le contrôle de son compte. Nous vous recommandons donc de [sécuriser le processus d’inscription avec des stratégies d’accès conditionnel](../conditional-access/howto-conditional-access-policy-registration.md) nécessitant des appareils et des emplacements approuvés. Vous pouvez renforcer la sécurisation du processus en imposant également un [passe d’accès temporaire](howto-authentication-temporary-access-pass.md). Il s’agit d’un code secret à durée limitée émis par un administrateur, qui répond aux impératifs de l’authentification forte et qui peut être utilisé pour intégrer d’autres méthodes d’authentification, notamment les méthodes d’authentification sans mot de passe.

### <a name="increase-the-security-of-registered-users"></a>Augmenter la sécurité des utilisateurs inscrits
Si des utilisateurs sont inscrits à l’authentification MFA basée sur des SMS ou des appels vocaux, vous pouvez les déplacer vers des méthodes plus sécurisées, par exemple l’application Microsoft Authenticator. Microsoft propose désormais une préversion publique des fonctionnalités qui vous permettent d’inviter les utilisateurs à configurer l’application Microsoft Authenticator durant la connexion. Vous pouvez définir ces invites par groupe, en contrôlant les personnes invitées, et en activant des campagnes ciblées pour déplacer les utilisateurs vers la méthode la plus sécurisée. 

### <a name="plan-recovery-scenarios"></a>Planifier des scénarios de récupération 
Comme indiqué, vérifiez que les utilisateurs sont inscrits à plusieurs méthodes MFA pour qu’ils aient une solution de secours au cas où l’une de ces méthodes ne seraient pas disponibles. Si les utilisateurs ne disposent pas d’une méthode de secours, vous pouvez : 

- Leur fournir un passe d’accès temporaire pour qu’ils puissent gérer leurs propres méthodes d’authentification. Vous pouvez également fournir un passe d’accès temporaire pour permettre l’accès temporaire aux ressources. 
- Mettre à jour leurs méthodes en tant qu’administrateur. Pour ce faire, sélectionnez l’utilisateur concerné dans le portail Azure, sélectionnez Méthodes d’authentification, puis mettez à jour ses méthodes.
Communication avec les utilisateurs

Il est essentiel d’informer les utilisateurs sur les changements à venir, les conditions d’inscription à Azure AD MFA ainsi que toute action utilisateur nécessaire. Nous fournissons des [modèles de communication](https://aka.ms/mfatemplates) et une [documentation destinée à l’utilisateur final](../user-help/security-info-setup-signin.md) pour faciliter la rédaction de vos communications. Dirigez les utilisateurs vers [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) pour qu’ils s’inscrivent en sélectionnant le lien **Informations de sécurité** sur cette page.

## <a name="plan-integration-with-on-premises-systems"></a>Planifier l’intégration avec les systèmes locaux

Les applications qui s’authentifient directement auprès d’Azure AD et qui disposent d’une authentification moderne (WS-Fed, SAML, OAuth, OpenID Connect) peuvent tirer parti des stratégies d’accès conditionnel.
Certaines applications héritées et locales ne s’authentifient pas directement auprès d’Azure AD. Elles nécessitent des étapes supplémentaires pour permettre l’utilisation d’Azure AD MFA. Vous pouvez les intégrer à l’aide du proxy d’application Azure AD ou des [services de stratégie réseau](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures).

### <a name="integrate-with-ad-fs-resources"></a>Effectuer l’intégration aux ressources AD FS

Nous vous recommandons de migrer les applications sécurisées avec les services de fédération Active Directory (AD FS) vers Azure AD. Toutefois, si vous n’êtes pas prêt à les migrer vers Azure AD, vous pouvez utiliser l’adaptateur Azure MFA avec AD FS 2016 ou une version plus récente.
Si votre organisation est fédérée avec Azure AD, vous pouvez [configurer Azure AD MFA en tant que fournisseur d’authentification avec des ressources AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa) à la fois localement et dans le cloud.  

### <a name="radius-clients-and-azure-ad-mfa"></a>Clients RADIUS et Azure AD MFA

En ce qui concerne les applications qui utilisent l’authentification RADIUS, nous vous recommandons de déplacer les applications clientes vers des protocoles modernes tels que SAML, OpenID Connect ou OAuth sur Azure AD. Si l’application ne peut pas être mise à jour, vous pouvez déployer un [serveur NPS (Network Policy Server) avec l’extension Azure MFA](howto-mfa-nps-extension.md). L’extension NPS (Network Policy Server) sert d’adaptateur entre les applications RADIUS et Azure AD MFA pour fournir un deuxième facteur d’authentification.

#### <a name="common-integrations"></a>Intégrations courantes

De nombreux fournisseurs prennent désormais en charge l’authentification SAML pour leurs applications. Dans la mesure du possible, nous vous recommandons de fédérer ces applications avec Azure AD et d’appliquer l’authentification MFA via l’accès conditionnel. Si votre fournisseur ne prend pas en charge l’authentification moderne, vous pouvez utiliser l’extension NPS.
Les intégrations courantes de clients RADIUS incluent des applications telles que les [passerelles des services Bureau à distance](howto-mfa-nps-extension-rdg.md) et les [serveurs VPN](howto-mfa-nps-extension-vpn.md). 

D’autres peuvent inclure :

- Citrix Gateway

  [Citrix Gateway](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-gateway-microsoft-azure.html#microsoft-azure-mfa-deployment-methods) prend en charge l’intégration des extensions RADIUS et NPS ainsi que l’intégration de SAML.

- Cisco VPN
  - Cisco VPN prend en charge à la fois l’authentification RADIUS et l’[authentification SAML pour SSO](../saas-apps/cisco-anyconnect.md).
  - En passant de l’authentification RADIUS à SAML, vous pouvez intégrer Cisco VPN sans déployer l’extension NPS.

- Tous les VPN

## <a name="deploy-azure-ad-mfa"></a>Déployer Azure AD MFA

Votre plan de déploiement MFA doit inclure un déploiement pilote suivi par des vagues de déploiement compatibles avec votre capacité de prise en charge. Commencez le déploiement en appliquant vos stratégies d’accès conditionnel à un petit groupe d’utilisateurs pilotes. Après avoir évalué l’impact sur les utilisateurs pilotes, le processus utilisé et les comportements d’inscription, vous pouvez ajouter d’autres groupes à la stratégie ou ajouter d’autres utilisateurs aux groupes existants.

Effectuez les étapes ci-dessous :

1. Respecter les prérequis nécessaires
1. Configurez les méthodes d’authentification choisies.
1. Configurez vos stratégies d’accès conditionnel.
1. Configurer les paramètres de durée de vie de session
1. Configurer les stratégies d’inscription Azure AD MFA 

## <a name="manage-azure-ad-mfa"></a>Gérer Azure AD MFA
Cette section fournit des informations sur la création de rapports et la résolution de problèmes pour Azure AD MFA.

### <a name="reporting-and-monitoring"></a>Rapports et supervision

Azure AD propose des rapports qui fournissent des insights techniques et métier, suivent la progression de votre déploiement et vérifient si les utilisateurs réussissent à se connecter avec l’authentification MFA. Demandez à vos propriétaires d’applications métier et techniques de s’attribuer la propriété de ces rapports et de les consommer en fonction des exigences de votre organisation.

Vous pouvez superviser l’inscription et l’utilisation des méthodes d’authentification dans votre organisation à l’aide du [tableau de bord d’activité des méthodes d’authentification](howto-authentication-methods-activity.md). Cela vous permet de comprendre quelles sont les méthodes inscrites et comment elles sont utilisées.

#### <a name="sign-in-report-to-review-mfa-events"></a>Rapport de connexion pour passer en revue les événements MFA

Les rapports de connexion Azure AD incluent les détails relatifs aux événements d’authentification quand un utilisateur est soumis à une authentification multifacteur. De plus, ils indiquent si des stratégies d’accès conditionnel ont été utilisées. Vous pouvez également utiliser PowerShell pour créer des rapports sur les utilisateurs inscrits à l’authentification MFA. 

Vous pouvez visualiser l’extension NPS et les journaux AD FS à partir de **Sécurité** > **MFA** > **Rapport d’activité**.

Pour plus d’informations et pour obtenir des rapports MFA supplémentaires, consultez [Passer en revue les événements Azure AD Multi-Factor Authentication](howto-mfa-reporting.md#view-the-azure-ad-sign-ins-report).

### <a name="troubleshoot-azure-ad-mfa"></a>Résoudre les problèmes liés à Azure AD MFA
Pour plus d’informations sur les problèmes courants, consultez [Résoudre les problèmes liés à Azure AD MFA](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues).

## <a name="next-steps"></a>Étapes suivantes

[Déployer d’autres fonctionnalités d’identité](../fundamentals/active-directory-deployment-plans.md)
