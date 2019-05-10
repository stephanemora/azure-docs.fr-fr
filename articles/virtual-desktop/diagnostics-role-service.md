---
title: Identifier les problèmes avec la fonctionnalité de diagnostics Windows Virtual Desktop Preview - Azure
description: Décrit la fonctionnalité de diagnostics Windows Virtual Desktop Preview et comment l’utiliser.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 747e177b0fbbfb9049959c3194ee39c3234bba50
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234033"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Identifier les problèmes relatifs à la fonctionnalité de diagnostic

Version préliminaire de Windows Virtual Desktop offre une fonctionnalité de diagnostic qui permet à l’administrateur identifier les problèmes via une interface unique. Les rôles de bureau virtuel Windows connecter à une activité de diagnostic chaque fois qu’un utilisateur interagit avec le système. Chaque journal contient des informations pertinentes telles que les rôles de bureau virtuel Windows impliquées dans la transaction, messages d’erreur, informations de locataire et les informations utilisateur. Activités de diagnostic sont créées par l’utilisateur final et les actions d’administration et peuvent être classées en trois catégories principales :

* Flux d’activités de l’abonnement : l’utilisateur final déclenche ces activités chaque fois qu’ils tentent de se connecter à leur flux via des applications de bureau à distance Microsoft.
* Activités de connexion : l’utilisateur final déclenche ces activités chaque fois qu’ils tentent de se connecter à un bureau ou à RemoteApp via des applications de bureau à distance Microsoft.
* Activités de gestion : l’administrateur déclenche ces activités chaque fois qu’ils effectuent des opérations de gestion sur le système, telles que la création de pools d’hôte, l’affectation d’utilisateurs à des groupes d’applications et la création des attributions de rôles.
  
Les connexions qui n’atteignent pas bureau virtuel Windows n’apparaîtront pas dans les résultats de diagnostic, car le service de rôle de diagnostics lui-même fait partie du bureau virtuel Windows. Problèmes de connexion de bureau virtuel Windows peuvent se produire lorsque l’utilisateur final rencontre des problèmes de connectivité réseau.

