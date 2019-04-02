---
title: Connecter Operations Manager à Azure Monitor | Microsoft Docs
description: Pour préserver vos investissements existants dans System Center Operations Manager et utiliser des fonctionnalités étendues avec Log Analytics, vous pouvez intégrer Operations Manager à votre espace de travail.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 2768a23c217052a342538b67ec59868e25fd4914
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793813"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Connecter Operations Manager à Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pour conserver vos investissements existants dans [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) et utiliser des fonctionnalités étendues avec Azure Monitor, vous pouvez intégrer Operations Manager à votre espace de travail Analytique de journal. Cela permet de que vous exploiter les opportunités de journaux dans Azure Monitor tout en continuant à utiliser Operations Manager pour :

* Surveiller l’intégrité de vos services informatiques avec Operations Manager
* Conserver l’intégration avec vos solutions ITSM pour la gestion des incidents et des problèmes
* Gérer le cycle de vie des agents déployés sur des machines virtuelles IaaS, qu’elles soient locales ou dans le cloud public, et que vous surveillez avec Operations Manager

L’intégration avec System Center Operations Manager enrichit votre stratégie d’opérations de service à l’aide de la vitesse et l’efficacité d’Azure Monitor dans la collecte, le stockage et analyse des données de journal à partir d’Operations Manager. Analyser le journal Azure interroge une corrélation aide et travail à identifier les erreurs de problèmes et les expositions de récurrences pour prendre en charge votre processus de gestion de problème existant. La flexibilité du moteur de requête pour examiner les performances, des événements et des alertes de données, avec les tableaux de bord complets et les fonctions de rapport pour exposer ces données de façon significative, montre la puissance d’Azure Monitor offre en matière d’Operations Manager.

Les agents qui communiquent avec le groupe d’administration Operations Manager collectent des données de vos serveurs selon les [des sources de données Analytique de journal](agent-data-sources.md) et solutions que vous avez activé dans votre espace de travail. Selon les solutions activées, leurs données sont soit envoyées directement à partir d’un serveur d’administration Operations Manager pour le service, ou en raison du volume de données collectées sur le système géré par agent, sont envoyées directement à partir de l’agent à un espace de travail Analytique de journal. Le serveur d’administration transfère directement les données au service. Celles-ci ne sont jamais écrites dans la base de données opérationnelle ou de l’entrepôt de données. Lorsqu’un serveur d’administration perd la connectivité avec Azure Monitor, il met en cache les données localement jusqu'à ce que la communication est rétablie. Si le serveur d’administration est hors connexion en raison d’une maintenance planifiée ou d’une interruption non planifiée, un autre serveur d’administration dans le groupe d’administration assure la connectivité avec Azure Monitor.  

