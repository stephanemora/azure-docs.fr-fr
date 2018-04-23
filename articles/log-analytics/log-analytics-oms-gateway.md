---
title: Connecter des ordinateurs à l’aide de la passerelle OMS | Microsoft Docs
description: Connectez vos appareils et les ordinateurs contrôlés par Operations Manager à la passerelle OMS pour leur permettre d’envoyer des données aux services Azure Automation et Log Analytics lorsqu’ils n’ont pas accès à Internet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Connecter des ordinateurs sans accès Internet à l’aide de la passerelle OMS
Ce document décrit comment configurer la communication avec Azure Automation et Log Analytics à l’aide de la passerelle OMS lorsque les ordinateurs directement connectés ou des ordinateurs surveillés par Operations Manager n’ont pas accès à Internet. La passerelle OMS est un proxy de transfert HTTP qui prend en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT. Elle peut collecter des données et les envoyer à Azure Automation ainsi qu’à Log Analytics pour le compte d’ordinateurs sans accès à internet.  

La passerelle OMS prend en charge :

* Runbooks Workers hybrides Azure Automation  
* Ordinateurs Windows avec Microsoft Monitoring Agent directement connecté à un espace de travail Log Analytics
* Ordinateurs Linux avec Agent OMS pour Linux directement connecté à un espace de travail Log Analytics  
* System Center Operations Manager 2012 SP1 avec UR7, Operations Manager 2012 R2 avec UR3, Operations Manager 2016 et le groupe de gestion Operations Manager version 1801 intégré à Log Analytics  

Si vos stratégies de sécurité informatique ne permettent pas à certains ordinateurs de votre réseau de se connecter à Internet, comme des appareils de point de vente ou des serveurs prenant en charge des services informatiques, mais que vous devez les connecter à Azure Automation ou Log Analytics pour les gérer et les surveiller, ils peuvent être configurés pour communiquer directement avec la plateforme OMS.

 Si ces ordinateurs sont configurés avec l’agent OMS pour se connecter directement à un espace de travail Log Analytics, tous les ordinateurs communiquent plutôt avec la passerelle OMS. Celle-ci transfère les données des agents directement au service. Elle n’analyse des données pendant leur transit.

Lorsqu’un groupe d’administration Operations Manager est intégré à Log Analytics, les serveurs d’administration peuvent être configurés pour se connecter à la passerelle OMS pour recevoir des informations de configuration et envoyer les données collectées. Les agents Operations Manager envoient certaines données, comme les alertes Operations Manager, les évaluations de la configuration, les espaces d’instance et les données de capacité, au serveur d’administration. D’autres données volumineuses, telles que les journaux IIS, les données de performances et les événements de sécurité, sont envoyées directement à la passerelle OMS.  

Si vous avez déployé un ou plusieurs serveurs de passerelle Operations Manager dans une zone DMZ ou un autre réseau isolé pour surveiller des systèmes non fiables, ils ne peuvent pas communiquer avec une passerelle OMS. Les serveurs de passerelle Operations Manager peuvent uniquement générer des rapports sur un serveur d’administration. 

Lorsqu’un groupe d’administration Operations Manager est configuré pour communiquer avec la passerelle OMS, les informations de configuration de proxy sont automatiquement distribuées à tous les ordinateurs gérés par agent qui sont configurés pour collecter des données pour Log Analytics, même si le paramètre est vide.    

Pour assurer une haute disponibilité aux groupes directement connectés ou Operations Management qui communiquent avec Log Analytics via la passerelle OMS, vous pouvez utiliser l’équilibrage de charge au niveau du réseau pour rediriger et distribuer le trafic entre plusieurs serveurs de passerelle OMS. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers un autre nœud disponible.  

Nous recommandons d’installer l’agent OMS sur l’ordinateur exécutant le logiciel de passerelle OMS pour surveiller celle-ci et analyser les données de performance ou d’événement. En outre, l’agent permet à la passerelle OMS d’identifier les points de terminaison de service dont il a besoin pour communiquer.

Tous les agents doivent disposer d’une connexion réseau à leur passerelle, afin qu’ils puissent automatiquement échanger des avec la passerelle. Nous déconseillons d’installer une passerelle sur un contrôleur de domaine.

Le diagramme suivant affiche le flux de données entre les agents directs et Azure Automation avec Log Analytics utilisant le serveur de passerelle. La configuration du proxy de l’agent doit utiliser le même port que la passerelle OMS utilise pour communiquer avec le service.  

