---
title: Migrer vers l’authentification utilisateur Azure AD MFA et Azure AD - Azure Active Directory
description: Conseils d’aide pas à pas pour passer du Serveur Azure MFA local à Azure AD MFA et à l’authentification utilisateur Azure AD
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f10052f6bcb458b98535d54a9b2fa5deb749c38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524386"
---
# <a name="migrate-to-azure-ad-mfa-and-azure-ad-user-authentication"></a>Migrer vers l’authentification utilisateur Azure AD MFA et Azure AD

L’authentification MFA (authentification multifacteur) permet de sécuriser votre infrastructure et vos ressources contre les acteurs malveillants. Le Serveur Multi-Factor Authentication (Serveur MFA) de Microsoft n’est plus proposé pour les nouveaux déploiements. Les clients qui utilisent le serveur MFA doivent passer à Azure AD Multi-Factor Authentication (Azure AD MFA). 

Il existe plusieurs options pour migrer votre authentification MFA (authentification multifacteur) du serveur MFA vers Azure AD (Azure Active Directory). En voici quelques-uns :

* Bon : Déplacement uniquement de votre [service MFA vers Azure AD](how-to-migrate-mfa-server-to-azure-mfa.md). 
* Mieux : Déplacement de votre service MFA et de l’authentification utilisateur vers Azure AD (traité dans cet article).
* Meilleur : Déplacement de toutes vos applications, de votre service MFA et de l’authentification utilisateur vers Azure AD. Consultez la section de cet article concernant le déplacement des applications vers Azure AD, si vous comptez déplacer des applications (traité dans cet article). 

Pour sélectionner l’option de migration MFA appropriée dans le cadre de votre organisation, consultez les considérations relatives à la [migration du serveur MFA vers Azure Active Directory MFA](how-to-migrate-mfa-server-to-azure-mfa.md). 

Le diagramme suivant montre le processus qui permet de migrer vers Azure AD MFA et l’authentification cloud tout en conservant certaines applications sur AD FS. Ce processus permet la migration itérative des utilisateurs du serveur MFA vers Azure MFA en fonction de l’appartenance au groupe.

Chaque étape est expliquée dans les sections suivantes de cet article.

>[!NOTE]
>Si vous prévoyez de déplacer des applications vers Azure Active Directory dans le cadre de cette migration, vous devez le faire avant la migration MFA. Si vous déplacez toutes vos applications, vous pouvez ignorer certaines sections du processus de migration MFA. Consultez la section relative au déplacement d’applications à la fin de cet article.

## <a name="process-to-migrate-to-azure-ad-and-user-authentication"></a>Processus de migration vers Azure AD et authentification utilisateur

![Processus de migration vers Azure AD et authentification utilisateur.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-cloud-authentication-flow.png)

## <a name="prepare-groups-and-conditional-access"></a>Préparer les groupes et l’accès conditionnel

Les groupes sont utilisés à trois niveaux pour la migration MFA.
* **Pour déplacer de manière itérative les utilisateurs vers Azure AD MFA avec un déploiement par étapes.**
Utilisez un groupe créé dans Azure AD, également appelé groupe cloud uniquement. Vous pouvez utiliser les groupes de sécurité Azure AD ou les groupes Microsoft 365 pour le déplacement des utilisateurs vers l’authentification MFA ainsi que pour les stratégies d’accès conditionnel.  Pour plus d’informations, consultez Création d’un groupe de sécurité Azure AD et Vue d’ensemble des groupes Microsoft 365 pour les administrateurs.
  >[!IMPORTANT]
  >Les groupes imbriqués et dynamiques ne sont pas pris en charge dans le processus de déploiement par étapes. N’utilisez pas ces types de groupe pour votre effort de déploiement par étapes.
