---
title: Migrer vers Azure AD MFA avec fédération - Azure Active Directory
description: Conseils d’aide pas à pas pour passer du Serveur Azure MFA local à Azure AD MFA avec fédération
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c5d92d16f5ac9fcd8aa69ce9fd71f4844a77d28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352704"
---
# <a name="migrate-to-azure-ad-mfa-with-federation"></a>Migrer vers Azure AD MFA avec fédération

Le déplacement de votre solution MFA (authentification multifacteur) vers Azure AD (Azure Active Directory) est une première étape importante dans votre parcours vers le cloud. Pensez également à passer plus tard à Azure AD pour l’authentification utilisateur. Pour plus d’informations, consultez le processus de migration vers Azure AD MFA avec authentification cloud.

Pour migrer vers Azure AD MFA avec fédération, le fournisseur d’authentification Azure MFA est installé sur AD FS. L’approbation de partie de confiance Azure AD et les autres approbations de partie de confiance sont configurées pour utiliser Azure MFA pour les utilisateurs migrés.

Le diagramme suivant illustre ce processus de migration.

![Organigramme montrant les étapes du processus. Celles-ci correspondent aux titres de section de ce document, et se présentent dans le même ordre](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/mfa-federation-flow.png)

## <a name="create-migration-groups"></a>Créer des groupes de migration

Pour créer des stratégies d’accès conditionnel, vous devez affecter ces stratégies à des groupes. Vous pouvez utiliser des groupes de sécurité Azure AD existants ou des groupes Microsoft 365 à cet effet. Vous pouvez également en créer ou en synchroniser de nouveaux.

Vous aurez également besoin d’un groupe de sécurité Azure AD pour migrer de manière itérative les utilisateurs vers Azure AD MFA. Ces groupes sont utilisés dans vos règles de revendication.

Ne réutilisez pas de groupes utilisés pour la sécurité. Si vous utilisez un groupe de sécurité pour sécuriser un groupe d’applications très important via une stratégie d’accès conditionnel, il s’agit de la seule utilisation de ce groupe.

## <a name="prepare-ad-fs"></a>Préparer AD FS

### <a name="upgrade-ad-fs-server-farm-to-2019-fbl-4"></a>Mettre à niveau la batterie de serveurs AD FS vers FBL 4 2019

Dans AD FS 2019, vous pouvez spécifier des méthodes d’authentification supplémentaires pour une partie de confiance, par exemple une application. Vous utilisez l’appartenance à un groupe pour déterminer le fournisseur d’authentification. En spécifiant une méthode d’authentification supplémentaire, vous pouvez effectuer une transition vers Azure AD MFA tout en conservant les autres méthodes d’authentification pendant la transition. Pour plus d’informations, consultez [Mise à niveau vers AD FS dans Windows Server 2016 à l’aide d’une base de données WID](/windows-server/identity/ad-fs/deployment/upgrading-to-ad-fs-in-windows-server). L’article couvre à la fois la mise à niveau de votre batterie de serveurs vers AD FS 2019 et la mise à niveau de votre FBL vers la version 4.

### <a name="configure-claims-rules-to-invoke-azure-ad-mfa"></a>Configurer des règles de revendication pour appeler Azure AD MFA

Dans la mesure où Azure AD MFA est une méthode d’authentification supplémentaire, vous pouvez l’affecter à des groupes d’utilisateurs. Pour ce faire, vous devez configurer des règles de revendication, également appelées approbations de partie de confiance. À l’aide de groupes, vous pouvez déterminer quel est le fournisseur d’authentification appelé, globalement ou par application. Par exemple, vous pouvez appeler Azure AD MFA pour les utilisateurs ayant accès à l’inscription combinée des informations de sécurité, ou dont les numéros de téléphone ont été migrés, et vous pouvez appeler le serveur MFA pour les autres.

