---
title: Activer les audits de sécurité pour Azure AD Domain Services | Microsoft Docs
description: Activer les audits de sécurité pour Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566704"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Activer les audits de sécurité pour Azure AD Domain Services (préversion)
L’audit de sécurité du service de domaine Azure AD permet aux clients d’utiliser le portail du service de domaine Azure AD pour envoyer en streaming les événements d’audit de sécurité vers des ressources ciblées. Les ressources qui peuvent recevoir ces événements incluent le Stockage Azure, les espaces de travail Azure Log Analytics ou Azure Event Hub. Peu après avoir activé les événements d’audit de sécurité, le service de domaine Azure AD envoie tous les événements audités pour la catégorie sélectionnée à la ressource ciblée. Les événements d’audit de sécurité permettent aux clients d’archiver les événements audités dans le stockage Azure. De plus, les clients peuvent intégrer les événements à un logiciel SIEM (Security Information and Event Management) (ou équivalent) à l’aide de hubs d’événements ou effectuer leurs propres insights et analyses à l’aide d’Azure Log Analytics à partir du portail Azure. 

> [!IMPORTANT]
> L’audit de sécurité Azure AD Domain Services est disponible uniquement sur les instances basées sur Azure Resource Manager pour Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Audit des catégories d’événements
L’audit de sécurité Azure AD Domain Services s’aligne sur l’audit traditionnel qui est fourni pour les contrôleurs de domaine Active Directory Domain Services. La réutilisation de modèles d’audit existants garantit que la même logique peut être utilisée lors de l’analyse des événements. L’audit de sécurité Azure AD Domain Services inclut les catégories d’événements suivants.