* **Stratégies d’accès conditionnel**. Vous pouvez utiliser Azure AD ou des groupes locaux pour l’accès conditionnel.
* **Pour appeler Azure AD MFA dans le cadre des applications AD FS avec des règles de revendication.**
Cela s’applique uniquement si vous avez des applications sur AD FS.
Il doit s’agir d’un groupe de sécurité Active Directory local. Une fois qu’Azure AD MFA est défini en tant que méthode d’authentification supplémentaire, vous pouvez désigner des groupes d’utilisateurs qui vont l’utiliser sur chaque approbation de partie de confiance. Par exemple, vous pouvez appeler Azure AD MFA pour les utilisateurs que vous avez déjà fait migrer, et le serveur MFA pour ceux qui n’ont pas encore été migrés. Cela est utile à la fois pour les tests et la migration. 

>[!NOTE] 
>Nous vous déconseillons de réutiliser les groupes utilisés pour la sécurité. Quand vous utilisez un groupe de sécurité pour sécuriser un groupe d’applications très important via une stratégie d’accès conditionnel, il s’agit de la seule utilisation de ce groupe.

### <a name="configure-conditional-access-policies"></a>Configurer des stratégies d’accès conditionnel

Si vous utilisez déjà l’accès conditionnel pour déterminer le moment où l’authentification MFA est proposée aux utilisateurs, vous n’avez pas à changer vos stratégies. Au fur et à mesure que les utilisateurs migrent vers l’authentification cloud, ils commencent à utiliser le service Azure AD MFA tel qu’il est défini par vos stratégies d’accès conditionnel existantes. Ils ne sont plus redirigés vers AD FS et le serveur MFA.

Si SupportsMFA a la valeur false pour vos domaines fédérés, cela signifie probablement que vous appliquez l’authentification MFA sur AD FS à l’aide de règles de revendication. Dans ce cas, vous devez analyser vos règles de revendication sur l’approbation de partie de confiance Azure AD et créer des stratégies d’accès conditionnel qui prennent en charge les mêmes objectifs de sécurité.