> [!NOTE]
> Les règles de revendication nécessitent un groupe de sécurité local. Avant d’apporter des changements aux règles de revendication, sauvegardez-les.


#### <a name="back-up-existing-rules"></a>Sauvegarder les règles existantes

Avant de configurer de nouvelles règles de revendication, sauvegardez vos règles existantes. Vous devez restaurer ces règles dans le cadre des étapes de nettoyage. 

Selon votre configuration, vous devrez peut-être également copier la règle existante et ajouter les nouvelles règles créées pour la migration.  

Pour voir les règles globales existantes, exécutez :  

```powershell
Get-AdfsAdditionalAuthenticationRule
```

Pour voir les approbations de partie de confiance existantes, exécutez la commande suivante en remplaçant RPTrustName par le nom de la règle de revendication d’approbation de partie de confiance : 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules 
```

#### <a name="access-control-policies"></a>Stratégies de contrôle d’accès

> [!NOTE]
> Vous ne pouvez pas configurer les stratégies de contrôle d’accès pour appeler un fournisseur d’authentification spécifique en fonction de l’appartenance à un groupe. 

 
Pour passer des stratégies de contrôle d’accès à des règles d’authentification supplémentaires, exécutez la commande suivante pour chacune de vos approbations de partie de confiance en utilisant le fournisseur d’authentification basé sur le serveur MFA :


```powershell
Set-AdfsRelyingPartyTrust -TargetName AppA -AccessControlPolicyName $Null
```

 

Cette commande permet de déplacer la logique de votre stratégie de contrôle d’accès actuelle vers des règles d’authentification supplémentaires.


#### <a name="set-up-the-group-and-find-the-sid"></a>Configurer le groupe et rechercher le SID

Vous devez disposer d’un groupe spécifique dans lequel vous placez les utilisateurs pour lesquels vous souhaitez appeler Azure AD MFA. Vous aurez besoin du SID (identificateur de sécurité) de ce groupe.

Pour trouver le SID du groupe, utilisez la commande suivante avec le nom de groupe

`Get-ADGroup "GroupName"`

![Image d’une capture d’écran montrant les résultats du script Get-ADGroup.](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/find-the-sid.png)

#### <a name="setting-the-claims-rules-to-call-azure-mfa"></a>Définition des règles de revendication pour l’appel d’Azure MFA

Les applets de commande PowerShell suivantes appellent Azure AD MFA pour les utilisateurs du groupe qui ne font pas partie du réseau d’entreprise. Remplacez « YourGroupSid » par le SID trouvé en exécutant la cmdlet ci-dessus.

Veillez à consulter le [Guide pratique pour choisir des fournisseurs d’authentification supplémentaires dans la version 2019](/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). 

 > [!IMPORTANT]
> Sauvegarder vos règles de revendication existantes

 

#### <a name="set-global-claims-rule"></a>Définir la règle de revendication globale 

Exécutez l’applet de commande PowerShell suivant : 

```powershell
(Get-AdfsRelyingPartyTrust -Name "RPTrustName").AdditionalAuthenticationRules
```

 

La commande retourne vos règles d’authentification supplémentaires actuelles pour l’approbation de partie de confiance. Ajoutez les règles suivantes à vos règles de revendication actuelles :

```console
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```

L’exemple suivant suppose que vos règles de revendication actuelles sont configurées pour demander une authentification MFA quand les utilisateurs se connectent depuis l’extérieur de votre réseau. Cet exemple inclut les règles supplémentaires que vous devez ajouter.

```PowerShell
Set-AdfsAdditionalAuthenticationRule -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
 c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


#### <a name="set-per-application-claims-rule"></a>Définir une règle de revendication par application

Cet exemple modifie les règles de revendication d’une approbation de partie de confiance spécifique (application), et inclut les informations à ajouter.