| Nom de la catégorie d’audit | Description |
|:---|:---|
| Connexion de compte|Audite les tentatives d’authentification de données de compte sur un contrôleur de domaine ou sur un gestionnaire de comptes de sécurité local.</p>Les événements et les paramètres de stratégie d’ouverture et de fermeture de session suivent les tentatives d’accès à un ordinateur particulier. Les paramètres et les événements de cette catégorie se concentrent sur la base de données de compte qui est utilisée. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer la validation des informations d’identification](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditer le service d’authentification Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditer les opérations de ticket du service Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditer d’autres événements d’ouverture/fermeture de session](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestion de compte|Audite les modifications apportées aux groupes et comptes d’utilisateurs et d’ordinateurs. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer la gestion des groupes d’applications](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditer la gestion des comptes d’ordinateur](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditer la gestion des groupes de distribution](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditer d’autres événements d’ouverture de session](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditer la gestion des groupes de sécurité](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditer la gestion des comptes d’utilisateur](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Suivi des détails|Audit les activités des applications et utilisateurs individuels sur l’ordinateur concerné et le fonctionnement de cet ordinateur. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer l’activité DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Auditer l’activité Plug-and-Play](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Auditer la création du processus](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditer la fin du processus](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditer les événements RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Accès aux services d’annuaire|Audite les tentatives d’accès aux objets dans Active Directory Domain Services (AD DS) et de modification de ces derniers. Ces événements d’audit sont journalisés uniquement sur des contrôleurs de domaine. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer la réplication du service d’annuaire détaillé](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditer l’accès au service d’annuaire](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditer les modifications du service d’annuaire](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditer la réplication du service d’annuaire](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Ouverture/fermeture de session|Audite les tentatives d’ouverture de session sur un ordinateur de manière interactive ou par le biais d’un réseau. Ces événements sont utiles pour le suivi de l’activité de l’utilisateur et l’identification des attaques potentielles sur les ressources réseau. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer le verrouillage du compte](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Auditer les revendications utilisateur/de périphérique](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditer le mode étendu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Auditer l’appartenance à un groupe](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditer le mode principal IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditer le mode rapide IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditer la fermeture de session](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditer l’ouverture de session](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditer le serveur NPS (Network Policy Server)](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditer d’autres événements d’ouverture/fermeture de session](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditer l’ouverture de session spéciale](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Accès aux objets| Audite les tentatives d’accès à des objets ou types d’objets spécifiques sur un ordinateur ou réseau. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer l’application générée](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditer les services de certification](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditer le partage de fichiers détaillé](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditer le partage de fichiers](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Auditer le système de fichiers](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditer la connexion de la plateforme de filtrage](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditer le rejet de paquet par la plateforme de filtrage](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Auditer la manipulation de handle](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Auditer l’objet de noyau](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditer d’autres événements d’accès à l’objet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Auditer le Registre](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Auditer le stockage amovible](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditer SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Auditer la stratégie d’accès centralisée intermédiaire](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Modification de stratégie|Audite les modifications apportées aux stratégies de sécurité importantes sur un réseau ou un système local. Les stratégies sont généralement établies par les administrateurs pour faciliter la sécurisation des ressources réseau. La supervision des modifications ou des tentatives de modification de ces stratégies peut être un aspect important de la gestion de la sécurité pour un réseau. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer la modification de la stratégie d’audit](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditer la modification de stratégie d’authentification](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditer la modification de la stratégie d’autorisation](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditer la modification de la stratégie de plateforme de filtrage](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditer la modification de la stratégie de niveau règle MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditer d’autres événements de modification de stratégie](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Utilisation des privilèges| Audite l’utilisation de certaines autorisations sur un ou plusieurs systèmes. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer l’utilisation de privilèges non sensibles](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditer l’utilisation de privilèges sensibles](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditer d’autres événements d’utilisation de privilèges](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Audite les modifications de niveau système apportées à un ordinateur non incluses dans les autres catégories et qui ont des implications de sécurité potentielles. Cette catégorie comprend les sous-catégories suivantes :<ul><li>[Auditer le pilote IPSEC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditer d’autres événements système](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditer la modification de l’état de la sécurité](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditer l’extension du système de sécurité](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditer l’intégrité du système](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID d’événements par catégorie
 L’audit de sécurité Azure AD Domain Services enregistre les ID d’événement suivants quand l’action spécifique déclenche un événement pouvant être audité.

| Nom de la catégorie d’événements | ID d’événement |
|:---|:---|
|Sécurité de la connexion de compte|4767, 4774, 4775, 4776, 4777|
|Sécurité de la gestion des comptes|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Sécurité du suivi des détails|Aucun|
|Sécurité de l’accès DS|5136, 5137, 5138, 5139, 5141|
|Sécurité de l’ouverture/fermeture de session|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Sécurité de l’accès aux objets|Aucun|
|Sécurité de la modification de stratégie|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Sécurité de l’utilisation des privilèges|4985|
|Sécurité système|4612, 4621|

## <a name="enable-security-audit-events"></a>Activer les événements d’audit de sécurité
Les conseils suivants vous aident à vous abonner aux événements d’audit de sécurité Azure AD Domain Services.

> [!IMPORTANT]
> Les audits de sécurité Azure AD Domain Services ne sont pas rétroactifs. Il est impossible de récupérer ou de relire des événements passés. Le service peut uniquement envoyer des événements qui se produisent une fois qu’il est activé.
>

### <a name="choose-the-target-resource"></a>Choisir la ressource cible
Vous pouvez utiliser n’importe quelle combinaison de stockage Azure, hubs d’événements Azure ou espaces de travail Azure Log Analytics comme ressource cible pour vos audits de sécurité. Consultez le tableau suivant afin de déterminer la meilleure ressource pour votre cas d’utilisation.

> [!IMPORTANT]
> Vous devez créer la ressource cible avant d’activer les audits de sécurité Azure AD Domain Services.
>

| Ressource cible | Scénario |
|:---|:---|
|Stockage Azure|Envisagez cette cible quand votre principal besoin est de stocker les événements d’audit de sécurité à des fins d’archivage. Les autres cibles peuvent être utilisées à des fins d’archivage ; toutefois, ces cibles fournissent des fonctionnalités au-delà du besoin principal d’archivage. Pour créer un compte de stockage Azure, consultez [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1).|
|Hubs d'événements Azure|Envisagez cette cible quand votre principal besoin est de partager des événements d’audit de sécurité avec un autre logiciel tel qu’un logiciel d’analyse de données ou un logiciel SIEM (Security Information and Event Management). Pour créer un hub d’événements, consultez [Démarrage rapide : Créer un hub d’événements avec le portail Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).|
|Espace de travail Azure Log Analytics|Envisagez cette cible quand votre principal besoin est d’analyser et de passer en revue les audits de sécurité directement à partir du portail Azure.  Pour créer un espace de travail Log Analytics, consultez [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Utilisation du portail Azure pour activer les événements d’audit de sécurité 
1. Connectez-vous au portail Azure sur https://portal.azure.com.  Dans le portail Azure, cliquez sur Tous les services. Dans la liste des ressources, tapez **Domaine**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Azure AD Domain Services**.
2. Cliquez sur l’instance Azure AD Domain Services dans la liste.
3. Cliquez sur **Paramètres de diagnostic (préversion)** dans la liste des actions sur la gauche.</p>
![Action Paramètres de diagnostic](./media/security-audit-events/diagnostic-settings-action.png)
4. Tapez le nom de la configuration de diagnostic (**aadds-auditing**, par exemple).</p>
![Page Paramètres de diagnostic](./media/security-audit-events/diagnostic-settings-page.png)
5. Cochez la case en regard des ressources ciblées que vous allez utiliser avec les événements d’audit de sécurité.
    > [!NOTE]
    > Vous ne pouvez pas créer de ressources cibles à partir de cette page.
    >
    
    **Stockage Azure :**</p>
    Cochez **Archiver dans un compte de stockage**. Cliquez sur **Configurer**. Sélectionnez l’**abonnement** et le **compte de stockage** que vous souhaitez utiliser pour archiver les événements d’audit de sécurité. Cliquez sur **OK**.</p>
    
    ![Paramètres de stockage du diagnostic](./media/security-audit-events/diag-settings-storage.png)
    
    **Hubs d’événements Azure :**</p>
    Cochez la case **Diffuser vers Event Hub**. Cliquez sur **Configurer**. Dans la page **Sélectionner un hub d’événements**, sélectionnez l’**abonnement** utilisé pour créer le hub d’événements. Ensuite, sélectionnez l’**espace de noms du hub d’événements**, le **nom du hub d’événements** et le **nom de la stratégie du hub d’événements**. Cliquez sur **OK**.</p>
    ![Paramètres du hub d’événements de diagnostic](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Espaces de travail Azure Log Analytics :**</p>
    Sélectionnez **Envoyer à Log Analytics**. Sélectionnez l’**abonnement** et l’**espace de travail Log Analytics** utilisés pour stocker les événements d’audit de sécurité.</p>
    ![Paramètres de l’espace de travail de diagnostic](./media/security-audit-events/diag-settings-log-analytics.png)

6. Sélectionnez les catégories de journaux à inclure pour la ressource cible particulière. Si vous utilisez des comptes de stockage, vous pouvez configurer des stratégies de conservation.

    > [!NOTE]
    > Vous pouvez sélectionner différentes catégories de journaux pour chaque ressource cible dans une même configuration. Cela vous permet de choisir les catégories de journaux à conserver pour Log Analytics et les catégories de journaux à archiver.
    >

7. Cliquez sur **Enregistrer** pour valider les modifications. Les ressources cibles reçoivent les événements d’audit de sécurité Azure AD Domain Services peu de temps après que vous avez enregistré votre configuration.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Utilisation d’Azure PowerShell pour activer les événements d’audit de sécurité
 
### <a name="prerequisites"></a>Prérequis

Suivez les instructions de l’article pour [Installer le module Azure PowerShell et vous connecter à votre abonnement Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Activer les audits de sécurité

1. Authentifiez-vous auprès d’Azure Resource Manager pour le locataire et l’abonnement appropriés à l’aide de l’applet de commande Azure PowerShell **Connect-AzAccount**.
2. Créez la ressource cible pour les événements d’audit de sécurité.</p>
    **Stockage Azure :**</p>
    Consultez [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) pour créer votre compte de stockage.</p>
    **Hubs d’événements Azure :**</p>
    Consultez [Démarrage rapide : Créer un hub d’événements avec Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) pour créer votre hub d’événements. Vous devrez peut-être également utiliser l’applet de commande Azure PowerShell [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) pour créer une règle d’autorisation afin de permettre des autorisations Active Directory AD Domain Services sur l’**espace de noms** du hub d’événements. La règle d’autorisation doit inclure les droits **Manage** (Gérer), **Listen** (Écouter) et **Send** (Envoyer).
    > [!IMPORTANT]
    > Vérifiez que vous définissez la règle d’autorisation sur l’espace de noms du hub d’événements, et non sur ce dernier.
       
    </p>
    
    **Espaces de travail Azure Log Analytics :**</p>
    Pour créer votre espace de travail, consultez [Créer un espace de travail Log Analytics avec Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh).
3. Obtenez l’ID de ressource pour votre instance Azure AD Domain Services. Dans une console Windows PowerShell ouverte et authentifiée, tapez la commande suivante. Utilisez la variable **$aadds.ResourceId** comme paramètre pour l’ID de ressource Azure AD Domain Services pour les applets de commande futures.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Utilisez l’applet de commande **Set-AzDiagnosticSetting** pour configurer les paramètres de diagnostic Azure afin d’utiliser la ressource cible pour les événements d’audit de sécurité Azure AD Domain Services. Dans les exemples ci-dessous, la variable $aadds.ResourceId représente l’ID de ressource de votre instance Azure AD Domain Services (voir l’étape 3).</p>
    **Stockage Azure :**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Remplacez *storageAccountId* par l’ID de votre compte de stockage.</p>
    
    **Hubs d’événements Azure :**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Remplacez *eventHubName* par le nom de votre hub d’événements. Remplacez *eventHubRuleId* par l’ID de la règle d’autorisation que vous avez créée.</p>
    
    **Espaces de travail Azure Log Analytics :**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Remplacez *workspaceId* par l’ID de l’espace de travail Log Analytics que vous avez créé. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Afficher les événements d’audit de sécurité à l’aide d’Azure Monitor
Les espaces de travail log Analytics vous permettent d’afficher et d’analyser les événements d’audit de sécurité à l’aide d’Azure Monitor et du langage de requête Kusto. Le langage de requête est conçu pour être utilisé en lecture seule, offrant des capacités d’analyse puissantes avec une syntaxe facile à lire.
Voici quelques ressources pour bien démarrer avec le langage de requête Kusto.
* [Documentation Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Prise en main de Log Analytics dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Bien démarrer avec les requêtes de journal Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries).
* [Créer et partager des tableaux de bord de données Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Exemples de requêtes

### <a name="sample-query-1"></a>Exemple de requête 1
Tous les événements de verrouillage de compte pour les sept derniers jours.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Exemple de requête 2
Tous les événements de verrouillage de compte (4740) entre le 26 juin 2019 à 9:00 et le 1er juillet 2019 minuit, triés par ordre croissant de date et heure.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Exemple de requête 3
Événements de connexion de compte il y a sept jours (par rapport à maintenant) pour l’utilisateur de compte nommé.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Exemple de requête 4
Événements de connexion de compte il y a sept jours par rapport à maintenant pour l’utilisateur de compte nommé qui a tenté de se connecter à l’aide d’un mot de passe incorrect (0xC0000006a).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Exemple de requête 5
Événements de connexion de compte il y a sept jours par rapport à maintenant pour l’utilisateur de compte nommé qui a tenté de se connecter alors que le compte était verrouillé (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Exemple de requête 6
Nombre d’événements de connexion de compte il y a sept jours par rapport à maintenant pour toutes les tentatives de connexion qui se sont produites pour tous les utilisateurs verrouillés.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Contenu connexe
* [Vue d’ensemble](https://docs.microsoft.com/azure/kusto/query/) du langage de requête Kusto.
* [Tutoriel Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) pour vous familiariser avec les principes fondamentaux des requêtes.
* [Exemples de requêtes](https://docs.microsoft.com/azure/kusto/query/samples) qui vous aident à découvrir de nouvelles façons de voir vos données.
* [Bonnes pratiques](https://docs.microsoft.com/azure/kusto/query/best-practices) Kusto pour optimiser vos requêtes.














 