Si vous devez configurer des stratégies d’accès conditionnel, vous devez le faire avant d’activer le déploiement par étapes. Pour plus d’informations, consultez les ressources suivantes :
* [Planifier un déploiement d’accès conditionnel](../conditional-access/plan-conditional-access.md)
* [Stratégies d’accès conditionnel courantes](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="prepare-ad-fs"></a>Préparer AD FS 

Si vous n’avez pas d’applications dans AD FS qui nécessite une authentification MFA, vous pouvez ignorer cette section et accéder à la section Préparer le déploiement par étapes.

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>Mettre à niveau la batterie de serveurs AD FS vers FBL 4 2019

Dans AD FS 2019, Microsoft a publié une nouvelle fonctionnalité qui permet de spécifier des méthodes d’authentification supplémentaires pour une partie de confiance, par exemple une application. Pour ce faire, utilisez l’appartenance à un groupe afin de déterminer le fournisseur d’authentification. En spécifiant une méthode d’authentification supplémentaire, vous pouvez effectuer une transition vers Azure AD MFA tout en conservant les autres méthodes d’authentification pendant la transition. 

Pour plus d’informations, consultez [Mise à niveau vers AD FS dans Windows Server 2016 à l’aide d’une base de données WID](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server). L’article couvre à la fois la mise à niveau de votre batterie de serveurs vers AD FS 2019 et la mise à niveau de votre FBL vers la version 4.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Configurer des règles de revendication pour appeler Azure AD MFA

Une fois que vous disposez d’Azure AD MFA en tant que méthode d’authentification supplémentaire, vous pouvez l’affecter à des groupes d’utilisateurs. Pour ce faire, configurez des règles de revendication, également appelées *approbations de partie de confiance*. À l’aide de groupes, vous pouvez déterminer quel est le fournisseur d’authentification appelé, globalement ou par application. Par exemple, vous pouvez appeler Azure AD MFA pour les utilisateurs ayant accès à l’inscription combinée des informations de sécurité, ou dont les numéros de téléphone ont été migrés, et vous pouvez appeler le serveur MFA pour les autres. 

>[!NOTE]
>Les règles de revendication nécessitent un groupe de sécurité local. 

#### <a name="back-up-existing-rules"></a>Sauvegarder les règles existantes 

Avant de configurer de nouvelles règles de revendication, sauvegardez vos règles existantes. Vous devrez les restaurer dans le cadre des étapes de nettoyage. 

Selon votre configuration, vous devrez peut-être également copier la règle existante et ajouter les nouvelles règles créées pour la migration.

Pour voir les règles globales existantes, exécutez :  
```powershell
Get-AdfsAdditionalAuthenticationRule
```

Pour voir les approbations de partie de confiance existantes, exécutez la commande suivante en remplaçant RPTrustName par le nom de la règle de revendication d’approbation de partie de confiance : 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

#### <a name="access-control-policies"></a>Stratégies de contrôle d’accès

>[!NOTE]
>Vous ne pouvez pas configurer les stratégies de contrôle d’accès pour appeler un fournisseur d’authentification spécifique en fonction de l’appartenance à un groupe. 

Pour passer de vos stratégies de contrôle d’accès à des règles d’authentification supplémentaires, exécutez la commande suivante pour chacune de vos approbations de partie de confiance en utilisant le fournisseur d’authentification basé sur le serveur MFA :

```powershell
Set-AdfsRelyingPartyTrust -**TargetName AppA -AccessControlPolicyName $Null**
```

Cette commande permet de déplacer la logique de votre stratégie de contrôle d’accès actuelle vers des règles d’authentification supplémentaires.

#### <a name="set-up-the-group-and-find-the-sid"></a>Configurer le groupe et rechercher le SID

Vous devez disposer d’un groupe spécifique dans lequel vous placez les utilisateurs pour lesquels vous souhaitez appeler Azure AD MFA. Vous aurez besoin de trouver le SID (identificateur de sécurité) de ce groupe.
Pour trouver le SID du groupe, utilisez la commande suivante avec le nom de groupe `Get-ADGroup “GroupName”`

![Commande PowerShell pour obtenir le SID du groupe.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Définition des règles de revendication pour l’appel d’Azure MFA

Les applets de commande PowerShell suivantes appellent Azure AD MFA pour les utilisateurs du groupe qui ne font pas partie du réseau d’entreprise. Vous devez remplacer « YourGroupSid » (VotreSidDeGroupe) par le SID trouvé en exécutant l’applet de commande précédente.

Veillez à consulter le [Guide pratique pour choisir des fournisseurs d’authentification supplémentaires dans la version 2019](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server#how-to-choose-additional-auth-providers-in-2019). 

>[!IMPORTANT] 
>Sauvegardez vos règles de revendication existantes avant de continuer.

##### <a name="set-global-claims-rule"></a>Définir la règle de revendication globale 

Exécutez la commande suivante en remplaçant RPTrustName par le nom de la règle de revendication d’approbation de partie de confiance : 

```powershell
(Get-AdfsRelyingPartyTrust -Name “RPTrustName”).AdditionalAuthenticationRules
```

La commande retourne vos règles d’authentification supplémentaires actuelles pour l’approbation de partie de confiance.  
Vous devez ajouter les règles suivantes à vos règles de revendication actuelles :

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

L’exemple suivant suppose que vos règles de revendication actuelles sont configurées pour demander une authentification MFA quand les utilisateurs se connectent depuis l’extérieur de votre réseau. Cet exemple inclut les règles supplémentaires que vous devez ajouter.

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

##### <a name="set-per-application-claims-rule"></a>Définir une règle de revendication par application

Cet exemple modifie les règles de revendication d’une approbation de partie de confiance spécifique (application). Il comprend les règles supplémentaires que vous devez ajouter.

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
“YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value==“YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");’
```

### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>Configurer Azure AD MFA en tant que fournisseur d’authentification dans AD FS

Pour configurer Azure AD MFA pour AD FS, vous devez configurer chaque serveur AD FS. Si vous avez plusieurs serveurs AD FS dans votre batterie, vous pouvez les configurer à distance à l’aide d’Azure AD PowerShell.

Pour obtenir des instructions pas à pas sur ce processus, consultez [Configurer les serveurs AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Une fois que vous avez configuré les serveurs, vous pouvez ajouter Azure AD MFA en tant que méthode d’authentification supplémentaire. 

![Capture d’écran montrant comment ajouter Azure AD MFA en tant que méthode d’authentification supplémentaire.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)


## <a name="prepare-staged-rollout"></a>Préparer le déploiement par étapes 

À présent, vous êtes prêt à activer la fonctionnalité de déploiement par étapes. Le déploiement par étapes vous permet de migrer de manière itérative vos utilisateurs vers l’authentification PHS (synchronisation de hachage du mot de passe) ou l’authentification PTA (authentification directe). 

* Veillez à passer en revue les [scénarios pris en charge](../hybrid/how-to-connect-staged-rollout.md#supported-scenarios). 
* Vous devez d’abord effectuer le [travail préliminaire pour l’authentification PHS](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) ou le [travail préliminaire pour l’authentification PTA](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication). Nous recommandons l’utilisation de l’authentification PHS. 
* Vous allez ensuite effectuer le [travail préliminaire pour une authentification SSO (authentification unique) transparente](../hybrid/how-to-connect-staged-rollout.md#pre-work-for-seamless-sso). 
* [Activer le déploiement par étapes de l’authentification cloud](../hybrid/how-to-connect-staged-rollout.md#enable-a-staged-rollout-of-a-specific-feature-on-your-tenant) pour la méthode d’authentification sélectionnée. 
* Ajoutez les groupes que vous avez créés pour le déploiement par étapes. Rappelez-vous que vous allez ajouter des utilisateurs aux groupes de manière itérative, et qu’il ne peut pas s’agir de groupes dynamiques ou de groupes imbriqués. 

## <a name="register-users-for-azure-mfa"></a>Inscrire des utilisateurs à Azure MFA

Il existe deux façons d’inscrire des utilisateurs à Azure MFA : 

* Effectuer l’inscription combinée des informations de sécurité (authentification MFA et réinitialisation de mot de passe en libre-service) 
* Migrer les numéros de téléphone à partir du serveur MFA

L’application Microsoft Authenticator peut être utilisée en tant que méthode de connexion sans mot de passe ainsi qu’en tant que deuxième facteur pour l’authentification MFA avec l’une ou l’autre de ces méthodes.

### <a name="register-for-combined-security-registration-recommended"></a>Effectuer l’inscription combinée des informations de sécurité (recommandé)

Nous vous recommandons d’inviter les utilisateurs à effectuer l’inscription combinée des informations de sécurité. Ils disposent ainsi d’un seul emplacement où inscrire leurs méthodes et appareils d’authentification pour MFA et SSPR. Bien qu’il soit possible de migrer des données du serveur MFA vers Azure AD MFA, vous devez prendre en compte les aspects suivants :

* Seuls les numéros de téléphone peuvent être migrés.
* Les applications d’authentification doivent être réinscrites.
* Les données périmées peuvent être migrées.

Microsoft propose des modèles de communication que vous pouvez fournir aux utilisateurs pour les guider tout au long du processus d’inscription combinée. Il s’agit notamment de modèles d’e-mails, d’affiches, de fiches cartonnées et de diverses autres ressources. Les utilisateurs inscrivent leurs informations sur `https://aka.ms/mysecurityinfo`, ce qui les amène à l’écran d’inscription combinée des informations de sécurité. 

Nous vous recommandons de [sécuriser le processus d’inscription des informations de sécurité par un accès conditionnel](../conditional-access/howto-conditional-access-policy-registration.md) qui oblige l’utilisateur à effectuer l’inscription à partir d’un appareil ou d’un emplacement approuvé. Pour plus d’informations sur le suivi des états d’inscription, consultez [Activité des méthodes d’authentification pour Azure Active Directory](howto-authentication-methods-activity.md).
> [!NOTE]
> Les utilisateurs qui DOIVENT effectuer l’inscription combinée de leurs informations de sécurité à partir d’un emplacement ou d’un appareil non approuvé peuvent se voir délivrer un passe d’accès temporaire, ou peuvent éventuellement être exclus temporairement de la stratégie.

### <a name="migrate-phone-numbers-from-mfa-server"></a>Migrer les numéros de téléphone à partir du serveur MFA

Bien que vous puissiez migrer les numéros de téléphone MFA et les jetons matériels des utilisateurs, vous ne pouvez pas migrer les inscriptions d’appareils, par exemple les paramètres d’application Microsoft Authenticator. La migration des numéros de téléphone peut entraîner la migration de numéros périmés et inciter les utilisateurs à conserver une authentification MFA par téléphone au lieu de configurer des méthodes plus sécurisées telles que la [connexion sans mot de passe avec l’application Microsoft Authenticator](howto-authentication-passwordless-phone.md). Nous vous recommandons donc d’inviter tous les utilisateurs à effectuer l’[inscription combinée des informations de sécurité](howto-registration-mfa-sspr-combined.md), quel que soit le chemin de migration que vous choisissez. L’inscription combinée des informations de sécurité permet aux utilisateurs de s’inscrire également à la réinitialisation de mot de passe en libre-service.

Si l’inscription combinée des informations de sécurité des utilisateurs n’est pas une option, il est possible d’exporter les utilisateurs et leurs numéros de téléphone à partir du serveur MFA et d’importer les numéros de téléphone dans Azure AD. 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>Exporter les numéros de téléphone des utilisateurs à partir du serveur MFA 

1. Ouvrez la console d’administration du serveur Multi-Factor Authentication sur le serveur MFA. 
1. Sélectionnez **Fichier** > **Exporter les utilisateurs**.
3)  Enregistrez le fichier CSV. Le nom par défaut est Utilisateurs Multi-Factor Authentication.csv.

#### <a name="interpret-and-format-the-csv-file"></a>Interpréter et mettre en forme le fichier .csv

Le fichier .csv contient un certain nombre de champs non nécessaires à la migration. Vous devez donc le modifier et le mettre en forme avant d’importer les numéros de téléphone dans Azure AD. 

Quand vous ouvrez le fichier .csv, les colonnes d’intérêt sont les suivantes : Nom d’utilisateur, Téléphone principal, Indicatif du pays principal, Indicatif de pays de sauvegarde, Téléphone de sauvegarde, Extension de sauvegarde. Vous devez interpréter ces données et les mettre en forme, le cas échéant.

#### <a name="tips-to-avoid-errors-during-import"></a>Astuces pour éviter les erreurs durant l’importation

* Vous devez modifier le fichier CSV avant d’utiliser l’API des méthodes d’authentification pour importer les numéros de téléphone dans Azure AD. 
* Nous recommandons de simplifier le fichier .csv en utilisant seulement trois colonnes : UPN, PhoneType et PhoneNumber. 

  ![Capture d’écran d’un exemple de fichier csv.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/csv-example.png)

* Vérifiez que le nom d’utilisateur de serveur MFA exporté correspond à Azure AD UserPrincipalName. Si ce n’est pas le cas, mettez à jour le nom d’utilisateur dans le fichier CSV pour qu’il corresponde à ce qui figure dans Azure AD, sinon l’utilisateur sera introuvable.

Les utilisateurs ont peut-être déjà inscrit des numéros de téléphone dans Azure AD. Quand vous importez des numéros de téléphone à l’aide de l’API des méthodes d’authentification, vous devez décider si vous souhaitez remplacer les numéros de téléphone existants, ou si vous préférez ajouter le numéro importé en tant que numéro de téléphone secondaire.

Les applets de commande PowerShell suivantes utilisent le fichier CSV que vous fournissez et ajoutent les numéros de téléphone exportés pour chaque UPN à l’aide de l’API des méthodes d’authentification. Remplacez « myPhones » par le nom de votre fichier CSV.


```powershell
$csv = import-csv myPhones.csv
$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 
```

Pour plus d’informations sur la gestion des méthodes d’authentification des utilisateurs, consultez [Gérer les méthodes d’authentification pour Azure AD Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

### <a name="add-users-to-the-appropriate-groups"></a>Ajouter des utilisateurs aux groupes appropriés 

* Si vous avez créé des stratégies d’accès conditionnel, ajoutez les utilisateurs appropriés à ces groupes. 
* Si vous avez créé des groupes de sécurité locaux pour les règles de revendication, ajoutez les utilisateurs appropriés à ces groupes. 
* Une fois que vous avez ajouté les utilisateurs aux règles d’accès conditionnel appropriées, ajoutez-les au groupe que vous avez créé pour le déploiement par étapes. Une fois l’opération effectuée, les utilisateurs se voient appliquer la méthode d’authentification Azure que vous avez sélectionnée (PHS ou PTA) et Azure AD MFA quand ils doivent effectuer l’authentification multifacteur.

> [!IMPORTANT] 
> Les groupes imbriqués et dynamiques ne sont pas pris en charge dans le processus de déploiement par étapes. N’utilisez pas ces types de groupe. 

Nous vous déconseillons de réutiliser les groupes utilisés pour la sécurité. Ainsi, si vous utilisez un groupe de sécurité pour sécuriser un groupe d’applications très important via une stratégie d’accès conditionnel, il s’agit de la seule utilisation de ce groupe.

## <a name="monitoring"></a>Surveillance

Un certain nombre de [classeurs Azure Monitor](../reports-monitoring/howto-use-azure-monitor-workbooks.md) et de rapports d’utilisation et d’insights sont disponibles pour vous permettre de superviser le déploiement. Vous les trouverez dans Azure AD, dans le volet de navigation sous **Supervision**. 

### <a name="monitoring-staged-rollout"></a>Supervision du déploiement par étapes

Dans la section [classeurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Workbooks), sélectionnez **Modèles publics**. Sous la section **Authentification hybride**, sélectionnez le classeur **Groupes, utilisateurs et connexions dans le déploiement par étapes**.

Vous pouvez utiliser ce classeur pour superviser ce qui suit : 
* Utilisateurs et groupes ajoutés au déploiement par étapes.
* Utilisateurs et groupes supprimés du déploiement par étapes.
* Échecs de connexion des utilisateurs dans le déploiement par étapes, et raisons de ces échecs.

### <a name="monitoring-azure-mfa-registration"></a>Supervision de l’inscription à Azure MFA
Vous pouvez superviser l’inscription à Azure MFA en utilisant le [rapport d’utilisation et d’insights sur les méthodes d’authentification](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AuthMethodsActivity/menuId/AuthMethodsActivity). Ce rapport se trouve dans Azure AD. Sélectionnez **Supervision**, puis **Utilisation et insights**. 

![Capture d’écran montrant comment trouver le rapport d’utilisation et d’insights.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/usage-report.png)

Dans Utilisation et insights, sélectionnez **Méthodes d’authentification**. 

Vous trouverez des informations détaillées sur l’inscription à Azure MFA sous l’onglet Inscription. Vous pouvez voir la liste des utilisateurs inscrits en sélectionnant le lien hypertexte **Users registered for Azure multi-factor authentication** (Utilisateurs inscrits pour Azure Multi-Factor Authentication).

![Capture d’écran de l’onglet Inscription.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/registration-tab.png)

### <a name="monitoring-app-sign-in-health"></a>Supervision de l’intégrité des connexions aux applications

Supervisez les applications que vous avez déplacées vers Azure AD à l’aide du classeur d’intégrité des connexions aux applications ou du rapport d’utilisation relatif à l’activité des applications.

* **Classeur d’intégrité des connexions aux applications**. Pour obtenir des conseils d’aide sur l’utilisation de ce classeur, consultez [Supervision de l’intégrité de connexion de l’application pour la résilience](../fundamentals/monitor-sign-in-health-for-resilience.md).
* **Rapport d’utilisation relatif à l’activité des applications Azure AD**. Ce [rapport](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsageAndInsightsMenuBlade/Azure%20AD%20application%20activity) permet d’afficher les connexions réussies et non réussies pour des applications individuelles. Il permet également d’explorer et d’afficher l’activité relative aux connexions pour une application spécifique. 

## <a name="clean-up-tasks"></a>Tâches de nettoyage

Une fois que vous avez migré tous les utilisateurs vers l’authentification cloud Azure AD et Azure MFA, vous devez être prêt à désactiver votre serveur MFA. Nous vous recommandons de passer en revue les journaux du serveur MFA pour vérifier qu’aucun utilisateur ou aucune application n’utilise le serveur avant sa suppression.

### <a name="convert-your-domains-to-managed-authentication"></a>Convertir vos domaines en authentification managée

Vous devez à présent [convertir vos domaines fédérés dans Azure AD en domaines managés](../hybrid/migrate-from-federation-to-cloud-authentication.md#convert-domains-from-federated-to-managed) et supprimer la configuration de déploiement par étapes. Cela permet de garantir l’authentification cloud des nouveaux utilisateurs sans qu’ils soient ajoutés aux groupes de migration.

### <a name="revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider"></a>Restaurer les règles de revendication sur AD FS et supprimer le fournisseur d’authentification basé sur le serveur MFA

Suivez les étapes décrites sous [Configurer des règles de revendication pour appeler Azure AD MFA](#configure-claims-rules-to-invoke-azure-ad-mfa) afin de restaurer les règles de revendication sauvegardées et de supprimer les règles de revendication AzureMFAServerAuthentication. 

Par exemple, supprimez les éléments suivants des règles : 

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value ==
“**YourGroupSID**"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders",
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");’
```


### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>Désactiver le serveur MFA en tant que fournisseur d’authentification dans AD FS

Ce changement permet de garantir que seul Azure MFA est utilisé en tant que fournisseur d’authentification.

1. Ouvrez la **Console de gestion AD FS**.
1. Sous **Services**, cliquez avec le bouton droit sur **Méthodes d’authentification**, puis sélectionnez **Éditer les méthodes d’authentification multifacteur**. 
1. Décochez la case **Azure Multi-Factor Authentication Server** (Serveur Azure Multi-Factor Authentication). 

### <a name="decommission-the-mfa-server"></a>Désactiver le serveur MFA

Suivez le processus de désactivation de votre serveur d’entreprise pour supprimer les serveurs MFA de votre environnement.

Considérations éventuelles à prendre en compte au moment de la désactivation du serveur MFA : 

* Nous vous recommandons de passer en revue les journaux du serveur MFA pour vérifier qu’aucun utilisateur ou aucune application n’utilise le serveur avant sa suppression.
* Désinstallez Serveur Multi-Factor Authentication à partir du Panneau de configuration du serveur.
* Vous pouvez éventuellement nettoyer les journaux et les répertoires de données restants après les avoir sauvegardés au préalable. 
* Désinstallez le kit SDK Multi-Factor Authentication Web Server, le cas échéant, notamment les fichiers restants situés dans les répertoires inetpub\wwwroot\MultiFactorAuthWebServiceSdk et/ou MultiFactorAuth.
* Pour les versions antérieures à la version 8.0.x du serveur MFA, vous devrez peut-être également supprimer le service web Multi-Factor Auth Phone App.

## <a name="move-application-authentication-to-azure-active-directory"></a>Migrer l’authentification des applications vers Azure Active Directory

Si vous migrez l’authentification des applications en totalité ainsi que l’authentification MFA et l’authentification utilisateur, vous pourrez supprimer des parties importantes de votre infrastructure locale, ce qui entraînera une réduction des coûts et des risques. Si vous migrez l’authentification des applications en totalité, vous pouvez ignorer la phase [Préparer AD FS](#prepare-ad-fs) et simplifier la migration MFA.

Le processus de déplacement de l’authentification des applications dans sa totalité est illustré dans le diagramme suivant.

![Processus de migration des applications vers Azure AD MFA.](media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/mfa-app-migration-flow.png)

S’il est impossible de déplacer toutes vos applications avant la migration, déplacez autant d’applications que possible avant de commencer.
Pour plus d’informations sur la migration d’applications vers Azure, consultez [Ressources pour la migration d’applications vers Azure Active Directory](../manage-apps/migration-resources.md).

## <a name="next-steps"></a>Étapes suivantes

- [Migrer du serveur Microsoft MFA vers Azure Multi-Factor Authentication (vue d’ensemble)](how-to-migrate-mfa-server-to-azure-mfa.md)
- [Migrer des applications de Windows Active Directory vers Azure Active Directory](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)
- [Planifier votre stratégie d’authentification cloud](../fundamentals/active-directory-deployment-plans.md)