```PowerShell
Set-AdfsRelyingPartyTrust -TargetName AppA -AdditionalAuthenticationRules 'c:[type == 
"https://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", value == "false"] => issue(type = 
"https://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod", value = 
"https://schemas.microsoft.com/claims/multipleauthn" );
c:[Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value == 
"YourGroupSID"] => issue(Type = "https://schemas.microsoft.com/claims/authnmethodsproviders", 
Value = "AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
Value=="YourGroupSid"]) => issue(Type = 
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value = 
"AzureMfaServerAuthentication");'
```


### <a name="configure-azure-ad-mfa-as-an-authentication-provider-in-ad-fs"></a>Configurer Azure AD MFA en tant que fournisseur d’authentification dans AD FS

Pour configurer Azure AD MFA pour AD FS, vous devez configurer chaque serveur AD FS. Si vous avez plusieurs serveurs AD FS dans votre batterie, vous pouvez les configurer à distance à l’aide d’Azure AD PowerShell.

Pour obtenir des instructions pas à pas sur ce processus, consultez [Configurer les serveurs AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa) dans l’article [Configurer Azure AD MFA en tant que fournisseur d’authentification avec AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa).

Une fois que vous avez configuré les serveurs, vous pouvez ajouter Azure AD MFA en tant que méthode d’authentification supplémentaire. 

![Capture d’écran montrant l’écran Éditer les méthodes d’authentification ainsi que les cases Azure MFA et Azure Multi-factor Authentication Server (Serveur Azure Multi-Factor Authentication) cochées](./media/how-to-migrate-mfa-server-to-azure-mfa-user-authentication/edit-authentication-methods.png)

## <a name="prepare-azure-ad-and-implement"></a>Préparer et implémenter Azure AD

### <a name="ensure-supportsmfa-is-set-to-true"></a>Vérifier que SupportsMFA a la valeur True

Pour les domaines fédérés, l’authentification MFA peut être appliquée par l’accès conditionnel Azure AD ou par le fournisseur de fédération local. Chaque domaine fédéré dans Azure AD a un indicateur SupportsMFA. Quand l’indicateur SupportsMFA a la valeur True, Azure AD redirige les utilisateurs vers l’authentification MFA sur AD FS ou un autre fournisseur de fédération. Par exemple, si un utilisateur accède à une application pour laquelle une stratégie d’accès conditionnel nécessitant une authentification MFA a été configurée, l’utilisateur est redirigé vers AD FS. L’ajout d’Azure AD MFA en tant que méthode d’authentification à AD FS permet d’appeler Azure AD MFA, une fois vos configurations effectuées.

Si l’indicateur SupportsMFA a la valeur False, cela signifie probablement que vous n’utilisez pas Azure MFA. Vous utilisez peut-être des règles de revendication sur les parties de confiance AD FS pour appeler l’authentification MFA.

Vous pouvez vérifier l’état de l’indicateur SupportsMFA à l’aide de l’[applet de commande Windows PowerShell](/powershell/module/msonline/get-msoldomainfederationsettings) suivante :

```powershell
Get-MsolDomainFederationSettings –DomainName yourdomain.com
```

Si l’indicateur SupportsMFA a la valeur false ou s’il est vide pour votre domaine fédéré, affectez-lui la valeur true à l’aide de l’applet de commande Windows PowerShell suivante :

```powershell
Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true
```

Cette configuration vous permet de décider d’utiliser le serveur MFA ou Azure MFA sur AD FS.

### <a name="configure-conditional-access-policies-if-needed"></a>Configurer les stratégies d’accès conditionnel si nécessaire

Si vous utilisez l’accès conditionnel pour déterminer le moment où l’authentification MFA est proposée aux utilisateurs, vous ne devez pas avoir à changer vos stratégies.

Si SupportsMFA a la valeur false pour vos domaines fédérés, analysez vos règles de revendication sur l’approbation de partie de confiance Azure AD, et créez des stratégies d’accès conditionnel qui prennent en charge les mêmes objectifs de sécurité.

