---
title: Planifier et exécuter un déploiement d’Azure multi-Factor Authentication - Azure Active Directory
description: Planification du déploiement de Microsoft Azure multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca69fc23d580b61e74fe56b3d0c3524fdfad747
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235532"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planification d’un déploiement d’Azure multi-Factor Authentication basé sur le cloud

Personnes sont connectent aux ressources d’organisation dans les scénarios plus en plus complexes. Personnes se connecter à partir d’appareils appartenant à l’organisation personnels, publics et sur site et hors du réseau d’entreprise à l’aide de Smartphones, tablettes, PC et ordinateurs portables, souvent sur plusieurs plateformes. Dans ce monde toujours connecté, multi-device et multi-plateforme, la sécurité des comptes d’utilisateur est plus importante que jamais. Les mots de passe, quel que soit leur complexité, utilisée sur les appareils, les réseaux et les plateformes ne sont plus suffisantes pour garantir la sécurité du compte d’utilisateur, en particulier lorsque les utilisateurs ont tendance à réutiliser les mots de passe des comptes. Sophistiquées anti-hameçonnage et autres attaques peuvent entraîner des noms d’utilisateur et mots de passe qui est validée et vendus dans le web invisible l’ingénierie sociale.

[Azure multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) contribue à sécuriser l’accès aux données et applications. Il fournit une couche supplémentaire de sécurité à l’aide d’un deuxième formulaire d’authentification. Les organisations peuvent utiliser [accès conditionnel](../conditional-access/overview.md) pour que la solution réponde à leurs besoins spécifiques.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer un déploiement d’Azure multi-Factor Authentication, il existe des éléments prérequis à prendre en compte.

| Scénario | Configuration requise |
| --- | --- |
| **Cloud uniquement** environnement identité avec l’authentification moderne | **Aucune tâche de configuration requise supplémentaire** |
| **Hybride** scénarios d’identité | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) est déployé et les identités des utilisateurs sont synchronisées ou fédérées avec les Services de domaine en local Active Directory avec Azure Active Directory. |
| En local des applications héritées publiées pour l’accès au cloud | Azure AD [Proxy d’Application](../manage-apps/application-proxy.md) est déployé. |
| Utilisation d’Azure MFA avec l’authentification RADIUS | Un [serveur NPS (Network Policy Server)](howto-mfa-nps-extension.md) est déployé. |
| Les utilisateurs ont Microsoft Office 2010 ou version antérieure, ou Apple Mail pour iOS 11 ou une version antérieure | Mise à niveau vers [Microsoft Office 2013 ou version ultérieure](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) et Apple mail pour iOS 12 ou ultérieure. Accès conditionnel n’est pas pris en charge par les protocoles d’authentification hérités. |

## <a name="plan-user-rollout"></a>Planifier le déploiement de l’utilisateur

Votre plan de déploiement MFA doit inclure un déploiement pilote suivi par vagues de déploiement qui se trouvent dans votre capacité de prise en charge. Commencer votre déploiement en appliquant vos stratégies d’accès conditionnel à un petit groupe d’utilisateurs pilotes. Après avoir évalué l’impact sur les utilisateurs pilotes, les processus utilisés et les comportements de l’inscription, vous pouvez ajouter plusieurs groupes à la stratégie ou ajouter d’autres utilisateurs aux groupes existants.

### <a name="user-communications"></a>Communications de l’utilisateur

Il est essentiel pour informer les utilisateurs, dans les communications planifiées, les modifications à venir, conditions d’inscription de Azure MFA et toutes les actions utilisateur nécessaires. Nous vous recommandons de communications sont développées conjointement avec les représentants d’au sein de votre organisation, comme un départements de Communications, de gestion des modifications ou de ressources humaines.

