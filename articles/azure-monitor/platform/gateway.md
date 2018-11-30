---
title: Connecter des ordinateurs à l’aide de la passerelle Log Analytics | Microsoft Docs
description: Connectez vos appareils et les ordinateurs contrôlés par Operations Manager à la passerelle Log Analytics pour leur permettre d’envoyer des données aux services Azure Automation et Log Analytics lorsqu’ils n’ont pas accès à Internet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 5e19c7c1ed15183fdb796a6fa4e537da946b40b9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637334"
---
# <a name="connect-computers-without-internet-access-using-the-log-analytics-gateway"></a>Connecter des ordinateurs sans accès Internet à l’aide de la passerelle Log Analytics
Ce document décrit comment configurer la communication avec Azure Automation et Log Analytics à l’aide de la passerelle Log Analytics lorsqu’elle est directement connectée ou lorsque les ordinateurs analysés Operations Manager n’ont pas accès à Internet.  La passerelle Log Analytics, qui est un proxy de transfert HTP prenant en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT, peut collecter des données et les envoyer à Azure Automation et Log Analytics en son nom.  

La passerelle Log Analytics prend en charge ce qui suit :

* Runbooks Workers hybrides Azure Automation  
* Ordinateurs Windows avec Microsoft Monitoring Agent directement connecté à un espace de travail Log Analytics
* Ordinateurs Linux avec agent Log Analytics pour Linux directement connecté à un espace de travail Log Analytics  
* System Center Operations Manager 2012 SP1 avec UR7, Operations Manager 2012 R2 avec UR3, Operations Manager 2016 et le groupe de gestion Operations Manager version 1801 intégré à Log Analytics.  

Si vos stratégies de sécurité informatique ne permettent pas aux ordinateurs de votre réseau de se connecter à Internet, comme des appareils de point de vente (PDV) ou des serveurs prenant en charge des services informatiques, mais que vous devez les connecter à Azure Automation ou Log Analytics pour les gérer et les surveiller, ils peuvent être configurés pour communiquer directement avec la passerelle Log Analytics pour recevoir la configuration et faire suivre les données en leur nom.  Si ces ordinateurs sont configurés avec l’agent Log Analytics pour se connecter directement à un espace de travail Log Analytics, tous les ordinateurs communiqueront plutôt avec la passerelle Log Analytics.  La passerelle transfère directement les données des agents au service. Elle n’analyse aucune des données en transit.

Lorsqu’un groupe d’administration Operations Manager est intégré à Log Analytics, les serveurs d’administration peuvent être configurés pour se connecter à la passerelle Log Analytics pour recevoir des informations de configuration et envoyer les données collectées en fonction de la solution que vous avez activée.  Les agents Operations Manager envoient certaines données liées à Operations Manager, telles que des alertes, évaluations de la configuration, espaces d’instance et données de capacité au serveur d’administration. D’autres données volumineuses, telles que les journaux IIS, les données de performances et les événements de sécurité, sont envoyées directement à la passerelle Log Analytics.  Si vous avez un ou plusieurs serveurs de passerelle Operations Manager déployés dans une zone DMZ ou un autre réseau isolé pour analyser les systèmes non fiables, ils ne peuvent pas communiquer avec une passerelle Log Analytics.  Les serveurs de passerelle Operations Manager peuvent uniquement générer des rapports sur un serveur d’administration.  Lorsqu’un groupe d’administration Operations Manager est configuré pour communiquer avec la passerelle Log Analytics, les informations de configuration de proxy sont automatiquement distribuées à tous les ordinateurs gérés par agent qui sont configurés pour collecter des données pour Log Analytics, même si le paramètre est vide.    

Pour fournir une haute disponibilité pour les groupes directement connectés ou Operations Management qui communiquent avec Log Analytics via la passerelle, vous pouvez utiliser l’équilibrage de charge au niveau du réseau pour rediriger et distribuer le trafic entre plusieurs serveurs de passerelle.  Si un serveur de passerelle tombe en panne, le trafic est redirigé vers un autre nœud disponible.  