![Diagramme de communication directe entre l’agent et les services](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Le diagramme suivant illustre le flux de données entre un groupe d’administration Operations Manager et Log Analytics.   

![diagramme de communication entre Operations Manager et Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Prérequis


Lorsque vous désignez un ordinateur pour exécuter la passerelle d’OMS, assurez-vous qu’il présente la configuration suivante :

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET Framework 4.5
* Au minimum un processeur 4 cœurs et 8 Go de mémoire 

### <a name="language-availability"></a>Langues disponibles

La passerelle OMS est disponible dans les langues suivantes :

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
La passerelle OMS ne prend en charge que le protocole TLS version 1.0, 1.1 et 1.2.  Elle ne prend pas en charge le protocole SSL.

### <a name="supported-number-of-agent-connections"></a>Nombre de connexion d’agent prises en charge
Le tableau suivant indique le nombre d’agents pris en charge, qui communiquent avec un serveur de passerelle. Cette prise en charge repose sur des agents qui chargent environ 200 Ko de données toutes les 6 secondes. Le volume de données par agent testé est d’environ 2,7 Go par jour.

|Passerelle |Nombre approximatif d’agents pris en charge|  
|--------|----------------------------------|  
|- Processeur : Intel XEON CPU E5-2660 v3 @ 2,6 GHz 2 cœurs<br> - Mémoire : 4 Go<br> Bande passante réseau : 1 Go/s| 600|  
|- Processeur : Intel XEON CPU E5-2660 v3 @ 2,6 GHz 4 cœurs<br> - Mémoire : 8 Go<br> Bande passante réseau : 1 Go/s| 1 000|  

## <a name="download-the-oms-gateway"></a>Télécharger la passerelle OMS

Il existe deux méthodes pour obtenir la dernière version du fichier de configuration de la passerelle OMS.

1. Téléchargez-la à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Téléchargez-la à partir du Portail Azure. Une fois connecté au Portail Azure, procédez comme suit :  

   1. Parcourez la liste des services, puis sélectionnez **Log Analytics**.  
   2. Sélectionnez un espace de travail.
   3. Dans le panneau de votre espace de travail, sous **Général**, sélectionnez **Démarrage rapide**.
   4. Sous **Choisir une source de données pour se connecter à l’espace de travail**, sélectionnez **Ordinateurs**.
   5. Dans le panneau **Agent direct**, sélectionnez **Télécharger la passerelle OMS**.
   
    ![Télécharger la passerelle OMS](./media/log-analytics-oms-gateway/download-gateway.png)

- OU - 

   1. Dans le panneau de votre espace de travail, sous **Paramètres**, sélectionnez **Paramètres avancés**.
   2. Sélectionnez **Sources connectées** > **Serveurs Windows**. Puis, sélectionnez **Télécharger la passerelle OMS**.

## <a name="install-the-oms-gateway"></a>Installer la passerelle OMS

Pour installer une passerelle, procédez comme suit. Si vous avez installé la version antérieure, appelée *Log Analytics Forwarder*, elle sera mise à niveau vers cette version.  

1. Dans le dossier de destination, sélectionnez **OMS Gateway.msi**.
2. Sur la page d’**accueil**, sélectionnez **Suivant**.

 ![Assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. Dans la page **Contrat de licence**, sélectionnez **J’accepte les termes du contrat de licence**. Sélectionnez ensuite **Suivant**.

4. Sur la page relative au **port et à l’adresse proxy** :

   a. Saisissez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation configure une règle entrante avec ce numéro de port sur le pare-feu Windows. La valeur par défaut est 8080. Les numéros de port valides sont compris entre 1 et 65535. Si la valeur saisie n’est pas comprise dans cette plage, un message d’erreur s’affiche.

   b. Éventuellement, si le serveur sur lequel la passerelle est installée doit communiquer via un proxy, saisissez l’adresse du proxy à laquelle la passerelle doit se connecter. La capture d’écran suivante montre `http://myorgname.corp.contoso.com:80` comme exemple. Si vous laissez ce champ vide, la passerelle tente de se connecter directement à Internet.  Si votre serveur proxy requiert une authentification, entrez un nom d’utilisateur et un mot de passe.
   
    ![Configuration du proxy de l’assistant d’installation de la passerelle](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Sélectionnez **Suivant**.

5. Si vous n’avez pas activé Microsoft Update, la page Microsoft Update s’affiche et vous pouvez choisir de l’activer. Effectuez une sélection, puis cliquez sur **Suivant**. Sinon, passez à l’étape suivante.

6. Dans la page **Dossier de destination**, conservez le dossier par défaut **C:\Program Files\OMS Gateway** ou indiquez l’emplacement où vous voulez installer la passerelle OMS. Sélectionnez ensuite **Suivant**.

7. Sur la page **Prêt pour l’installation**, sélectionnez **Installer**. Un contrôle de compte utilisateur peut apparaître et demander une autorisation d’installation. Si vous obtenez une demande d’autorisation, sélectionnez **Oui**.

8. Une fois l’installation terminée, sélectionnez **Terminer**. Vous pouvez vérifier que le service est en cours d’exécution en ouvrant le composant logiciel enfichable services.msc puis en vérifiant que **Passerelle OMS** apparaît dans la liste des services et que son statut est **Exécution en cours**.

    ![Services – Passerelle OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configuration de l’équilibrage de la charge réseau 
Vous pouvez configurer la passerelle afin qu’elle offre une haute disponibilité à l’aide de l’équilibrage de la charge réseau. Utilisez l’équilibrage de charge réseau Microsoft ou des équilibreurs de charge matériels.  L’équilibreur de charge gère le trafic en redirigeant les connexions demandées à partir des agents OMS ou des serveurs d’administration Operations Manager sur ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

Pour apprendre à concevoir et déployer un cluster d’équilibrage de charge réseau Windows Server 2016, consultez [Équilibrage de charge réseau](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Les étapes suivantes décrivent comment configurer un cluster d’équilibrage de charge réseau Microsoft.  

1.  Connectez-vous au serveur Windows membre du cluster d’équilibrage de charge réseau, avec un compte d’administration.  

2.  Dans le Gestionnaire de serveur, ouvrez le Gestionnaire d’équilibrage de charge réseau. Sélectionnez **Outils**, puis **Gestionnaire d’équilibrage de charge réseau**.

3. Pour vous connecter à un serveur de passerelle OMS sur lequel Microsoft Monitoring Agent est installé, faites un clic droit sur l’adresse IP du cluster, puis sélectionnez **Ajouter l’hôte au cluster**.

 ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster](./media/log-analytics-oms-gateway/nlb02.png)

4. Entrez l’adresse IP du serveur de passerelle auquel vous voulez vous connecter.

    ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster : Connexion](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Configurer l’agent OMS et le groupe d’administration Operations Manager
Les sections suivantes de cet article décrivent des étapes sur la configuration d’agents OMS connectés directement, d’un groupe d’administration Operations Manager ou de Runbook Worker hybrides Azure Automation avec la passerelle OMS pour communiquer avec Azure Automation ou Log Analytics.  

Pour comprendre les exigences et les étapes relatives à l’installation de l’agent OMS sur les ordinateurs Windows qui se connectent directement à Lob Analytics, consultez [Collecter des données d’ordinateurs dans un environnement avec Log Analytics](log-analytics-concept-hybrid.md#prerequisites).

 Pour les ordinateurs Linux, consultez [Connecter des ordinateurs Linux à Log Analytics](log-analytics-quick-collect-linux-computer.md). Pour plus d’informations relatives au traitement de Runbook Worker hybride Automation, consultez [Déployer Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Configurer l’agent OMS autonome
Pour plus d’informations sur la configuration d’un agent pour utiliser un serveur proxy (en l’occurrence, la passerelle), consultez [Collecter des données d’ordinateurs dans un environnement avec Log Analytics](log-analytics-concept-hybrid.md#prerequisites). Si vous avez déployé plusieurs serveurs de passerelle derrière un équilibreur de charge réseau, la configuration de proxy de l’agent OMS est l’adresse IP virtuelle de l’équilibrage de charge réseau :

![Propriétés de Microsoft Monitoring Agent – Paramètres de proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Configurer Operations Manager : tous les agents utilisent le même serveur proxy
Vous configurez Operations Manager pour ajouter le serveur de passerelle.  La configuration du proxy Operations Manager est automatiquement appliquée à tous les agents générant un rapport pour Operations Manager, même si le paramètre est vide.  

Pour utiliser la passerelle d’OMS pour prendre en charge Operations Manager, les composants suivants doivent être en place :

* Microsoft Monitoring Agent (version de l’agent : **8.0.10900.0** ou version ultérieure) installé sur le serveur de passerelle et configuré pour des espaces de travail Log Analytics avec lequel vous voulez communiquer.

* Une passerelle qui dispose d’une connexion à Internet ou une connexion à un serveur proxy connecté à Internet.

> [!NOTE]
> Si vous ne spécifiez pas de valeur pour la passerelle, les valeurs vides sont transférées à tous les agents.
> 

S’il s’agit de la première inscription de votre groupe d’administration Operations Manager dans un espace de travail Log Analytics, la possibilité de spécifier la configuration du proxy pour le groupe d’administration n’est pas disponible dans la console Opérations. 

Le groupe d’administration doit être correctement inscrit auprès du service avant que cette option soit disponible. Mettez à jour la configuration du proxy système à l’aide de Netsh sur le système à partir duquel vous exécutez la console Opérations pour et tous les serveurs d’administration du groupe d’administration.

1. Ouvrez une invite de commandes avec élévation de privilèges.

    a. Cliquez sur **Démarrer**. Ensuite, tapez **cmd**.
    
    b. Cliquez avez le bouton droit sur **Invite de commandes**. Puis sélectionnez **Exécuter en tant qu’administrateur**.
    
2. Entrez la commande suivante, puis sélectionnez **Entrée** :

    `netsh winhttp set proxy <proxy>:<port>`

Après avoir terminé l’intégration avec Log Analytics, vous pouvez supprimer la modification en exécutant `netsh winhttp reset proxy`. Ensuite, utilisez l’option **Configurer le serveur proxy** dans la console Opérations pour spécifier le serveur de passerelle OMS. 

1. Ouvrez la console Operations Manager. Sous **Operations Management Suite**, sélectionnez **Connexion**. Ensuite, sélectionnez **Configurer le serveur proxy**.

    ![Operations Manager : Configurer le serveur proxy](./media/log-analytics-oms-gateway/scom01.png)

2. Sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite**. Entrez l’adresse IP du serveur de passerelle OMS ou l’adresse IP virtuelle de l’équilibreur de charge réseau. Vérifiez que vous démarrez avec le préfixe `http://`.

    ![Operations Manager - Adresse du serveur proxy](./media/log-analytics-oms-gateway/scom02.png)

3. Sélectionnez **Terminer**. Votre groupe d’administration Operations Manager est maintenant configuré pour communiquer via le serveur de passerelle pour le service Log Analytics.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Configurer Operations Manager : des agents spécifiques utilisent le serveur proxy
Pour les environnements complexes ou volumineux, vous pouvez souhaiter que certains serveurs (ou groupes) n’utilisent que le serveur de passerelle OMS. Pour ces serveurs, vous ne pouvez pas mettre à jour l’agent Operations Manager directement, cette valeur étant remplacée par la valeur globale du groupe d’administration. Au lieu de cela, remplacez la règle utilisée pour transférer ces valeurs.  

> [!NOTE] 
> Cette même technique de configuration peut être utilisée pour autoriser l’utilisation de plusieurs serveurs de passerelle OMS dans votre environnement. Par exemple, vous pouvez exiger que certains serveurs de passerelle OMS spécifiques soient spécifiés par région.
>  

1. Ouvrez la console Operations Manager, puis sélectionnez l’espace de travail **Création**.

2. Dans l’espace de travail Création, sélectionnez **Règles**. Puis sélectionnez le bouton **Étendue** dans la barre d’outils Operations Manager. Si ce bouton n’est pas disponible, vérifiez que vous avez sélectionné un objet, et non un dossier, dans le volet **Analyse**. La boîte de dialogue **Objets du pack de gestion de l’étendue** affiche une liste des classes, groupes ou objets couramment ciblés. 

3. Dans le champ **Rechercher**, entrez **Service de contrôle d’intégrité**. Ensuite, sélectionnez-le dans la liste. Sélectionnez **OK**.  

4. Recherchez la règle **Advisor Proxy Setting Rule (Règle de configuration d’Advisor Proxy)**. Dans la barre d’outils de la console Operations, sélectionnez **Remplacements**. Ensuite, sélectionnez **Remplacer la règle\Pour un objet spécifique de la classe : Service de contrôle d’intégrité**. 

5. Puis sélectionnez un objet spécifique dans la liste. 

6. Le cas échéant, créez un groupe personnalisé qui contient l’objet Service de contrôle d’intégrité des serveurs auxquels vous souhaitez appliquer ce remplacement. Appliquez ensuite ce remplacement à ce groupe.

7. Dans la boîte de dialogue **Propriétés du remplacement**, cliquez pour placer une coche dans la colonne **Remplacer** en regard du paramètre **WebProxyAddress**.  Dans le champ **Remplacer la valeur**, entrez l’URL du serveur de passerelle OMS pour vérifier qu’il démarre avec le préfixe `http://`.  

    >[!NOTE]
    > Vous n’avez pas besoin d’activer la règle. Elle est déjà gérée automatiquement avec un remplacement contenu dans le pack d’administration Microsoft System Center Advisor Secure Reference Override qui cible le groupe Microsoft System Center Advisor Monitoring Server.
    >   

8. Sélectionnez un pack d’administration dans la liste **Sélectionner un pack d’administration de destination** ou créez un pack de gestion non scellé en sélectionnant **Nouveau**. 

9. Lorsque vous effectuez vos modifications, cliquez sur **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Configurer la passerelle OMS pour des Runbook Workers hybrides Automation
Si vous avez des Runbook Workers hybrides Automation dans votre environnement, les étapes suivantes fournissent des solutions manuelles de contournement temporaires pour configurer la passerelle OMS afin qu’elle les prenne en charge.

Dans les étapes suivantes, vous devez connaître la région Azure dans laquelle réside le compte Automation. Pour trouver l’emplacement, effectuez les étapes suivantes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez le service Azure Automation.
3. Sélectionnez le compte Azure Automation approprié.
4. Sous **Emplacement**, consultez la région du compte.

    ![Portail Azure - Emplacement du compte Automation](./media/log-analytics-oms-gateway/location.png)  

Utilisez les tableaux suivants pour identifier l’URL pour chaque emplacement :

**URL de service de données d’exécution de la tâche**

| **Lieu** | **URL** |
| --- | --- |
| Centre-Nord des États-Unis |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canada central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australie |ase-jobruntimedata-prod-su1.azure-automation.net |

**URL de service de l’agent**

| **Lieu** | **URL** |
| --- | --- |
| Centre-Nord des États-Unis |ncus-agentservice-prod-1.azure-automation.net |
| Europe de l'Ouest |we-agentservice-prod-1.azure-automation.net |
| États-Unis - partie centrale méridionale |scus-agentservice-prod-1.azure-automation.net |
| Est des États-Unis 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canada central |cc-agentservice-prod-1.azure-automation.net |
| Europe du Nord |ne-agentservice-prod-1.azure-automation.net |
| Asie du Sud-Est |sea-agentservice-prod-1.azure-automation.net |
| Inde centrale |cid-agentservice-prod-1.azure-automation.net |
| Japon |jpe-agentservice-prod-1.azure-automation.net |
| Australie |ase-agentservice-prod-1.azure-automation.net |

Si votre ordinateur est automatiquement inscrit en tant que Runbook Worker hybride pour l’application de correctifs à l’aide de la solution Update Management, suivez ces étapes :

1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple, tapez : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Redémarrez le service de passerelle OMS en utilisant la cmdlet PowerShell suivante : `Restart-Service OMSGatewayService`

Si votre ordinateur est intégré à Azure Automation à l’aide de l’applet de commande d’inscription Hybrid Runbook Worker, suivez ces étapes :

1. Ajoutez l’URL d’enregistrement de service de l’agent à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple, tapez : `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle OMS. Par exemple, tapez : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Redémarrez le service de passerelle OMS : `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles
Les applets de commande peuvent vous aider à effectuer des tâches qui sont nécessaires pour mettre à jour les paramètres de configuration de la passerelle OMS. Avant de les utiliser, veillez à suivre les étapes suivantes :

1. Installez la passerelle OMS (MSI).
2. Ouvrez une fenêtre de console PowerShell.
3. Pour importer le module, tapez la commande : `Import-Module OMSGateway`.
4. Si aucune erreur ne s’est produite lors des étapes précédentes, cela signifie que le module a été importé, et que les cmdlets peuvent être utilisées. Saisissez `Get-Module OMSGateway`.
5. Après avoir apporté des modifications à l’aide des applets de commande, n’oubliez pas de redémarrer le service de passerelle OMS.

Si vous obtenez une erreur à l’étape 3, cela signifie que le module n’a pas été importé. L’erreur peut se produire lorsque PowerShell ne trouve pas le module. Vous pouvez le trouver dans le dossier d’installation de la passerelle OMS : *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **Applet de commande** | **Paramètres** | **Description** | **Exemple** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Clé |Récupère la configuration du service |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Clé (obligatoire) <br> Valeur |Modifie la configuration du service |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Récupère l’adresse du proxy de relais (en amont) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresse<br> Nom d’utilisateur<br> Mot de passe |Définit l’adresse (et les informations d’identification) du proxy de relais (en amont) |1. Définir un proxy relais et des informations d’identification :<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Définir un proxy relais n’exigeant pas d’authentification : `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Effacer le paramètre de proxy relais :<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Récupère l’hôte actuellement autorisé (uniquement l’hôte autorisé configuré localement, et non les hôtes autorisés téléchargés automatiquement) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hôte (obligatoire) |Ajoute l’hôte à la liste d’éléments autorisés |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hôte (obligatoire) |Supprime l’hôte de la liste d’éléments autorisés |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Ajoute l’objet du certificat client à la liste d’éléments autorisés |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Supprime l’objet du certificat client de la liste d’éléments autorisés |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Récupère les objets de certificat client actuellement autorisés (uniquement les objets configurés localement, et non les objets autorisés téléchargés automatiquement) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Résolution de problèmes
Pour collecter des événements journalisés par la passerelle, vous devez également avoir installé l’agent OMS.

![Observateur d’événements - Journal de la passerelle OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**Identifiants et descriptions des événements de la passerelle OMS**

Le tableau suivant montre les identifiants et descriptions des événements du journal de la passerelle OMS :

| **Identifiant** | **Description** |
| --- | --- |
| 400 |Toute erreur d’application ne disposant pas d’un identifiant spécifique. |
| 401 |Configuration incorrecte. Par exemple : listenPort = "texte" au lieu d’un nombre entier. |
| 402 |Exception lors de l’analyse des messages de liaison TLS. |
| 403 |Erreur de mise en réseau. Par exemple : connexion impossible au serveur cible. |
| 100 |Informations générales. |
| 101 |Le service a démarré. |
| 102 |Le service s’est arrêté. |
| 103 |Réception d’une commande HTTP CONNECT de la part du client. |
| 104 |L’élément n’est pas une commande HTTP CONNECT. |
| 105 |Le serveur de destination ne figure pas dans la liste autorisée ou le port de destination n’est pas un port sécurisé (443). <br> <br> Vérifiez que l’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 105 |ERREUR TcpConnection – Certificat client non valide : CN=passerelle <br><br> Assurez-vous que : <br>    <br> - Vous utilisez une passerelle ayant au moins le numéro de version 1.0.395.0. <br> - L’agent MMA sur votre serveur de passerelle et les agents communiquant avec la passerelle sont connectés au même espace de travail Log Analytics. |
| 106 |La passerelle OMS prend uniquement en charge le protocole TLS 1.0, 1.1 et 1.2.  Elle ne prend pas en charge le protocole SSL. Pour toute version de protocole TLS/SSL non prise en charge, la passerelle OMS génère un ID d’événement 106.|
| 107 |La session TLS a été vérifiée. |

**Compteurs de performances à collecter**

Le tableau suivant montre les compteurs de performances disponibles pour la passerelle OMS. Vous pouvez ajouter des compteurs à l’aide de l’Analyseur de performances.

| **Name** | **Description** |
| --- | --- |
| Passerelle OMS/Connexion du client actif |Nombre de connexions du réseau client actif (TCP) |
| Passerelle OMS/Nombre d’erreurs |Nombre d’erreurs |
| Passerelle OMS/Client connecté |Nombre de clients connectés |
| Passerelle OMS/Nombre de rejets |Nombre de rejets dus à des erreurs de validation de TLS |

![Compteurs de performances de la passerelle OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtenir de l’aide
Lorsque connecté au portail Azure, vous pouvez créer une demande d’assistance avec la passerelle OMS ou tout autre service Azure ou toute fonctionnalité d’un service.

Pour demander de l’aide, sélectionnez le symbole de point d’interrogation dans le coin supérieur droit du portail. Puis sélectionnez **Nouvelle demande de support**. Remplissez le nouveau formulaire de demande de support.

![Nouvelle demande de support](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Étapes suivantes
[Ajoutez des sources de données](log-analytics-data-sources.md) pour collecter des données provenant de vos sources connectées et les stocker dans votre espace de travail Log Analytics.