Le diagramme suivant montre la connexion entre les serveurs d’administration et les agents dans un groupe d’administration System Center Operations Manager et Azure Monitor, notamment la direction et les ports.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Si vos stratégies de sécurité informatiques n’autorisent pas les ordinateurs sur votre réseau à se connecter à Internet, les serveurs d’administration peuvent être configurés pour se connecter à la passerelle Log Analytics afin de recevoir des informations de configuration et d’envoyer les données collectées en fonction des solutions activées. Pour plus d’informations et pour savoir comment configurer votre groupe d’administration Operations Manager pour communiquer via une passerelle d’Analytique de journal à Azure Monitor, consultez [connecter des ordinateurs à Azure Monitor à l’aide de la passerelle d’Analytique de journal](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Conditions préalables 

Avant de commencer, passez en revue les exigences suivantes.

* Azure Monitor prend uniquement en charge System Center Operations Manager 2016 ou version ultérieure, Operations Manager 2012 SP1 UR6 ou version ultérieure et Operations Manager 2012 R2 UR2 ou version ultérieure. La prise en charge du proxy a été ajoutée dans Operations Manager 2012 SP1 UR7 et Operations Manager 2012 R2 UR3.
* L’intégration de System Center Operations Manager 2016 dans le cloud US Government nécessite un pack d’administration Advisor mis à jour inclus avec le correctif cumulatif 2 ou version ultérieure. System Center Operations Manager 2012 R2 nécessite un pack d’administration Advisor mis à jour inclus avec le correctif cumulatif 3 ou version ultérieure.
* Tous les agents Operations Manager doivent répondre aux exigences en matière de prise en charge. Vérifiez que chaque agent est au niveau minimum de mise à jour ; sinon, la communication de l’agent Windows échouera, entraînant des erreurs dans le journal des événements Operations Manager.
* Un espace de travail Log Analytics. Pour plus d'informations, consultez [Présentation de l'espace de travail Log Analytics](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).   
* Vous vous authentifiez sur Azure avec un compte qui est membre du [rôle Collaborateur Log Analytics](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Modifications récentes apportées à l’API Azure empêche les clients d’être en mesure de configurer correctement l’intégration entre leur groupe d’administration et d’Azure Monitor pour la première fois. Pour les clients qui ont déjà intégré leur groupe d’administration avec le service, vous n’êtes pas affecté, sauf si vous devez reconfigurer votre connexion existante.  
>Un nouveau pack d’administration a été publié pour les versions suivantes d’Operations Manager :
>  
>* Pour System Center Operations Manager 1801, téléchargez le pack d'administration [ici](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Pour System Center Operations Manager 2016, téléchargez le pack d'administration [ici](https://www.microsoft.com/download/details.aspx?id=57172)  
>* Pour System Center Operations Manager 2012 R2, téléchargez le pack d'administration [ici](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Cette mise à jour du pack de gestion n’est pas applicable à System Center Operations Manager 1807, qui est une version mise à jour depuis la version 1801 et non un build complet du produit.   

### <a name="network"></a>Réseau

Les informations ci-dessous la liste des informations de configuration du pare-feu et de proxy requises pour l’agent Operations Manager, serveurs d’administration et console Opérateur communiquer avec Azure Monitor. Le trafic provenant de chaque composant est sortant à partir de votre réseau à Azure Monitor.   

|Ressource | Numéro de port| Ignorer l’inspection HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Oui|  
|\*.oms.opinsights.azure.com| 443|Oui|  
|\*.blob.core.windows.net| 443|Oui|  
|\*.azure-automation.net| 443|Oui|  
|**Serveur d’administration**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Oui|  
|\*.ods.opinsights.azure.com| 443| Oui|  
|*.azure-automation.net | 443| Oui|  
|**Console Operations Manager à Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 et 443||  
|\*.microsoft.com| 80 et 443||  
|\*.microsoftonline.com| 80 et 443||  
|\*.mms.microsoft.com| 80 et 443||  
|login.windows.net| 80 et 443||  
|portal.loganalytics.io| 80 et 443||
|api.loganalytics.io| 80 et 443||
|docs.loganalytics.io| 80 et 443||  

### <a name="tls-12-protocol"></a>Protocole TLS 1.2

Pour garantir la sécurité des données en transit vers Azure Monitor, nous vous encourageons vivement à configurer le groupe d’administration et l’agent à utiliser au moins sécurité TLS (Transport Layer) 1.2. Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**. Pour plus d’informations, passez en revue [Envoi sécurisé de données via TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-azure-monitor"></a>Connexion d’Operations Manager à Azure Monitor

Suivez les étapes ci-après pour configurer votre groupe d’administration Operations Manager de façon à ce qu’il se connecte à l’un de vos espaces de travail Log Analytics.

Lors de l’inscription initiale de votre groupe d’administration Operations Manager avec un espace de travail Log Analytics, l’option de spécification de la configuration du proxy pour le groupe d’administration n’est pas disponible dans la console Opérations.  Le groupe d’administration doit être correctement inscrit auprès du service avant que cette option soit disponible.  À cet effet, vous devez mettre à jour la configuration du proxy système à l’aide de Netsh sur le système à partir duquel vous exécutez la console Opérations pour configurer l’intégration et tous les serveurs d’administration du groupe d’administration.  

1. Ouvrez une invite de commande avec élévation de privilèges.
   a. Allez à **Démarrer** et entrez **cmd**.
   b. Cliquez avec le bouton de droite sur **Invite de commande** et sélectionnez Exécuter en tant qu’administrateur**.
1. Saisissez la commande suivante, puis appuyez sur **Entrée** :

    `netsh winhttp set proxy <proxy>:<port>`

Après avoir effectué les étapes suivantes pour intégrer avec Azure Monitor, vous pouvez supprimer la configuration en exécutant `netsh winhttp reset proxy` , puis utilisez le **configurer le serveur proxy** option dans la console opérateur pour spécifier le proxy ou le journal Serveur de passerelle Analytique. 

1. Dans la console Operations Manager, sélectionnez l’espace de travail **Administration** .
1. Développez le nœud Operations Management Suite, puis cliquez sur **Connexion**.
1. Cliquez sur le lien **S’inscrire auprès de Operations Management Suite** .
1. Sur la page **Assistant Intégration d’Operations Management Suite : authentification**, entrez l’adresse e-mail ou le numéro de téléphone et le mot de passe du compte administrateur associés à votre abonnement OMS, puis cliquez sur **Se connecter**.

   >[!NOTE]
   >Le nom d’Operations Management Suite a été retiré. 
   
1. Une fois authentifié, vous êtes invité à sélectionner votre espace de travail Log Analytics ainsi que votre tenant et votre abonnement Azure sur la page **Assistant Intégration d’Operations Management Suite : sélectionner un espace de travail**. Si vous avez plusieurs espaces de travail, sélectionnez celui que vous souhaitez inscrire auprès du groupe d’administration d’Operations Manager dans la liste déroulante, puis cliquez sur **Suivant**.
   
   > [!NOTE]
   > Operations Manager prend uniquement en charge un espace de travail Log Analytics à la fois. La connexion et les ordinateurs qui ont été inscrits auprès d’Azure Monitor avec l’espace de travail précédent sont supprimés à partir d’Azure Monitor.
   > 
   > 
1. Sur la page **Assistant Intégration d’Operations Management Suite : résumé**, vérifiez vos paramètres. S’ils sont corrects, cliquez sur **Créer**.
1. Sur la page **Assistant Intégration d’Operations Management Suite : fin** , cliquez sur **Fermer**.

### <a name="add-agent-managed-computers"></a>Ajout d’ordinateurs gérés par des agents

La configuration de l’intégration avec votre espace de travail Log Analytics établit uniquement une connexion au service, sans qu’aucune donnée soit collectée par les agents générant des rapports pour votre groupe d’administration. Cela ne se produire jusqu'à ce que, après avoir configuré les ordinateurs gérés par agent spécifiques collectant des données de journal pour Azure Monitor. Vous pouvez sélectionner les objets d’ordinateur individuellement ou sélectionner un groupe qui contient des objets d’ordinateur Windows. Vous ne pouvez pas sélectionner un groupe qui contient des instances d’une autre classe, telles que des disques logiques ou des bases de données SQL.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
1. Développez le nœud Operations Management Suite, puis cliquez sur **Connexion**.
1. Cliquez sur le lien **Ajouter un ordinateur/groupe** sous l’en-tête Actions dans la partie droite du volet.
1. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d’ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à Azure Monitor, cliquez sur **ajouter**, puis cliquez sur **OK**.

Vous pouvez afficher les ordinateurs et les groupes configurés pour collecter des données à partir du nœud Ordinateurs gérés, sous Operations Management Suite, dans l’espace de travail **Administration** de la console Opérateur. De là, vous pouvez ajouter ou supprimer des ordinateurs et des groupes selon les besoins.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Configuration des paramètres de proxy dans la console Operations

Si un serveur proxy interne est entre le groupe d’administration et d’Azure Monitor, procédez comme suit. Ces paramètres sont gérés à partir du groupe d’administration et distribués aux systèmes gérés par agent qui sont inclus dans l’étendue pour collecter des données de journal pour Azure Monitor de manière centralisée.  Cette méthode présente des avantages lorsque certaines solutions contournent le serveur d’administration et envoient les données directement au service.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
1. Développez Operations Management Suite, puis cliquez sur **Connexions**.
1. Dans la vue Connexion à OMS, cliquez sur **Configurer le serveur proxy**.
1. Sur la page **Assistant Operations Management Suite : serveur proxy**, sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite**, puis saisissez l’URL avec le numéro de port, par exemple http://corpproxy:80, et cliquez sur **Terminer**.

Si votre serveur proxy requiert une authentification, procédez comme suit pour configurer les paramètres qui doivent se propager aux ordinateurs gérés qui signale à Azure Monitor dans le groupe d’administration et les informations d’identification.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
1. Sous **Configuration d’identification**, sélectionnez **Profils**.
1. Ouvrez le profil **Proxy d'identification de System Center Advisor** .
1. Dans l’Assistant Profil d’identification, cliquez sur Ajouter pour utiliser un compte d’identification. Vous pouvez créer un [compte d’identification](https://technet.microsoft.com/library/hh321655.aspx) ou utiliser un compte existant. Ce compte doit disposer des autorisations suffisantes pour franchir le serveur proxy.
1. Pour définir le compte à gérer, choisissez **Une classe, un groupe ou un objet sélectionné(e)**, cliquez sur **Sélectionner...** puis sur **Groupe...** Pour ouvrir la boîte de dialogue **Recherche de groupes**.
1. Recherchez le **groupe Microsoft System Center Advisor Monitoring Server**, puis sélectionnez-le. Une fois le groupe sélectionné, cliquez sur **OK** pour fermer la zone **Recherche de groupes**.
1. Cliquez sur **OK** pour fermer la zone **Ajouter un compte d’identification**.
1. Cliquez sur **Enregistrer** pour fermer l’Assistant et enregistrer vos modifications.

Une fois que la connexion est créée et vous configurez les agents collectent et envoient les données de journal à Azure Monitor, la configuration suivante est appliquée dans le groupe d’administration, mais pas nécessairement dans l’ordre :

* Le compte d’identification **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** est créé. Il est associé au profil **Blob du profil d’identification de Microsoft System Center Advisor** et cible deux classes : **Collection Server** et **Operations Manager Management Group**.
* Deux connecteurs sont créés.  Le premier se nomme **Microsoft.SystemCenter.Advisor.DataConnector** et est automatiquement configuré avec un abonnement qui transfère toutes les alertes générées par les instances de toutes les classes dans le groupe d’administration pour Azure Monitor. Le second connecteur **Advisor Connector**, qui est chargé de communiquer avec Azure Monitor et de partager des données.
* Les agents et les groupes sélectionnés pour la collecte de données dans le groupe d’administration sont ajoutés au **groupe Microsoft System Center Advisor Monitoring Server**.

## <a name="management-pack-updates"></a>Mises à jour du pack d’administration

Une fois la configuration terminée, le groupe d’administration Operations Manager établit une connexion avec Azure Monitor. Le serveur d’administration est synchronisé au service web et reçoit des informations de configuration mises à jour sous la forme de packs d’administration pour les solutions que vous avez activées et qui s’intègrent à Operations Manager. Operations Manager recherche des mises à jour de ces packs d’administration, puis les télécharge et les importe automatiquement lorsqu’elles sont disponibles. Deux règles principales contrôlent ce processus :

* **Microsoft.SystemCenter.Advisor.MPUpdate** -met à jour des base des packs d’administration Azure Monitor. S’exécute toutes les 12 heures par défaut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : met à jour les packs d’administration de solution activés dans votre espace de travail. Par défaut, elle s’exécute toutes les cinq (5) minutes.

Vous pouvez ignorer ces deux règles pour empêcher le téléchargement automatique par leur désactivation, ou modifier la fréquence de la fréquence à laquelle le serveur d’administration se synchronise avec Azure Monitorto déterminent si un nouveau pack d’administration est disponible et doit être téléchargé. Suivez les étapes de la [procédure de remplacement d’une règle ou d’une analyse](https://technet.microsoft.com/library/hh212869.aspx) pour modifier la valeur en secondes du paramètre **Fréquence** afin de changer la fréquence de synchronisation, ou pour modifier le paramètre **Activé** afin de désactiver les règles. Pour le remplacement, ciblez tous les objets de la classe Groupe d’administration Operations Manager.

Pour continuer à suivre votre processus habituel de contrôle des modifications pour contrôler les versions du pack d’administration dans votre groupe d’administration de production, vous pouvez désactiver les règles et les activer à des heures auxquelles les mises à jour sont autorisées. Si votre environnement dispose d’un groupe d’administration du développement ou de l’assurance qualité connecté à Internet, vous pouvez configurer ce groupe d’administration avec un espace de travail Log Analytics de façon à ce qu’il prenne en charge ce scénario. Ainsi, vous pourrez examiner et évaluer les versions itératives des packs d’administration Azure Monitor avant de les introduire dans votre groupe d’administration de production.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Basculement d’un groupe Operations Manager vers un nouvel espace de travail Log Analytics

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**, puis créez un espace de travail.  
1. Ouvrez la console Operations Manager à l’aide d’un compte qui est membre du rôle Operations Manager Administrators, puis sélectionnez l’espace de travail **Administration** .
1. Développez Log Analytics, puis sélectionnez **Connexions**.
1. Sélectionnez le lien **Reconfigurer Operation Management Suite** au milieu du volet.
1. Dans la page **Assistant Intégration de Log Analytics**, entrez l’adresse e-mail ou le numéro de téléphone et le mot de passe du compte d’administrateur associé à votre nouvel espace de travail Log Analytics.
   
   > [!NOTE]
   > La page **Assistant Intégration d’Operations Management Suite : sélectionner un espace de travail** indique l’espace de travail existant en cours d’utilisation.
   > 
   > 

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Valider l’intégration d’Operations Manager avec Azure Monitor

Il existe plusieurs façons différentes, vous pouvez vérifier que Azure Monitor pour l’intégration d’Operations Manager a réussi.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Pour confirmer l’intégration à partir du portail Azure

1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée.
1. Dans votre liste d’espaces de travail Log Analytics, sélectionnez celui voulu.  
1. Sélectionnez **Paramètres avancés**, **Sources connectées**, puis **System Center**. 
1. Dans la table, sous la section System Center Operations Manager, vous devriez voir le nom du groupe d’administration avec le nombre d’agents et son état lors de la dernière réception de données.
   
   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Pour confirmer l’intégration à partir de la console Operations

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration** .
1. Sélectionnez **Packs d’administration**, puis, dans la zone de texte **Rechercher**, tapez **Advisor** ou **Intelligence**.
1. En fonction des solutions activées, le pack d’administration correspondant apparaît dans les résultats de recherche.  Par exemple, si vous avez activé la solution de gestion des alertes, le pack d’administration Gestion des alertes Microsoft System Center Advisor figure dans la liste.
1. À partir de la vue **Analyse**, accédez à la vue **Operations Management Suite\État d’intégrité**.  Sélectionnez un serveur d’administration sous le volet **État du serveur d’administration**. Ensuite, dans le volet **Vue détaillée**, confirmez que la valeur de la propriété **URI du service d’authentification** correspond bien à l’identifiant de l’espace de travail Log Analytics.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Supprimer l’intégration avec Azure Monitor

Si l’intégration entre votre groupe d’administration Operations Manager et votre espace de travail Log Analytics se révèle inutile, vous devez effectuer plusieurs étapes pour supprimer correctement la connexion et la configuration dans le groupe d’administration. La procédure suivante vous a à mettre à jour de votre espace de travail Analytique de journal en supprimant la référence de votre groupe d’administration, supprimer les connecteurs Azure Monitor, puis supprimez les packs d’administration prenant en charge l’intégration avec le service.  

Packs d’administration pour les solutions vous avez activé qui s’intègrent avec Operations Manager et les packs d’administration requis pour prendre en charge l’intégration avec Azure Monitor ne peut pas être facilement supprimés du groupe d’administration. Il s’agit, car certains des packs d’administration Azure Monitor ont des dépendances sur d’autres packs d’administration associés. Pour supprimer les packs d’administration qui ont une dépendance vis-à-vis d’autres packs d’administration, téléchargez le script [Supprimer un pack d’administration avec des dépendances](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) à partir du centre de scripts TechNet.  

1. Ouvrez l’interface de commande de Microsoft Operations Manager à l’aide d’un compte qui est membre du rôle Administrateurs Operations Manager.
   
    > [!WARNING]
    > Avant de continuer, vérifiez que les noms des packs d’administration personnalisés ne contiennent pas « Advisor » ou « IntelligencePack » ; sinon, les étapes suivantes les supprimeront du groupe d’administration.
    > 

1. À l’invite de l’interpréteur de commandes, tapez `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Ensuite, tapez `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Pour supprimer les packs d’administration restants qui ont une dépendance sur les autres packs d’administration System Center Advisor, utilisez le script *RecursiveRemove.ps1* que vous avez téléchargé à partir du centre de scripts TechNet.  
 
    > [!NOTE]
    > L’étape de suppression des packs d’administration Advisor avec PowerShell ne supprimera pas automatiquement les packs d’administration Microsoft System Center Advisor ou Microsoft System Center Advisor Internal.  N’essayez pas de les supprimer.  
    >  

1. Ouvrez la console Opérateur Operations Manager à l’aide d’un compte qui est membre du rôle Administrateurs Operations Manager.
1. Sous **Administration**, sélectionnez le nœud **Packs d’administration**. Ensuite, dans la zone **Rechercher**, tapez **Advisor** et vérifiez que les packs d’administration suivants sont toujours importés dans votre groupe d’administration :
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Dans le portail Azure, cliquez sur la vignette **Paramètres**.
1. Sélectionnez **Sources connectées**.
1. Dans le tableau sous la section System Center Operations Manager, le nom du groupe d’administration à supprimer de l’espace de travail doit s’afficher. Sous la colonne **Dernières données**, cliquez sur **Supprimer**.  
   
    > [!NOTE]
    > Le lien **Supprimer** ne sera pas disponible avant 14 jours si aucune activité n’est détectée à partir du groupe d’administration connecté.  
    > 

1. Une fenêtre s’affiche pour vous demander de confirmer la suppression.  Cliquez sur **Oui** pour continuer. 

Pour supprimer les deux connecteurs (Microsoft.SystemCenter.Advisor.DataConnector et Advisor Connector), enregistrez le script PowerShell ci-dessous sur votre ordinateur et exécutez-le en suivant les exemples ci-dessous :

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Si l’ordinateur à partir duquel vous exécutez ce script n’est pas un serveur d’administration, vous devez installer l’interpréteur de commandes Operations Manager selon la version de votre groupe d’administration.
> 
> 

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Par la suite, pour reconnecter votre groupe d’administration à un espace de travail Log Analytics, vous devez réimporter le fichier du pack d’administration `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb`. Selon la version de System Center Operations Manager déployée dans votre environnement, ce fichier se trouve à l’emplacement suivant :

* Sur le média source, dans le dossier `\ManagementPacks` pour System Center 2016 - Operations Manager et les versions supérieures.
* À partir du correctif cumulatif le plus récent appliqué à votre groupe d’administration. Pour Operations Manager 2012, le dossier source est` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` pour 2012 R2. Il se trouve dans `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Étapes suivantes

Pour ajouter des fonctionnalités et collecter des données, consultez [solutions ajouter Azure Monitor à partir de la galerie de Solutions](../../azure-monitor/insights/solutions.md).