Une fois que vous avez créé des stratégies d’accès conditionnel pour appliquer les mêmes contrôles qu’AD FS, vous pouvez sauvegarder et supprimer vos personnalisations de règles de revendication sur la partie de confiance Azure AD.

Pour plus d’informations, consultez les ressources suivantes :

* [Planifier un déploiement d’accès conditionnel](../conditional-access/plan-conditional-access.md)

* [Stratégies d’accès conditionnel courantes](../conditional-access/concept-conditional-access-policy-common.md)


## <a name="register-users-for-azure-mfa"></a>Inscrire des utilisateurs à Azure MFA

Il existe deux façons d’inscrire des utilisateurs à Azure MFA : 

* Effectuer l’inscription combinée des informations de sécurité (authentification MFA et réinitialisation de mot de passe en libre-service) 

* Migrer les numéros de téléphone à partir du serveur MFA

L’application Microsoft Authenticator peut être utilisée en mode sans mot de passe. Elle peut également être utilisée en tant que second facteur pour l’authentification MFA avec l’une ou l’autre des méthodes d’inscription.

### <a name="register-for-combined-security-registration-recommended"></a>Effectuer l’inscription combinée des informations de sécurité (recommandé)

Invitez les utilisateurs à effectuer l’inscription combinée des informations de sécurité. Ils disposent ainsi d’un seul emplacement où inscrire leurs méthodes et appareils d’authentification pour MFA et SSPR. Bien qu’il soit possible de migrer des données du serveur MFA vers Azure AD MFA, vous devez prendre en compte les aspects suivants :

* Seuls les numéros de téléphone peuvent être migrés.

* Les applications d’authentification doivent être réinscrites.

* Les données périmées peuvent être migrées.

Microsoft propose des modèles de communication que vous pouvez fournir aux utilisateurs pour les guider tout au long du processus d’inscription combinée. Il s’agit notamment de modèles d’e-mails, d’affiches, de fiches cartonnées et d’autres ressources. Les utilisateurs inscrivent leurs informations sur [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo), ce qui les amène à l’écran d’inscription combinée des informations de sécurité. 

Nous vous recommandons de [sécuriser le processus d’inscription des informations de sécurité par un accès conditionnel](../conditional-access/howto-conditional-access-policy-registration.md) qui oblige l’utilisateur à effectuer l’inscription à partir d’un appareil ou d’un emplacement approuvé.

> [!NOTE]
> Si des utilisateurs DOIVENT effectuer l’inscription combinée de leurs informations de sécurité à partir d’un emplacement ou d’un appareil non approuvé, vous pouvez leur délivrer un passe d’accès temporaire ou les exclure temporairement de la stratégie.

### <a name="migrate-phone-numbers-from-mfa-server"></a>Migrer les numéros de téléphone à partir du serveur MFA

La migration des numéros de téléphone peut entraîner la migration de numéros périmés et inciter les utilisateurs à conserver une authentification MFA par téléphone au lieu de configurer des méthodes plus sécurisées telles que la [connexion sans mot de passe avec l’application Microsoft Authenticator](howto-authentication-passwordless-phone.md). Vous ne pouvez pas migrer des inscriptions d’appareils, par exemple les paramètres d’application de Microsoft Authenticator. Nous vous recommandons d’inviter tous les utilisateurs à effectuer l’[inscription combinée des informations de sécurité](howto-registration-mfa-sspr-combined.md). L’inscription combinée des informations de sécurité des utilisateurs entraîne également l’inscription à la réinitialisation de mot de passe en libre-service.

Si l’inscription combinée des informations de sécurité des utilisateurs n’est absolument pas une option, il est possible d’exporter les utilisateurs et leurs numéros de téléphone à partir du serveur MFA et d’importer les numéros de téléphone dans Azure AD.
 

#### <a name="export-user-phone-numbers-from-mfa-server"></a>Exporter les numéros de téléphone des utilisateurs à partir du serveur MFA 