Microsoft fournit [modèles de communication](https://aka.ms/mfatemplates) et [documentation de l’utilisateur final](../user-help/security-info-setup-signin.md) pour aider à le brouillon de vos communications. Vous pouvez envoyer aux utilisateurs de [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) pour inscrire directement en sélectionnant le **des informations de sécurité** liens sur cette page.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Azure multi-factor Authentication est déployée en appliquant des stratégies avec l’accès conditionnel. Un [stratégie d’accès conditionnel](../conditional-access/overview.md) peuvent demander aux utilisateurs effectuer l’authentification multifacteur lorsque certains critères tels que :

* Tous les utilisateurs, un utilisateur spécifique, un membre d’un rôle de groupe ou affecté
* Application cloud spécifique en cours d’accès
* Plateforme d’appareil.
* État du périphérique
* Emplacement réseau ou adresse de géo-localisés
* Applications clientes
* Risque de connexion (nécessite Identity Protection)
* Conformité de l’appareil
* Appareil joint Azure AD hybride
* Application cliente approuvée

Utilisez les posters personnalisables et modèles d’e-mails dans [matériaux de déploiement de l’authentification multifacteur](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) à déployer l’authentification multifacteur sur votre organisation.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Activer l’authentification multifacteur avec l’accès conditionnel

Stratégies d’accès conditionnel appliquent l’inscription, nécessitant des utilisateurs non inscrits terminer l’inscription à la première connexion, un facteur de sécurité important.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribue à la fois une stratégie d’inscription pour et les stratégies de détection et correction automatisée des risques pour l’histoire de l’authentification multifacteur Azure. Les stratégies peuvent être créées pour imposer des modifications de mot de passe lorsqu’il existe une menace d’identité compromise ou exiger l’authentification Multifacteur lors de la connexion est considérée comme à risque par ce qui suit [événements](../reports-monitoring/concept-risk-events.md):

* Informations d’identification divulguées
* Connexions depuis des adresses IP anonymes
* Voyage impossible vers des emplacements inhabituels
* Connexions depuis des emplacements non connus
* Connexions depuis des appareils infectés
* Connexions depuis des adresses IP des activités suspectes

Certains des événements à risque détectés par Azure Active Directory Identity Protection se produisent en temps réel et certaines nécessitent un traitement en mode hors connexion. Les administrateurs peuvent choisir de bloquer les utilisateurs qui présentent des comportements à risque et corriger manuellement, nécessitent une modification de mot de passe ou requièrent une authentification multifacteur dans le cadre de leurs stratégies d’accès conditionnel.

## <a name="define-network-locations"></a>Définissent les emplacements réseau

Nous recommandons d’organisations d’utiliser l’accès conditionnel pour définir leur réseau à l’aide [emplacements nommés](../conditional-access/location-condition.md#named-locations). Si votre organisation à l’aide de la Protection d’identité, envisagez d’utiliser des stratégies en fonction des risques au lieu des emplacements nommés.

### <a name="configuring-a-named-location"></a>Configuration d’un emplacement nommé

1. Ouvrez **Azure Active Directory** dans le portail Azure
2. Cliquez sur **l’accès conditionnel**
3. Cliquez sur **emplacements nommés**
4. Cliquez sur **nouvel emplacement**
5. Dans le **nom** champ, entrez un nom explicite
6. Indiquez si vous définissez l’emplacement à l’aide de plages d’adresses IP ou le pays/régions
   1. Si vous utilisez des plages d’adresses IP
      1. Décider s’il faut marquer l’emplacement comme approuvé. La connexion à partir d’un emplacement approuvé réduit le risque de connexion d’un utilisateur. Marquez uniquement cet emplacement comme approuvé si vous connaissez les plages IP entrées sont établies et crédibles dans votre organisation.
      2. Spécifiez les plages IP
   2. Si vous utilisez des pays/régions
      1. Développez le menu déroulant et sélectionnez le pays ou régions que vous souhaitez définir pour cet emplacement nommé.
      2. Décider s’il faut inclure les zones inconnues. Zones inconnues sont des adresses IP qui ne peut pas être mappés à un pays/région.
7. Cliquez sur **Créer**

## <a name="plan-authentication-methods"></a>Planifier des méthodes d’authentification

Les administrateurs peuvent choisir le [méthodes d’authentification](../authentication/concept-authentication-methods.md) qu’ils souhaitent rendre disponibles pour les utilisateurs. Il est important autoriser plus d’une méthode d’authentification unique, permettant aux utilisateurs une méthode de sauvegarde au cas où sa principale méthode n’est pas disponible. Les méthodes suivantes sont disponibles pour les administrateurs à activer :

### <a name="notification-through-mobile-app"></a>Notification via une application mobile

Une notification push est envoyée à l’application Microsoft Authenticator sur votre appareil mobile. L’utilisateur consulte la notification et sélectionne **approuver** pour terminer la vérification. L’option la moins intrusive pour les utilisateurs fournissent des notifications Push via une application mobile. Elles sont également l’option la plus fiable et sécurisée, car elles utilisent une connexion de données plutôt que de téléphonie.

> [!NOTE]
> Si votre organisation a personnel travaillant dans ou en déplacement à la Chine, le **Notification via application mobile** méthode sur **les appareils Android** ne fonctionne pas dans ce pays. Autres méthodes doivent être accessibles aux utilisateurs.

### <a name="verification-code-from-mobile-app"></a>Code de vérification de l’application mobile

Une application mobile comme l’application Microsoft Authenticator génère un nouveau code de vérification OATH toutes les 30 secondes. L’utilisateur entre ce code de vérification dans l’interface de connexion. L’option d’application mobile peut être utilisée si le téléphone dispose d’un signal cellulaire ou données.

### <a name="call-to-phone"></a>Appel vers le téléphone

Un appel vocal automatisé est placé à l’utilisateur. L’utilisateur répond à l’appel et appuie sur **#** sur le clavier du téléphone pour approuver leur authentification. Appel téléphonique est une méthode de sauvegarde très efficace pour le code de notification ou de vérification à partir d’une application mobile.

### <a name="text-message-to-phone"></a>Message texte vers le téléphone

Un message texte qui contient un code de vérification est envoyé à l’utilisateur, l’utilisateur est invité à entrer le code de vérification dans l’interface de connexion.

### <a name="choose-verification-options"></a>Choisir les options de vérification

1. Accédez à **Azure Active Directory**, **Utilisateurs**, **Multi-Factor Authentication**.

   ![Accès au portail Multi-Factor Authentication à partir du panneau Utilisateurs Azure AD dans le portail Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Sous le nouvel onglet qui vient de s’ouvrir dans votre navigateur, accédez aux **paramètres de service**.
1. Sous **options de vérification**, cochez toutes les cases pour les méthodes disponibles pour les utilisateurs.

   ![Configurer des méthodes de vérification dans l’onglet de paramètres de service Authentification multifacteur](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Cliquez sur **Save**(Enregistrer).
1. Fermez l’onglet **paramètres de service**.

## <a name="plan-registration-policy"></a>Planifier la stratégie d’inscription

Les administrateurs doivent déterminer comment les utilisateurs seront inscrire leurs méthodes. Les organisations doivent [activer la nouvelle expérience d’inscription combiné](howto-registration-mfa-sspr-combined.md) pour Azure MFA et le mot de passe libre-service (SSPR) de réinitialisation. SSPR permet aux utilisateurs de réinitialiser leur mot de passe de manière sécurisée avec les mêmes méthodes qu’ils utilisent pour l’authentification multifacteur. Nous vous recommandons de que ceci, combiné d’enregistrement, actuellement en version préliminaire publique, car il s’agit d’une excellente expérience pour les utilisateurs, avec la capacité d’enregistrer une seule fois pour les deux services. Activation des mêmes méthodes que celles pour SSPR et Azure MFA permet à vos utilisateurs à inscrire pour utiliser ces deux fonctionnalités.

### <a name="registration-with-identity-protection"></a>Inscription avec la Protection d’identité

Si votre organisation utilise Azure Active Directory Identity Protection, [configurer la stratégie d’inscription MFA](../identity-protection/howto-mfa-policy.md) pour inviter les utilisateurs à inscrire la prochaine fois qu’ils se connectent de manière interactive.

### <a name="registration-without-identity-protection"></a>Inscription sans Protection d’identité

Si votre organisation n’a pas de licences qui activent la Protection d’identité, les utilisateurs sont invités à inscrire la prochaine fois que l’authentification Multifacteur est requise à la connexion. Les utilisateurs ne peuvent pas être inscrits pour l’authentification Multifacteur s’ils n’utilisent des applications protégées avec l’authentification Multifacteur. Il est important d’obtenir tous les utilisateurs inscrits de sorte que les mauvais acteurs ne peuvent pas deviner le mot de passe d’un utilisateur et s’inscrire à MFA en leur nom, prenant efficacement le contrôle du compte.

#### <a name="enforcing-registration"></a>L’application d’inscription

En procédant comme suit un accès conditionnel stratégie peut forcer les utilisateurs à s’inscrire pour l’authentification multifacteur

1. Créer un groupe, ajouter tous les utilisateurs actuellement pas inscrits.
2. L’accès conditionnel, appliquer l’authentification multifacteur pour ce groupe pour l’accès à toutes les ressources.
3. Périodiquement, réévaluer l’appartenance au groupe et supprimer des utilisateurs qui se sont inscrits dans le groupe.

Vous pouvez identifier les utilisateurs de Azure MFA inscrits et non inscrits avec des commandes PowerShell qui s’appuient sur le [module MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identifier les utilisateurs inscrits

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifier les utilisateurs non inscrits

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Les utilisateurs de convertir à partir de l’authentification Multifacteur par utilisateur à l’accès conditionnel en fonction de MFA

Si vos utilisateurs ont été activées à l’aide par utilisateur activé et appliqué l’authentification multifacteur Azure PowerShell suivante peuvent vous aider à effectuer la conversion à l’accès conditionnel basé sur Azure multi-Factor Authentication.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

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

```

## <a name="plan-conditional-access-policies"></a>Planifier les stratégies d’accès conditionnel

Pour planifier votre stratégie d’accès conditionnel, qui détermine quand MFA et autres contrôles sont requis, reportez-vous à [quel est l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md).

Il est important que vous empêcher d’appropriation votre client Azure AD. Vous pouvez atténuer l’impact de ce manque involontaire d’un accès administratif par [création de deux ou plusieurs comptes d’accès d’urgence dans votre client](../users-groups-roles/directory-emergency-access.md) et les excluant ainsi de votre stratégie d’accès conditionnel.

### <a name="create-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général.
1. Accédez à **Azure Active Directory**, **accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Entrez un nom explicite pour votre stratégie.
1. Sous **utilisateurs et groupes** :
   * Dans l’onglet **Inclure**, sélectionnez la case d’option **Tous les utilisateurs**
   * Sur le **exclure** onglet, cochez la case pour **utilisateurs et groupes** et choisissez vos comptes d’accès d’urgence.
   * Cliquez sur **Done**.
1. Sous **Applications Cloud**, sélectionnez la case d’option **Toutes les applications cloud**.
   * FACULTATIF : sous l’onglet **Exclure**, choisissez les applications cloud pour lesquelles votre organisation ne nécessite pas d’authentification multifacteur.
   * Cliquez sur **Done**.
1. Sous la section **Conditions** :
   * FACULTATIF : si vous avez activé Azure Identity Protection, vous pouvez choisir d’évaluer le risque de connexion dans le cadre de la stratégie.
   * FACULTATIF : si vous avez configuré des emplacements approuvés ou des emplacements nommés, vous pouvez spécifier l’inclusion ou l’exclusion de ces emplacements à partir de la stratégie.
1. Sous **Accord**, assurez-vous que la case d’option **Accorder l’accès** est sélectionnée.
    * Cochez la case **Exiger une authentification multifacteur**.
    * Cliquez sur **Sélectionner**.
1. Ignorez la section **Session**.
1. Basculez **Activer la stratégie** sur **Activé**.
1. Cliquez sur **Créer**.

![Créer une stratégie d’accès conditionnel pour activer l’authentification Multifacteur pour les utilisateurs du portail Azure dans le groupe pilote](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Planification de l’intégration avec les systèmes locaux

Certaines applications héritées et en local qui ne s’authentifient pas directement auprès d’Azure AD nécessitent des étapes supplémentaires pour utiliser l’authentification Multifacteur, notamment :

* Hérité des applications sur site, ce qui vous devrez utiliser le proxy d’Application.
* RADIUS des applications sur site, qui vous devrez utiliser l’adaptateur MFA avec un serveur NPS.
* AD FS des applications sur site, qui vous devrez utiliser l’adaptateur MFA avec AD FS 2016.

Les applications qui s’authentifier directement auprès d’Azure AD et l’ont moderne (WS-Fed, SAML, OAuth, OpenID Connect) peuvent tirer parti de l’accès conditionnel stratégies directement.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Utiliser l’authentification Multifacteur Azure avec le Proxy d’Application Azure AD

Applications locales peuvent être publiés sur votre annuaire Azure AD locataire [Proxy d’Application Azure AD](../manage-apps/application-proxy.md) et peuvent tirer parti d’Azure multi-Factor Authentication s’ils sont configurés pour utiliser Azure AD l’authentification préalable.

Ces applications sont soumis aux stratégies d’accès conditionnel qui imposent d’Azure multi-Factor Authentication, tout comme n’importe quel autre Azure intégrée à Active Directory application.

De même, si l’authentification multifacteur Azure est appliquée pour toutes les connexions des utilisateurs, en local applications publiées avec un Proxy d’Application Azure AD seront protégées.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>L’intégration d’Azure multi-Factor Authentication avec le serveur NPS

L’extension de serveur NPS (Network Policy Server) pour Azure MFA permet d’ajouter des fonctionnalités de MFA basées sur le cloud à votre infrastructure d’authentification à l’aide de vos serveurs existants. Avec l’extension NPS, vous pouvez ajouter un appel téléphonique, SMS ou application téléphonique à votre flux d’authentification existante. Cette intégration présente les limitations suivantes :

* Avec le protocole CHAPv2, pris en charge uniquement les notifications de push de l’application de l’authentificateur et appel vocal.
* Stratégies d’accès conditionnel ne peut pas être appliqués.

L’extension NPS joue le rôle d’adaptateur entre RADIUS et Azure MFA sur le cloud pour fournir un second facteur d’authentification pour protéger [VPN](howto-mfa-nps-extension-vpn.md), [connexions de passerelle des services Bureau à distance](howto-mfa-nps-extension-rdg.md), ou autres capable de RADIUS applications. Les utilisateurs que register pour Azure MFA dans cet environnement sera demandé de fournir toutes les tentatives d’authentification, l’absence de stratégies d’accès conditionnel signifient que MFA est toujours requise.

#### <a name="implementing-your-nps-server"></a>Implémentation de votre serveur NPS

Si vous avez une instance de serveur NPS déployée et en cours d’utilisation référencer au préalable, [intégrer votre Infrastructure NPS existante à Azure multi-Factor Authentication](howto-mfa-nps-extension.md). Si vous configurez NPS pour la première fois, reportez-vous à [serveur NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) pour obtenir des instructions. Conseils de dépannage, voir l’article [résoudre les messages d’erreur à partir de l’extension NPS pour Azure multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Préparer le serveur NPS pour les utilisateurs qui ne sont pas inscrits pour l’authentification Multifacteur

Choisissez ce qui se passe lorsque les utilisateurs qui ne sont pas inscrits avec l’authentification Multifacteur tentent de s’authentifier. Utilisez le paramètre de Registre `REQUIRE_USER_MATCH` dans le chemin d’accès de Registre `HKLM\Software\Microsoft\AzureMFA` pour contrôler le comportement de la fonctionnalité. Ce paramètre a une seule option de configuration.

| Clé | Valeur | Default |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | Non défini (équivaut à TRUE) |

L’objectif de ce paramètre est de déterminer l’action à exécuter lorsqu’un utilisateur n’est pas inscrit pour l’authentification MFA. Les effets de la modification de ce paramètre sont répertoriées dans le tableau ci-dessous.

| Paramètres | État de l’utilisateur MFA | Effets |
| --- | --- | --- |
| Clé n’existe pas | Non inscrits | Authentification MFA n’aboutit pas |
| Valeur définie sur True / ne pas définie | Non inscrits | Authentification MFA n’aboutit pas |
| Clé de la valeur False | Non inscrits | Authentification sans authentification Multifacteur |
| Clé de la valeur False ou True | Inscrit | Doit s’authentifier avec MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Intégrer avec Active Directory Federation Services

Si votre organisation est fédérée avec Azure AD, vous pouvez utiliser [Azure multi-Factor Authentication pour sécuriser les ressources d’AD FS](multi-factor-authentication-get-started-adfs.md), à la fois en local et dans le cloud. Azure MFA vous permet de réduire les mots de passe et fournissent un moyen plus sûr d’authentifier. À compter de Windows Server 2016, vous pouvez maintenant configurer Azure MFA pour l’authentification principale.

Contrairement à avec AD FS dans Windows Server 2012 R2, l’adaptateur Azure MFA de 2016 AD FS s’intègre directement à Azure AD et ne requiert pas de serveur Azure MFA en local. L’adaptateur Azure MFA est intégrée à Windows Server 2016, et il n’est pas nécessaire pour une installation supplémentaire.

Lors de l’utilisation de Azure MFA avec AD FS 2016 et l’application cible est soumis à la stratégie d’accès conditionnel, il existe des considérations supplémentaires :

* Accès conditionnel est disponible lorsque l’application est une partie de confiance à Azure AD fédéré avec AD FS 2016.
* Accès conditionnel n’est pas disponible lorsque l’application est une partie de confiance pour AD FS 2016 et est gérée ou fédérée avec AD FS 2016.
* Accès conditionnel n’est également pas disponible lorsque AD FS 2016 est configuré pour utiliser Azure MFA en tant que la méthode d’authentification principale.

#### <a name="ad-fs-logging"></a>Journalisation AD FS

Journalisation 2016 standard AD FS dans le journal de sécurité Windows et le journal de l’administrateur AD FS, contient des informations sur les demandes d’authentification et leur réussite ou l’échec. Les données de journal des événements au sein de ces événements indiquera si Azure MFA a été utilisée. Par exemple, un ID d’événement d’audit FS AD 1200 peut contenir :

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renouveler et gérer des certificats

Sur chaque serveur AD FS, dans l’ordinateur local Mes Store, il y aura une authentification Multifacteur Azure auto-signé certificat unité d’organisation intitulée = Microsoft AD FS Azure MFA, qui contient la date d’expiration de certificat. Vérifier la période de validité de ce certificat sur chaque serveur AD FS pour déterminer la date d’expiration.

Si la période de validité des certificats est arrivent à expiration, [générer et vérifier un nouveau certificat d’authentification Multifacteur sur chaque serveur AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Les conseils suivants vous explique comment gérer les certificats Azure MFA sur vos serveurs AD FS. Lorsque vous configurez des services AD FS avec Azure MFA, les certificats générés par le biais de la `New-AdfsAzureMfaTenantCertificate` applet de commande PowerShell sont valides pendant 2 ans. Renouveler et installer les certificats renouvelés avant l’expiration pour le plateau interruptions de service d’authentification Multifacteur.

## <a name="implement-your-plan"></a>Implémenter votre plan

Maintenant que vous avez planifié votre solution, vous pouvez implémenter en suivant les étapes ci-dessous :

1. Répondre à tous les prérequis
   1. Déployer [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) pour tous les scénarios hybrides
   1. Déployer [Proxy d’Application Azure AD](../manage-apps/application-proxy.md) pour sur toutes les applications locales publiées pour l’accès au cloud
   1. Déployer [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) pour toute authentification RADIUS
   1. Vérifiez les utilisateurs ont mis à niveau vers des versions prises en charge de Microsoft Office avec l’authentification moderne activée
1. Configurer choisi [méthodes d’authentification](#choose-verification-options)
1. Définir votre [réseau emplacements nommés](../conditional-access/location-condition.md#named-locations)
1. Sélectionner les groupes à commencer le déploiement MFA.
1. Configurer votre [stratégies d’accès conditionnel](#create-conditional-access-policy)
1. Configurer votre stratégie d’inscription à MFA
   1. [MFA combiné et SSPR](howto-registration-mfa-sspr-combined.md)
   1. Avec [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Envoyer des communications de l’utilisateur et d’obtenir les utilisateurs à inscrire au [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Effectuer le suivi de qui est inscrit](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Gérer votre solution

Rapports pour Azure MFA

Azure multi-Factor Authentication fournit des rapports via le portail Azure :

| Rapport | Lieu | Description |
| --- | --- | --- |
| Alertes relatives à l’utilisation et aux fraudes | Azure AD > Connexions | Fournit des informations sur l’utilisation globale, un récapitulatif par utilisateur, des informations détaillées sur les utilisateurs, ainsi que l’historique des alertes de fraude émises au cours de la plage de dates spécifiée. |

## <a name="troubleshoot-mfa-issues"></a>Résoudre les problèmes d’authentification Multifacteur

Trouver des solutions pour les problèmes courants avec Azure MFA à la [article de résolution des problèmes Azure multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) sur le Center Support Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* [Que sont les méthodes d’authentification ?](concept-authentication-methods.md)
* [Activer l’inscription convergée pour Azure Multi-Factor Authentication et la réinitialisation de mot de passe en libre-service Azure AD](concept-registration-mfa-sspr-converged.md)
* Qu’est-ce qui détermine qu’un utilisateur est ou n’est pas invité à effectuer une MFA ? Consultez la section [Rapport de connexion Azure AD du document Rapports dans Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