L’agent Log Analytics est requis sur l’ordinateur exécutant la passerelle Log Analytics afin qu’il puisse identifier les points de terminaison du service dont il a besoin pour la communication et surveiller la passerelle Log Analytics pour analyser ses performances ou données d’événement.

Chaque agent doit disposer d’une connexion réseau avec sa passerelle, afin que les agents puissent automatiquement transférer des données vers la passerelle et recevoir des données de cette dernière. L’installation de la passerelle sur un contrôleur de domaine n’est pas recommandée.

Le diagramme suivant affiche le flux de données entre les agents directs et Azure Automation avec Log Analytics utilisant le serveur de passerelle.  La configuration de proxy des agents doit correspondre au même port que celui sur lequel la passerelle Log Analytics est configurée pour communiquer avec le service.  

![diagramme de communication directe entre l’agent et les services](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Le diagramme suivant illustre le flux de données entre un groupe d’administration Operations Manager et Log Analytics.   

![diagramme de communication entre Operations Manager et Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Prérequis

Lorsque vous configurez un ordinateur pour qu’il s’exécute sur la passerelle Log Analytics, cet ordinateur doit disposer des éléments suivants :

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 et Windows Server 2008
* .NET Framework 4.5
* Au minimum un processeur 4 cœurs et 8 Go de mémoire 
* Agent Log Analytics pour Windows 

### <a name="language-availability"></a>Langues disponibles

La passerelle Log Analytics est disponible dans les langues suivantes :

- Chinois (simplifié)
- Chinois (traditionnel)
- Tchèque
- Néerlandais
- Français
- Français
- Allemand
- Hongrois
- Italien
- Japonais
- Coréen
- Polonais
- Portugais (Brésil)
- Portugais (Portugal)
- Russe
- Espagnol (international)

### <a name="supported-encryption-protocols"></a>Protocoles de chiffrement pris en charge
La passerelle Log Analytics prend uniquement en charge le protocole TLS version 1.0, 1.1 et 1.2.  Elle ne prend pas en charge le protocole SSL.  Pour garantir la sécurité des données en transit vers Log Analytics, nous vous encourageons vivement à configurer la passerelle de façon à utiliser au moins Transport Layer Security (TLS) 1.2. Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**.  Pour plus d’informations, passez en revue [Envoi sécurisé de données via TLS 1.2](../../log-analytics/log-analytics-data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Nombre de connexion d’agent prises en charge
Le tableau suivant met en lumière le nombre d’agents pris en charge qui communiquent avec un serveur de passerelle.  Cette prise en charge est basée sur des agents qui chargent environ 200 Ko de données toutes les 6 secondes. Le volume de données par agent testé est d’environ 2,7 Go par jour.

|Passerelle |Nombre approximatif d’agents pris en charge|  
|--------|----------------------------------|  
|- Processeur : Intel XEON CPU E5-2660 v3 \@ 2,6 GHz 2 cœurs<br> - Mémoire : 4 Go<br> Bande passante réseau : 1 Go/s| 600|  
|- Processeur : Intel XEON CPU E5-2660 v3 \@ 2,6 GHz 4 cœurs<br> - Mémoire : 8 Go<br> Bande passante réseau : 1 Go/s| 1 000|  

## <a name="download-the-log-analytics-gateway"></a>Téléchargement de la passerelle Log Analytics

Il existe deux méthodes pour obtenir la dernière version du fichier de configuration de la passerelle Log Analytics.

1. Téléchargez-le à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

1. Téléchargez à partir du portail Azure.  Après la connexion au portail Azure :  

   1. Parcourez la liste des services, puis sélectionnez **Log Analytics**.  
   1. Sélectionnez un espace de travail.
   1. Dans le panneau de votre espace de travail, sous **Général**, cliquez sur **Démarrage rapide**.
   1. Sous **Choisir une source de données pour se connecter à l’espace de travail**, cliquez sur **Ordinateurs**.
   1. Dans le panneau **Agent direct**, cliquez sur **Télécharger la passerelle Log Analytics**.<br><br> ![téléchargement de la passerelle Log Analytics](./media/gateway/download-gateway.png)

or 

   1. Dans le panneau de votre espace de travail sous **Paramètres**, cliquez sur **Paramètres avancés**.
   1. Accédez à **Sources connectées** > **Serveurs Windows** et cliquez sur **Télécharger la passerelle de Log Analytics**.

## <a name="install-the-log-analytics-gateway"></a>Installation de la passerelle Log Analytics

Pour installer une passerelle, procédez comme suit.  Si vous avez installé une version antérieure, anciennement appelée *Log Analytics Forwarder*, il sera mis à niveau vers cette version.  

1. Dans le dossier de destination, double-cliquez sur **Log Analytics gateway.msi**.
1. Sur la page d’**accueil**, cliquez sur **Suivant**.<br><br> ![Assistant d’installation de la passerelle](./media/gateway/gateway-wizard01.png)<br> 
1. Dans la page du **contrat de licence**, sélectionnez **J’accepte les termes du contrat de licence** pour accepter le CLUF, puis cliquez sur **Suivant**.
1. Sur la page relative au **port et à l’adresse proxy** :
   1. Saisissez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation configure une règle entrante avec ce numéro de port sur le pare-feu Windows.  La valeur par défaut est 8080.
      Les numéros de port valides sont compris entre 1 et 65535. Si la valeur saisie n’est pas comprise dans cette plage, un message d’erreur s’affiche.
   1. Éventuellement, si le serveur sur lequel la passerelle est installée doit communiquer via un proxy, saisissez l’adresse proxy à laquelle la passerelle doit se connecter. Par exemple : `http://myorgname.corp.contoso.com:80`.  Si le champ est vide, la passerelle essaie de se connecter à Internet directement.  Si votre serveur proxy requiert une authentification, entrez un nom d'utilisateur et un mot de passe.<br><br> ![Configuration du proxy de l’assistant d’installation de la passerelle](./media/gateway/gateway-wizard02.png)<br>   
   1. Cliquez sur **Suivant**.
1. Si vous n’avez pas activé Microsoft Update, la page Microsoft Update s’affiche, et vous pouvez choisir de l’activer. Effectuez une sélection, puis cliquez sur **Suivant**. Sinon, passez à l’étape suivante.
1. Dans la page **Dossier de destination**, conservez le dossier par défaut C:\Program Files\OMS Gateway ou entrez l’emplacement où vous voulez installer la passerelle, puis cliquez sur **Suivant**.
1. Dans la page **Prêt pour l’installation**, cliquez sur **Installer**. Un contrôle de compte utilisateur peut apparaître et demander une autorisation d’installation. Dans ce cas, cliquez sur **Oui**.
1. Une fois l’installation terminée, cliquez sur **Terminer**. Vous pouvez vérifier que le service est en cours d’exécution en ouvrant le composant logiciel enfichable services.msc et en vérifiant que **Passerelle Log Analytics** apparaît dans la liste des services et que son statut est défini sur **Exécution en cours**.<br><br> ![Services : passerelle Log Analytics](./media/gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configuration de l’équilibrage de la charge réseau 
Vous pouvez configurer la passerelle pour la haute disponibilité à l’aide de l’équilibrage de charge réseau Microsoft ou basé sur le matériel.  L’équilibreur de charge gère le trafic en redirigeant les connexions demandées à partir des agents Log Analytics ou des serveurs d’administration Operations Manager sur ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

Pour apprendre à concevoir et déployer un cluster d’équilibrage de charge réseau Windows Server 2016, consultez [Équilibrage de charge réseau](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Les étapes suivantes décrivent comment configurer un cluster d’équilibrage de charge réseau Microsoft.  

1. Connectez-vous au serveur Windows qui est membre du cluster d’équilibrage de charge réseau avec un compte d’administration.  
1. Ouvrez le Gestionnaire d’équilibrage de charge réseau dans le Gestionnaire de serveur, cliquez sur **Outils**, puis sur **Gestionnaire d’équilibrage de charge réseau**.
1. Pour vous connecter à un serveur de passerelle Log Analytics sur lequel Microsoft Monitoring Agent est installé, faites un clic droit sur l’adresse IP du cluster, puis cliquez sur **Ajouter l’hôte au cluster**.<br><br> ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster](./media/gateway/nlb02.png)<br> 
1. Entrez l’adresse IP du serveur de passerelle que vous voulez connecter.<br><br> ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster : Connexion](./media/gateway/nlb03.png) 
    
## <a name="configure-log-analytics-agent-and-operations-manager-management-group"></a>Configurer l’agent Log Analytics et le groupe d’administration Operations Manager
La section suivante contient des étapes expliquant la configuration d’agents Log Analytics connectés directement, d’un groupe d’administration Operations Manager ou Runbook Worker hybride Azure Automation avec la passerelle Log Analytics pour communiquer avec Azure Automation ou Log Analytics.  

### <a name="configure-standalone-log-analytics-agent"></a>Configuration d’un agent Log Analytics autonome
Pour comprendre les exigences et la procédure à suivre pour installer l’agent Log Analytics sur des ordinateurs Windows connectés directement à Log Analytics, consultez [Connecter des ordinateurs Windows à Log Analytics](agent-windows.md) ou pour les ordinateurs Linux, consultez [Connecter des ordinateurs Linux à Log Analytics](../../log-analytics/log-analytics-quick-collect-linux-computer.md). Au lieu de spécifier un serveur proxy lors de la configuration de l’agent, vous remplacez cette valeur par l’adresse IP du serveur de passerelle Log Analytics et son numéro de port.  Si vous avez déployé plusieurs serveurs de passerelle derrière un équilibreur de charge réseau, la configuration de proxy de l’agent Log Analytics est l’adresse IP virtuelle de l’équilibreur de charge réseau.  

Pour plus d’informations relatives au traitement de Runbook Worker hybride Automation, consultez [Déployer Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Configurer Operations Manager : tous les agents utilisent le même serveur proxy
Vous configurez Operations Manager pour ajouter le serveur de passerelle.  La configuration du proxy Operations Manager est automatiquement appliquée à tous les agents effectuant un rapport à Operations Manager, même si le paramètre est vide.  

Pour que la passerelle prenne en charge Operations Manager, vous devez disposer des éléments suivants :

* Microsoft Monitoring Agent (version de l’agent : **8.0.10900.0** ou version ultérieure) installé sur le serveur de passerelle et configuré pour des espaces de travail Log Analytics avec lequel vous voulez communiquer.
* La passerelle doit disposer d’une connectivité Internet, ou être connectée à un serveur proxy connecté à Internet.

> [!NOTE]
> Si vous ne spécifiez pas de valeur pour la passerelle, les valeurs vides sont transférées à tous les agents.
> 

S’il s’agit de la première fois que votre groupe d’administration Operations Manager est inscrit sur un espace de travail Log Analytics, l’option pour spécifier la configuration du proxy pour le groupe d’administration n’est pas disponible dans la console Opérations.  Le groupe d’administration doit être correctement inscrit auprès du service avant que cette option soit disponible.  Vous devez mettre à jour la configuration du proxy système à l’aide de Netsh sur le système à partir duquel vous exécutez la console Opérations pour configurer l’intégration et tous les serveurs d’administration du groupe d’administration.  

1. Ouvrez une invite de commande avec élévation de privilèges.
   a. Allez à **Démarrer** et entrez **cmd**.
   b. Cliquez avec le bouton de droite sur **Invite de commande** et sélectionnez Exécuter en tant qu’administrateur**.
1. Saisissez la commande suivante, puis appuyez sur **Entrée** :

    `netsh winhttp set proxy <proxy>:<port>`

À l’issue de l’intégration avec Log Analytics, vous pouvez supprimer la modification en exécutant `netsh winhttp reset proxy`puis, utilisez l’option **Configurer le serveur proxy** dans la console Opérations pour spécifier le serveur de la passerelle Log Analytics. 

1. Ouvrez la console Operations Manager, puis, sous **Operations Management Suite**, cliquez sur **Connexion**, puis sur **Configurer le serveur proxy**.<br><br> ![Operations Manager – Configurer le serveur proxy](./media/gateway/scom01.png)<br> 
1. Sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite**, puis saisissez l’adresse IP du serveur de la passerelle Log Analytics ou l’adresse IP virtuelle de l’équilibrage de charge réseau. Vérifiez que vous démarrez avec le préfixe `http://`.<br><br> ![Operations Manager – Adresse du serveur proxy](./media/gateway/scom02.png)<br> 
1. Cliquez sur **Terminer**. Votre groupe d’administration Operations Manager est maintenant configuré pour communiquer via le serveur de passerelle pour le service Log Analytics.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Configurer Operations Manager : des agents spécifiques utilisent le serveur proxy
Pour les environnements complexes ou volumineux, vous pouvez souhaiter que seuls des serveurs spécifiques (ou groupes) utilisent le serveur de passerelle Log Analytics.  Pour ces serveurs, vous ne pouvez pas mettre à jour l’agent Operations Manager directement, cette valeur étant remplacée par la valeur globale du groupe d’administration.  Au lieu de cela, vous devez remplacer la règle utilisée pour transférer ces valeurs.  

> [!NOTE] 
> Cette même technique de configuration peut être utilisée pour autoriser l’utilisation de plusieurs serveurs de passerelle Log Analytics dans votre environnement.  Par exemple, vous pouvez exiger que des serveurs de passerelle Log Analytics spécifiques soient spécifiés par région.
>  

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Création**.  
1. Dans l’espace de travail Création, sélectionnez **Règles**, puis cliquez sur **Étendue** dans la barre d’outils Operations Manager. Si ce bouton n’est pas disponible, vérifiez que vous avez bien sélectionné un objet, et non un dossier, dans le volet Analyse. La boîte de dialogue **Objets du pack de gestion de l’étendue** affiche une liste des classes, groupes ou objets couramment ciblés. 
1. Saisissez **Service d’intégrité** dans le champ **Rechercher** et sélectionnez-le dans la liste.  Cliquez sur **OK**.  
1. Recherchez la règle **Règle du paramètre proxy Advisor** et, dans la barre d’outils de la console Operations, cliquez sur **Remplace**, puis pointez sur **Remplacer la règle\D’un objet de classe spécifique : Service d’intégrité**, puis sélectionnez un objet spécifique dans la liste.  Si vous le souhaitez, vous pouvez créer un groupe personnalisé qui contient l’objet de service d’intégrité des serveurs auxquels vous souhaitez appliquer ce remplacement, puis appliquer le remplacement à ce groupe.
1. Dans la boîte de dialogue **Propriétés du remplacement**, cliquez pour placer une coche dans la colonne **Remplacer** en regard du paramètre **WebProxyAddress**.  Dans le champ **Remplacer la valeur**, entrez l’URL du serveur de passerelle Log Analytics pour vérifier que vous démarrez avec le préfixe `http://`.  

    >[!NOTE]
    > Vous n’avez pas besoin d’activer la règle, car elle est déjà gérée automatiquement avec un remplacement contenu dans le pack d’administration Microsoft System Center Advisor Secure Reference Override ciblant le groupe Microsoft System Center Advisor Monitoring Server.
    >   

1. Sélectionnez un pack d’administration dans la liste **Sélectionner un pack d’administration de destination** ou créez un pack de gestion non scellé en cliquant sur **Nouveau**. 
1. Lorsque vous effectuez vos modifications, cliquez sur **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Configuration pour les workers hybrides Automation
Si vous avez des Runbook Workers hybrides Automation dans votre environnement, les étapes suivantes fournissent des solutions manuelles de contournement temporaires pour configurer la passerelle afin qu’elle les prenne en charge.

Dans les étapes suivantes, vous devez connaître la région Azure dans laquelle réside le compte Automation. Pour localiser l’emplacement :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Sélectionnez le service Azure Automation.
1. Sélectionnez le compte Azure Automation approprié.
1. Regardez sa région sous **Emplacement**.<br><br> ![Portail Azure – Emplacement du compte Automation](./media/gateway/location.png)  

Utilisez les tableaux suivants pour identifier l’URL pour chaque emplacement :

**URL de service de données d’exécution de la tâche**

| **location** | **URL** |
| --- | --- |
| USA Centre Nord |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europe Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| USA Centre Sud |scus-jobruntimedata-prod-su1.azure-automation.net |
| USA Est 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canada central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europe Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Asie Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde Centre |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australie |ase-jobruntimedata-prod-su1.azure-automation.net |

**URL de service de l’agent**

| **location** | **URL** |
| --- | --- |
| USA Centre Nord |ncus-agentservice-prod-1.azure-automation.net |
| Europe Ouest |we-agentservice-prod-1.azure-automation.net |
| USA Centre Sud |scus-agentservice-prod-1.azure-automation.net |
| USA Est 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canada central |cc-agentservice-prod-1.azure-automation.net |
| Europe Nord |ne-agentservice-prod-1.azure-automation.net |
| Asie Sud-Est |sea-agentservice-prod-1.azure-automation.net |
| Inde Centre |cid-agentservice-prod-1.azure-automation.net |
| Japon |jpe-agentservice-prod-1.azure-automation.net |
| Australie |ase-agentservice-prod-1.azure-automation.net |

Si votre ordinateur est automatiquement inscrit en tant que Runbook Worker hybride pour l’application de correctifs à l’aide de la solution de gestion des mises à jour, suivez ces étapes :

1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle Log Analytics. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Redémarrez le service de passerelle Log Analytics en utilisant la cmdlet PowerShell suivante : `Restart-Service OMSGatewayService`

Si votre ordinateur est intégré à Azure Automation à l’aide de la cmdlet d’inscription Runbook Worker hybride, suivez ces étapes :

1. Ajoutez l’URL d’enregistrement de service de l’agent à la liste d’hôtes autorisés sur la passerelle Log Analytics. Par exemple : `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle Log Analytics. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Redémarrez le service de passerelle Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles
Les applets de commande peuvent vous aider à effectuer des tâches qui sont nécessaires pour mettre à jour les paramètres de configuration de la passerelle Log Analytics. Avant de les utiliser, veillez à suivre les instructions suivantes :

1. Installez la passerelle Log Analytics (MSI).
1. Ouvrez une fenêtre de console PowerShell.
1. Pour importer le module, saisissez cette commande : `Import-Module OMSGateway`
1. Si aucune erreur ne s’est produite lors des étapes précédentes, cela signifie que le module a été importé, et que les cmdlets peuvent être utilisées. Saisissez `Get-Module OMSGateway`
1. Une fois que vous avez apporté des modifications à l’aide des applets de commande, assurez-vous de redémarrer le service de passerelle.

Si vous obtenez une erreur à l’étape 3, cela signifie que le module n’a pas été importé. L’erreur peut se produire lorsque PowerShell ne trouve pas le module. Vous pouvez le trouver dans le chemin d’installation de la passerelle : *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Applet de commande** | **Paramètres** | **Description** | **Exemple** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Clé |Récupère la configuration du service |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Clé (obligatoire) <br> Valeur |Modifie la configuration du service |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Récupère l’adresse du proxy de relais (en amont) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresse<br> Nom d’utilisateur<br> Mot de passe |Définit l’adresse (et les informations d’identification) du proxy de relais (en amont) |1. Définir un proxy relais et des informations d’identification :<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Définir un proxy relais n’exigeant pas d’authentification : `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Effacer le paramètre de proxy relais :<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Récupère l’hôte actuellement autorisé (uniquement l’hôte autorisé configuré localement, pas les hôtes autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hôte (obligatoire) |Ajoute l’hôte à la liste d’éléments autorisés |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hôte (obligatoire) |Supprime l’hôte de la liste d’éléments autorisés |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Ajoute l’objet du certificat client à la liste d’éléments autorisés |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Supprime l’objet du certificat client de la liste d’éléments autorisés |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Récupère les objets de certificat client actuellement autorisés (uniquement les objets configurés localement, pas les objets autorisés téléchargés automatiquement) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Résolution de problèmes
Pour collecter des événements journalisés par la passerelle, vous devez également avoir installé l’agent Log Analytics.<br><br> ![Observateur d’événements : journal de la passerelle Log Analytics](./media/gateway/event-viewer.png)

**Identifiants et descriptions des événements de la passerelle Log Analytics**

Le tableau suivant montre les identifiants et descriptions des événements du journal de la passerelle Log Analytics.

| **Identifiant** | **Description** |
| --- | --- |
| 400 |Toute erreur d’application ne disposant pas d’un identifiant spécifique |
| 401 |Configuration incorrecte. Par exemple : listenPort = "texte" au lieu d’un nombre entier |
| 402 |Exception lors de l’analyse des messages de liaison TLS |
| 403 |Erreur de mise en réseau. Par exemple : impossible de se connecter au serveur cible |
| 100 |Informations générales |
| 101 |Le service a démarré |
| 102 |Le service s’est arrêté |
| 103 |Réception d’une commande HTTP CONNECT de la part du client |
| 104 |L’élément n’est pas une commande HTTP CONNECT |
| 105 |Le serveur de destination n’est pas dans la liste autorisée ou le port de destination n’est pas un port sécurisé (443) <br> <br> Vérifiez que l’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 105 |ERREUR TcpConnection – Certificat client non valide : CN=passerelle <br><br> Assurez-vous que : <br>    <br> &#149; Vous utilisez une passerelle ayant le numéro de version 1.0.395.0 ou supérieur. <br> &#149; L’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 106 |La passerelle Log Analytics prend uniquement en charge le protocole TLS 1.0, 1.1 et 1.2.  Elle ne prend pas en charge le protocole SSL. Pour toute version de protocole TLS/SSL non prise en charge, la passerelle Log Analytics génère un ID d’événement 106.|
| 107 |La session TLS a été vérifiée |

**Compteurs de performances à collecter**

Le tableau suivant montre les compteurs de performances disponibles pour la passerelle Log Analytics. Vous pouvez ajouter des compteurs à l’aide de l’Analyseur de performances.

| **Nom** | **Description** |
| --- | --- |
| Passerelle Log Analytics/Connexion du client actif |Nombre de connexions du réseau client actif (TCP) |
| Passerelle Log Analytics/Nombre d’erreurs |Nombre d’erreurs |
| Passerelle Log Analytics/Client connecté |Nombre de clients connectés |
| Passerelle Log Analytics/Nombre de rejets |Nombre de rejets dus à des erreurs de validation de TLS |

![Compteurs de performances de la passerelle Log Analytics](./media/gateway/counters.png)

## <a name="get-assistance"></a>Obtenir de l’aide
Lorsque vous êtes connecté au Portail Azure, vous pouvez créer une demande d’assistance avec la passerelle Log Analytics ou tout autre service Azure ou toute fonctionnalité d’un service.
Pour demander de l’aide, cliquez sur le symbole en forme de point d’interrogation dans le coin supérieur droit du portail, puis cliquez sur **Nouvelle demande de support**. Ensuite, remplissez le nouveau formulaire de demande de support.

![Nouvelle demande de support](./media/gateway/support.png)

## <a name="next-steps"></a>Étapes suivantes
[Ajoutez des sources de données](../../azure-monitor/platform/agent-data-sources.md) pour collecter des données à partir de vos sources connectées et les stocker dans votre espace de travail Log Analytics.