1. Ouvrez la console d’administration du serveur Multi-Factor Authentication sur le serveur MFA. 

1. Sélectionnez **Fichier**, puis **Exporter les utilisateurs**.

1. **Enregistrez** le fichier CSV. Le nom par défaut est Utilisateurs Multi-Factor Authentication.csv

#### <a name="interpret-and-format-the-csv--file"></a>Interpréter et mettre en forme le fichier .csv

Le fichier .csv contient de nombreux champs non nécessaires à la migration. Modifiez-le et mettez-le en forme avant d’importer les numéros de téléphone dans Azure AD. 

Quand vous ouvrez le fichier .csv, les colonnes d’intérêt sont les suivantes :

* Nom d’utilisateur
* Téléphone principal
* Indicatif du pays principal
* Indicatif de pays de sauvegarde
* Téléphone de secours
* Extension de sauvegarde

Vous devez interpréter, nettoyer et mettre en forme les données.

#### <a name="tips-to-avoid-errors-during-import"></a>Astuces pour éviter les erreurs durant l’importation

* Modifiez le fichier CSV avant d’utiliser l’API des méthodes d’authentification pour importer les numéros de téléphone dans Azure AD. 

* Simplifiez le fichier .csv en utilisant seulement trois colonnes : UPN, PhoneType et PhoneNumber. 

* Vérifiez que le nom d’utilisateur de serveur MFA exporté correspond à Azure AD UserPrincipalName. Si ce n’est pas le cas, mettez à jour le nom d’utilisateur dans le fichier CSV pour qu’il corresponde à ce qui figure dans Azure AD, sinon l’utilisateur sera introuvable.

Les utilisateurs ont peut-être déjà inscrit des numéros de téléphone dans Azure AD. Quand vous importez des numéros de téléphone à l’aide de l’API des méthodes d’authentification, vous devez décider si vous souhaitez remplacer les numéros de téléphone existants, ou si vous préférez ajouter le numéro importé en tant que numéro de téléphone secondaire.

Les applets de commande PowerShell suivantes utilisent le fichier CSV que vous fournissez et ajoutent les numéros de téléphone exportés pour chaque UPN à l’aide de l’API des méthodes d’authentification. Remplacez « myPhones » par le nom de votre fichier CSV.

```powershell

$csv = import-csv myPhones.csv

$csv|% { New-MgUserAuthenticationPhoneMethod -UserId $_.UPN -phoneType $_.PhoneType -phoneNumber $_.PhoneNumber} 

```

### <a name="add-users-to-the-appropriate-groups"></a>Ajouter des utilisateurs aux groupes appropriés

* Si vous avez créé des stratégies d’accès conditionnel, ajoutez les utilisateurs appropriés à ces groupes. 

* Si vous avez créé des groupes de sécurité locaux pour les règles de revendication, ajoutez les utilisateurs appropriés à ces groupes.

Nous vous déconseillons de réutiliser les groupes utilisés pour la sécurité. Ainsi, si vous utilisez un groupe de sécurité pour sécuriser un groupe d’applications très important via une stratégie d’accès conditionnel, il s’agit de la seule utilisation de ce groupe.

## <a name="monitoring"></a>Surveillance