Prise en main, [télécharger et importer le module PowerShell de bureau virtuel Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell si vous n’avez pas déjà.

## <a name="diagnose-issues-with-powershell"></a>Diagnostiquer les problèmes avec PowerShell

Diagnostics de bureau virtuel Windows utilise qu’une applet de commande PowerShell, mais contient de nombreux paramètres facultatifs pour aider à isoler et isoler les problèmes. Les sections suivantes répertorient les applets de commande que vous pouvez exécuter pour diagnostiquer les problèmes. La plupart des filtres peuvent être appliqués ensemble. Les valeurs figurant entre crochets, comme `<tenantName>`, doit être remplacé par les valeurs qui s’appliquent à votre situation.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>Récupérer les activités de diagnostic dans votre client

Vous pouvez récupérer les activités de diagnostic en entrant le **Get-RdsDiagnosticActivities** applet de commande. L’applet de commande d’exemple suivante retournera une liste des activités de diagnostic, triés du plus au moins récente.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

Comme les autres applets de commande PowerShell de bureau virtuel de Windows, vous devez utiliser le **- TenantName** paramètre pour spécifier le nom du client que vous souhaitez utiliser pour votre requête. Le nom du locataire est applicable pour presque toutes les requêtes d’activité de diagnostic.

### <a name="retrieve-detailed-diagnostic-activities"></a>Récupérer les activités de diagnostics détaillées

Le **-détaillées** paramètre fournit des détails supplémentaires pour chaque activité de diagnostic retournée. Le format pour chaque activité varie en fonction de son type d’activité. Le **-détaillées** paramètre peut être ajouté à aucun **Get-RdsDiagnosticActivities** interroger, comme illustré dans l’exemple suivant.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Récupérer un ID par une activité de diagnostic spécifique

Le **- ActivityId** paramètre retourne une valeur spécifique à une activité de diagnostic si elle existe, comme indiqué dans l’applet de commande suivante de l’exemple.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>Filtrer les activités de diagnostic par utilisateur

Le **- UserName** paramètre retourne une liste des activités de diagnostic initié par l’utilisateur spécifié, comme indiqué dans l’applet de commande suivante de l’exemple.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Le **- UserName** paramètre peut également être combiné avec d’autres paramètres de filtrage facultatifs.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrer les activités de diagnostic par heure

Vous pouvez filtrer la liste de l’activité de diagnostic retournée avec la **- StartTime** et **- EndTime** paramètres. Le **- StartTime** paramètre retourne une liste de l’activité de diagnostic à partir d’une date spécifique, comme indiqué dans l’exemple suivant.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

Le **- EndTime** paramètre peut être ajouté à une applet de commande avec le **- StartTime** paramètre pour spécifier une période spécifique pendant laquelle vous voulez recevoir les résultats. L’applet de commande d’exemple suivant retourne une liste des activités de diagnostic entre le 1er août et le 10 août.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Le **- StartTime** et **- EndTime** paramètres peuvent également être combinées avec d’autres paramètres de filtrage facultatifs.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Activités de diagnostic de filtre par type d’activité

Vous pouvez également filtrer les activités de diagnostic par type d’activité avec la **- ActivityType** paramètre. L’applet de commande suivante renvoie une liste de connexions de l’utilisateur final :

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

L’applet de commande suivante renvoie une liste des tâches de gestion d’administrateur :

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Le **Get-RdsDiagnosticActivities** applet de commande ne prend actuellement en charge la spécification des flux en tant que le type d’activité.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtrer les activités de diagnostic par résultat

Vous pouvez filtrer la liste de l’activité de diagnostic retournée par le résultat avec la **-résultat** paramètre. L’applet de commande d’exemple suivant retourne une liste des activités de diagnostics réussites.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

L’applet de commande d’exemple suivant retourne une liste des activités de Diagnostics ayant échoué.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Le **-résultat** paramètre peut également être combiné avec d’autres paramètres de filtrage facultatifs.

## <a name="common-error-scenarios"></a>Scénarios d’erreur courants

Scénarios d’erreur sont classés comme internes au service et externes au bureau virtuel Windows.

* Problème interne : Spécifie les scénarios qui ne peut pas être atténués par l’administrateur client et doivent être résolus en tant qu’un problème de support. Lorsque vous fournissez des commentaires via le [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), incluez l’ID d’activité, puis se rapprocher de laps de temps de laquelle le problème s’est produite.
* Problème externe : se rapportent aux scénarios qui peuvent être atténuées par l’administrateur système. Il s’agit externes au bureau virtuel Windows.

Le tableau suivant répertorie les erreurs liées à que vos administrateurs susceptible de rencontrer.

>[!NOTE]
>Cette version préliminaire n’inclut pas une catégorisation complète des erreurs et sera mis à jour régulièrement. Pour garantir que vous disposez des informations les plus récentes, veillez à consulter, dans cet article, au moins une fois par mois.

### <a name="external-management-error-codes"></a>Codes d’erreur de gestion externe

|Code numérique|Code d'erreur|Solution suggérée|
|---|---|---|
|3|UnauthorizedAccess|L’utilisateur qui a tenté d’exécuter l’applet de commande PowerShell d’administration n’a pas les autorisations pour ce faire, ou mal tapé son nom d’utilisateur.|
|1 000|TenantNotFound|Le nom du client que vous avez entré ne correspond pas à des locataires existants. Passez en revue le nom de client de fautes de frappe et réessayez.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Vous ne pouvez pas supprimer un locataire comme longtemps qu’il contient des objets. Supprimez d’abord les pools d’hôte de session, puis réessayez.|
|2000|HostPoolNotFound|Le nom d’hôte du pool que vous avez entré ne correspond pas à tous les pools de hôte existant. Passez en revue le nom de pool d’hôte pour les fautes de frappe et réessayez.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Vous ne pouvez pas supprimer un pool de l’hôte tant qu’il contient des objets. Supprimez d’abord tous les groupes d’applications dans le pool de l’hôte.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Supprimer tous les hôtes de sessions avant de le supprimer du pool d’hôte de session.|
|5001|SessionHostNotFound|L’hôte de session interrogée peut être hors connexion. Vérifiez l’état du pool de l’hôte.|
|5008|SessionHostUserSessionsExist |Vous devez vous déconnecter tous les utilisateurs sur l’hôte de session avant l’exécution de votre activité de gestion.|
|6000|AppGroupNotFound|Le nom de groupe d’application que vous avez entré ne correspond pas à des groupes d’applications existant. Vérifiez le nom de groupe d’application pour les fautes de frappe et réessayez.|
|6022|RemoteAppNotFound|Le nom de RemoteApp que vous avez entré ne correspond pas à n’importe quel RemoteApps. Passez en revue le nom de RemoteApp de fautes de frappe et réessayez.|
|6010|PublishedItemsExist|Le nom de la ressource que vous tentez de publier est identique à une ressource qui existe déjà. Modifier le nom de ressource, puis réessayez.|
|7002|NameNotValidWhiteSpace|N’utilisez pas un espace blanc dans le nom.|
|8000|InvalidAuthorizationRoleScope|Le nom du rôle que vous avez entré ne correspond pas à aucun nom de rôle existant. Vérifiez le nom de rôle pour les fautes de frappe et réessayez. |
|8001|UserNotFound |Le nom d’utilisateur que vous avez entré ne correspond pas à aucun nom d’utilisateur existant. Vérifiez le nom de frappe et réessayez.|
|8005|UserNotFoundInAAD |Le nom d’utilisateur que vous avez entré ne correspond pas à aucun nom d’utilisateur existant. Vérifiez le nom de frappe et réessayez.|
|8008|TenantConsentRequired|Suivez les instructions [ici](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) à donner son consentement pour votre client.|

### <a name="external-connection-error-codes"></a>Codes d’erreur de connexion externe

|Code numérique|Code d'erreur|Solution suggérée|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|L’hôte de session n’est pas correctement joint à Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Les connexions a échoué car l’hôte de session n’est pas disponible. Vérifiez l’intégrité de l’hôte de session.|
|-2146233088|ConnectionFailedClientDisconnect|Si vous consultez souvent cette erreur, assurez-vous que l’ordinateur est connecté au réseau.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|La session d’à que l’utilisateur de l’hôte a tenté de se connecter n’est pas saine. Déboguer la machine virtuelle.|
|-2146233088|ConnectionFailedUserNotAuthorized|L’utilisateur n’est pas autorisé à accéder à l’application publiée ou le bureau. L’erreur peut apparaître une fois que l’administrateur de supprimer les ressources publiées. Demandez à l’utilisateur d’actualiser le flux dans l’application de bureau à distance.|
|2|FileNotFound|L’application que l’utilisateur tente d’accéder est incorrectement installée ou la valeur est un chemin d’accès incorrect.|
|3|InvalidCredentials|Le nom d’utilisateur ou mot de passe entré par l’utilisateur ne correspond pas à des noms d’utilisateur ou les mots de passe existant. Passez en revue les informations d’identification pour les fautes de frappe et réessayez.|
|8|ConnectionBroken|La connexion entre le Client et passerelle ou serveur supprimé. Aucune action n’est nécessaire, sauf si elle ne se.|
|14|UnexpectedNetworkDisconnect|La connexion au réseau est supprimée. Demandez à l’utilisateur pour se connecter à nouveau.|
|24|ReverseConnectFailed|La machine virtuelle hôte n’a aucune une ligne directe à la passerelle Bureau à distance. Vérifiez que l’adresse IP de la passerelle peut être résolu.|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les rôles au sein d’un bureau virtuel Windows, consultez [environnement en version préliminaire de bureau virtuel Windows](environment-setup.md).

Pour obtenir une liste des applets de commande PowerShell disponibles pour bureau virtuel de Windows, consultez le [référence PowerShell](/powershell/windows-virtual-desktop/overview).