Vous pouvez superviser l’inscription à Azure MFA en utilisant le [rapport d’utilisation et d’insights sur les méthodes d’authentification](https://portal.azure.com/). Ce rapport se trouve dans Azure AD. Sélectionnez **Supervision**, puis **Utilisation et insights**. 

Dans Utilisation et insights, sélectionnez **Méthodes d’authentification**. 

Vous trouverez des informations détaillées sur l’inscription à Azure MFA sous l’onglet Inscription. Vous pouvez voir la liste des utilisateurs inscrits en sélectionnant le lien hypertexte **Utilisateurs prenant en charge Azure Multi-Factor Authentication**.

![Image de l’écran d’activité des méthodes d’authentification montrant les inscriptions des utilisateurs à MFA](./media/how-to-migrate-mfa-server-to-azure-mfa-with-federation/authentication-methods.png)

   

## <a name="clean-up-steps&quot;></a>Étapes de nettoyage

Une fois que vous avez effectué la migration vers Azure MFA et que vous êtes prêt à désactiver le serveur MFA, effectuez les trois opérations suivantes : 

1. Restaurez les règles de revendication sur AD FS à leur configuration de prémigration, puis supprimez le fournisseur d’authentification basé sur le serveur MFA.

1. Supprimez le serveur MFA en tant que fournisseur d’authentification dans AD FS. Cela permet de garantir que tous les utilisateurs se servent d’Azure MFA, car il s’agit de la seule méthode d’authentification supplémentaire activée. 

1. Désactivez le serveur MFA.

### <a name=&quot;revert-claims-rules-on-ad-fs-and-remove-mfa-server-authentication-provider&quot;></a>Restaurer les règles de revendication sur AD FS et supprimer le fournisseur d’authentification basé sur le serveur MFA

Suivez les étapes décrites sous Configurer des règles de revendication pour appeler Azure AD MFA afin de restaurer les règles de revendication sauvegardées et de supprimer les règles de revendication AzureMFAServerAuthentication. 

Par exemple, supprimez les éléments suivants des règles : 

 
```console
c:[Type == &quot;https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid&quot;, Value ==
&quot;**YourGroupSID**&quot;] => issue(Type = &quot;https://schemas.microsoft.com/claims/authnmethodsproviders&quot;,
Value = &quot;AzureMfaAuthentication");
not exists([Type == "https://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
Value=="YourGroupSid"]) => issue(Type =
"https://schemas.microsoft.com/claims/authnmethodsproviders", Value =
"AzureMfaServerAuthentication");'
```

### <a name="disable-mfa-server-as-an-authentication-provider-in-ad-fs"></a>Désactiver le serveur MFA en tant que fournisseur d’authentification dans AD FS

Ce changement permet de garantir que seul Azure MFA est utilisé en tant que fournisseur d’authentification.

1. Ouvrez la **Console de gestion AD FS**.

1. Sous **Services**, cliquez avec le bouton droit sur **Méthodes d’authentification**, puis sélectionnez **Éditer les méthodes d’authentification multifacteur**. 

1. Décochez la case en regard de **Azure Multi-Factor Authentication Server** (Serveur Azure Multi-Factor Authentication). 

### <a name="decommission-the-mfa-server"></a>Désactiver le serveur MFA

Suivez le processus de désactivation de votre serveur d’entreprise pour supprimer les serveurs MFA de votre environnement.

Considérations éventuelles à prendre en compte au moment de la désactivation des serveurs MFA : 

* Passez en revue les journaux du serveur MFA pour vérifier qu’aucun utilisateur ou aucune application n’utilise le serveur avant sa suppression.

* Désinstallez Serveur Multi-Factor Authentication à partir du Panneau de configuration du serveur

* Vous pouvez éventuellement nettoyer les journaux et les répertoires de données restants après les avoir sauvegardés au préalable. 

* Désinstallez le kit SDK Multi-Factor Authentication Web Server, le cas échéant, notamment les fichiers restants situés dans les répertoires inetpub\wwwroot\MultiFactorAuthWebServiceSdk et/ou MultiFactorAuth

* Pour les versions du serveur MFA antérieures à la version 8.0, vous devrez peut-être également supprimer le service web Multi-Factor Auth Phone App

## <a name="next-steps"></a>Étapes suivantes

- [Déployer la synchronisation de hachage de mot de passe](../hybrid/whatis-phs.md)
- [En savoir plus sur l’accès conditionnel](../conditional-access/overview.md)
- [Migrer des applications vers Azure AD](../manage-apps/migrate-application-authentication-to-azure-active-directory.md)

 

 

 
