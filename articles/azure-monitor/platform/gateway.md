---
title: Connecter des ordinateurs à l’aide de la passerelle d’Analytique de journal | Microsoft Docs
description: Connecter vos appareils et ordinateurs analysés par Operations Manager à l’aide de la passerelle d’Analytique de journal pour envoyer des données à Azure Automation et service d’Analytique de journal lorsqu’ils n’ont pas accès à internet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: 47b589d32accc4a699e7260b9e4b2de4cca58f2b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369613"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Connecter des ordinateurs sans accès à internet à l’aide de la passerelle d’Analytique de journal dans Azure Monitor

>[!NOTE]
>Comme Microsoft Operations Management Suite (OMS) passe à Microsoft Azure Monitor, de la modification de la terminologie. Cet article fait référence à la passerelle OMS en tant que la passerelle Azure Log Analytique. 
>

Cet article décrit comment configurer la communication avec Azure Automation et Azure Monitor à l’aide de la passerelle d’Analytique de journal lorsque les ordinateurs qui sont directement connectés ou qui doivent être analysés par Operations Manager sans accès à internet. 

La passerelle d’Analytique de journal est un proxy de transfert HTTP qui prend en charge le tunneling HTTP à l’aide de la commande HTTP CONNECT. Cette passerelle peut collecter des données et envoyez-le à Azure Automation et un espace de travail Analytique de journal dans Azure Monitor pour les ordinateurs qui ne sont pas connectés à internet.  

La passerelle Log Analytics prend en charge ce qui suit :

* Création de rapports jusqu'à l’Analytique de journal même quatre agents d’espace de travail qui se trouvent derrière lui et qui sont configurés avec Azure Automation Hybrid Runbook Workers.  
* Ordinateurs Windows sur lequel l’agent Microsoft Monitoring Agent est directement connecté à un espace de travail Analytique de journal dans Azure Monitor.
* Ordinateurs Linux sur lesquels un agent d’Analytique de journal pour Linux est directement connecté à un espace de travail Analytique de journal dans Azure Monitor.  
* System Center Operations Manager 2012 SP1 avec UR7, Operations Manager 2012 R2 avec correctif cumulatif 3 ou un groupe d’administration dans Operations Manager 2016 ou version ultérieure qui est intégré avec l’Analytique de journal.  

Certaines stratégies de sécurité informatique ne pas autoriser la connexion internet pour les ordinateurs du réseau. Ces ordinateurs non connectés peut être le point d’appareils de vente (PDV) ou des serveurs prenant en charge des services informatiques, par exemple. Pour connecter ces appareils à Azure Automation ou un espace de travail Analytique de journal afin de pouvoir gérer et surveiller les, configurez-les pour communiquer directement avec la passerelle d’Analytique de journal. La passerelle d’Analytique de journal peut recevoir des informations de configuration et de transférer des données en leur nom. Si les ordinateurs sont configurés avec l’agent d’Analytique de journal pour vous connecter directement à un espace de travail Analytique de journal, les ordinateurs communiquent plutôt avec la passerelle d’Analytique de journal.  

La passerelle d’Analytique de journal transfère les données à partir des agents au service directement. Elle n’analyse pas les données en transit.

Lorsqu’un groupe d’administration Operations Manager est intégré avec l’Analytique de journal, les serveurs d’administration peuvent être configurés pour vous connecter à la passerelle d’Analytique de journal pour recevoir des informations de configuration et envoyer des données collectées, en fonction de la solution que vous avez activé .  Les agents Operations Manager envoient certaines données au serveur d’administration. Par exemple, les agents peuvent envoyer des alertes Operations Manager, données d’évaluation de configuration, données espace d’instance et données de capacité. Autres données volumineuses, telles que les journaux Internet Information Services (IIS), les données de performances et les événements de sécurité, sont envoyées directement à la passerelle d’Analytique de journal. 

Si un ou plusieurs serveurs de passerelle Operations Manager sont déployés pour surveiller les systèmes non fiables dans un réseau de périmètre ou un réseau isolé, ces serveurs ne peuvent pas communiquer avec une passerelle d’Analytique de journal.  Serveurs d’Operations Manager Gateway peuvent signaler uniquement à un serveur d’administration.  Lorsqu’un groupe d’administration Operations Manager est configuré pour communiquer avec la passerelle d’Analytique de journal, les informations de configuration de proxy sont automatiquement distribuées à tous les ordinateurs gérés par agent qui sont configuré pour collecter des données de journal pour Azure Monitor, même si le paramètre est vide.    

Pour fournir une haute disponibilité pour directement connectés ou des groupes de gestion des opérations qui communiquent avec un espace de travail Analytique de journal via la passerelle, utilisez réseau équilibrage de charge (NLB) pour rediriger et distribuer le trafic entre plusieurs serveurs de passerelle. De cette façon, si un serveur de passerelle tombe en panne, le trafic est redirigé vers un autre nœud disponible.  

L’ordinateur qui exécute la passerelle d’Analytique de journal requiert l’agent Windows d’Analytique de journal identifier les points de terminaison de service nécessaires pour communiquer avec la passerelle. L’agent doit également diriger la passerelle à enregistrer dans les espaces de travail de mêmes que les agents ou groupe d’administration Operations Manager derrière la passerelle sont configurés avec. Cette configuration permet à la passerelle et l’agent pour communiquer avec leur espace de travail attribué.

Une passerelle peut être multirésident à jusqu'à quatre espaces de travail. Il s’agit du nombre total d’un agent Windows prend en charge des espaces de travail.  

Chaque agent doit disposer de connectivité réseau vers la passerelle afin que les agents puissent automatiquement échanger vers et à partir de la passerelle. Évitez d’installer la passerelle sur un contrôleur de domaine.

Le diagramme suivant affiche les données circulent entre les agents directs, via la passerelle, à Azure Automation et d’Analytique de journal. La configuration de proxy de l’agent doit correspondre au port configuré avec la passerelle d’Analytique de journal.  

![Diagramme de communication d’agent direct avec les services](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Le diagramme suivant illustre le flux de données entre un groupe d’administration Operations Manager et Log Analytics.   

![Diagramme de communication d’Operations Manager avec l’Analytique de journal](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurer votre système

Les ordinateurs désignés pour exécuter la passerelle d’Analytique de journal doivent avoir la configuration suivante :

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Au moins un processeur 4 cœurs et les 8 Go de mémoire 
* Un [agent Analytique de journal pour Windows](agent-windows.md) qui est configuré pour le rapport à l’espace de travail que les agents qui communiquent via la passerelle

### <a name="language-availability"></a>Langues disponibles

La passerelle d’Analytique de journal est disponible dans les langues suivantes :

- Chinois (simplifié)
- Chinois (traditionnel)
- Tchèque
- Néerlandais
- Anglais
- Anglais
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
La passerelle d’Analytique de journal prend en charge uniquement sécurité TLS (Transport Layer) 1.0, 1.1 et 1.2.  Il ne prend en charge de Secure Sockets Layer (SSL).  Pour garantir la sécurité des données en transit vers Analytique de journal, configurez la passerelle à utiliser au moins TLS 1.2. Les versions antérieures de SSL ou TLS sont vulnérables. Même si elles autorise actuellement la compatibilité descendante, évitez de les utiliser.  

Pour plus d’informations, passez en revue [Envoi sécurisé de données via TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Nombre de connexion d’agent prises en charge
Le tableau suivant présente environ combien d’agents peut communiquer avec un serveur de passerelle. Prise en charge est basée sur les agents qui chargent environ 200 Ko de données toutes les 6 secondes. Pour chaque agent testé, le volume de données est environ 2,7 Go par jour.

|Passerelle |Agents pris en charge (approximatif)|  
|--------|----------------------------------|  
|Processeur : Intel Xeon E5-2660 v3 de processeur \@ 2,6 GHz 2 cœurs<br> Mémoire : 4 Go<br> Bande passante réseau : 1 Gbit/s| 600|  
|Processeur : Intel Xeon E5-2660 v3 de processeur \@ 2,6 GHz, 4 cœurs<br> Mémoire : 8 Go<br> Bande passante réseau : 1 Gbit/s| 1 000|  

## <a name="download-the-log-analytics-gateway"></a>Téléchargement de la passerelle Log Analytics

Obtenir la dernière version du fichier de programme d’installation de passerelle Analytique de journal à partir de le [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) ou le portail Azure.

Pour obtenir la passerelle d’Analytique de journal à partir du portail Azure, procédez comme suit :

1. Parcourez la liste des services, puis sélectionnez **Log Analytics**. 
1. Sélectionnez un espace de travail.
1. Dans le panneau de votre espace de travail, sous **Général**, sélectionnez **Démarrage rapide**. 
1. Sous **Choisir une source de données pour se connecter à l’espace de travail**, sélectionnez **Ordinateurs**.
1. Dans le **Agent Direct** panneau, sélectionnez **passerelle d’Analytique de journal télécharger**.
 
   ![Capture d’écran des étapes pour télécharger la passerelle d’Analytique de journal](./media/gateway/download-gateway.png)

or 

1. Dans le panneau de votre espace de travail, sous **Paramètres**, sélectionnez **Paramètres avancés**.
1. Accédez à **Sources connectées** > **les serveurs Windows** et sélectionnez **passerelle d’Analytique de journal télécharger**.

## <a name="install-the-log-analytics-gateway"></a>Installation de la passerelle Log Analytics

Pour installer une passerelle, procédez comme suit.  (Si vous avez installé une version précédente, appelée redirecteur d’Analytique de journal, il est mis à niveau vers cette version.)

1. Dans le dossier de destination, double-cliquez sur **Log Analytics gateway.msi**.
1. Sur la page d’**accueil**, sélectionnez **Suivant**.

   ![Capture d’écran d’accueil de page dans l’Assistant Installation de passerelle](./media/gateway/gateway-wizard01.png)

1. Sur le **contrat de licence** page, sélectionnez **J’accepte les termes du contrat de licence** pour accepter les termes du contrat de licence de logiciel Microsoft, puis sélectionnez **suivant**.
1. Sur la page relative au **port et à l’adresse proxy** :

   a. Entrez le numéro de port TCP à utiliser pour la passerelle. Le programme d’installation utilise ce numéro de port pour configurer une règle de trafic entrant sur le pare-feu de Windows.  La valeur par défaut est 8080.
      La plage valide le numéro de port est 1 et 65535. Si la valeur saisie n’est pas comprise dans cette plage, un message d’erreur s’affiche.

   b. Si le serveur sur lequel la passerelle est installée doit communiquer via un proxy, saisissez l’adresse proxy à laquelle la passerelle doit se connecter. Par exemple, entrez : `http://myorgname.corp.contoso.com:80`.  Si vous laissez ce champ vide, la passerelle tente de se connecter directement à internet.  Si votre serveur proxy requiert une authentification, entrez un nom d'utilisateur et un mot de passe.

   c. Sélectionnez **Suivant**.

   ![Capture d’écran de configuration du proxy de passerelle](./media/gateway/gateway-wizard02.png)

1. Si vous n’avez pas activé Microsoft Update, la page Microsoft Update s’affiche, et vous pouvez choisir de l’activer. Effectuez une sélection, puis sélectionnez **suivant**. Sinon, passez à l’étape suivante.
1. Sur le **dossier de Destination** page, conservez le dossier par défaut C:\Program Files\OMS Gateway ou entrez l’emplacement où vous souhaitez installer la passerelle. Sélectionnez ensuite **Suivant**.
1. Sur la page **Prêt pour l’installation**, sélectionnez **Installer**. Si le contrôle de compte d’utilisateur demande l’autorisation d’installer, sélectionnez **Oui**.
1. Une fois l’installation terminée, sélectionnez **Terminer**. Pour vérifier que le service est en cours d’exécution, ouvrez le composant logiciel enfichable services.msc et vérifiez que **passerelle OMS** apparaît dans la liste des services et que son état est **en cours d’exécution**.

   ![Capture d’écran des services locaux, montrant la passerelle OMS est en cours d’exécution](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Configuration de l’équilibrage de la charge réseau 
Vous pouvez configurer la passerelle pour la haute disponibilité à l’aide d’équilibrage de charge réseau (NLB) à l’aide de Microsoft [équilibrage de charge réseau (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md), ou les équilibreurs de charge matérielle. L’équilibreur de charge gère le trafic en redirigeant les connexions demandées à partir des agents Log Analytics ou des serveurs d’administration Operations Manager sur ses nœuds. Si un serveur de passerelle tombe en panne, le trafic est redirigé vers d’autres nœuds.

### <a name="microsoft-network-load-balancing"></a>Équilibrage de charge réseau Microsoft
Pour apprendre à concevoir et déployer un cluster d’équilibrage de charge réseau Windows Server 2016, consultez [Équilibrage de charge réseau](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Les étapes suivantes décrivent comment configurer un cluster d’équilibrage de charge réseau Microsoft.  

1. Connectez-vous au serveur Windows qui est membre du cluster d’équilibrage de charge réseau avec un compte d’administration.  
2. Ouvrez le Gestionnaire d’équilibrage de charge réseau dans le Gestionnaire de serveur, cliquez sur **Outils**, puis sur **Gestionnaire d’équilibrage de charge réseau**.
3. Pour vous connecter à un serveur de passerelle Log Analytics sur lequel Microsoft Monitoring Agent est installé, faites un clic droit sur l’adresse IP du cluster, puis cliquez sur **Ajouter l’hôte au cluster**. 

    ![– Gestionnaire d’équilibrage de charge réseau ajouter l’hôte au Cluster](./media/gateway/nlb02.png)
 
4. Entrez l’adresse IP du serveur de passerelle que vous voulez connecter. 

    ![Gestionnaire d’équilibrage de charge réseau – Ajouter l’hôte au cluster : Connecter](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Pour apprendre à concevoir et déployer un équilibreur de charge Azure, consultez [What ' s Azure Load Balancer ?](../../load-balancer/load-balancer-overview.md). Pour déployer un équilibreur de charge de base, suivez les étapes décrites dans ce [quickstart](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) à l’exclusion de la procédure décrite dans la section **créer des serveurs principaux**.   

> [!NOTE]
> Configuration de l’à l’aide de l’équilibreur de charge Azure le **référence SKU de base**, requiert que les machines virtuelles appartiennent à un groupe à haute disponibilité. Pour en savoir plus sur les groupes à haute disponibilité, consultez [gérer la disponibilité des machines virtuelles Windows Azure](../../virtual-machines/windows/manage-availability.md). Pour ajouter des ordinateurs virtuels existants à un groupe à haute disponibilité, reportez-vous à [définir Azure Resource Manager machine virtuelle à haute disponibilité](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Une fois l’équilibrage de charge est créé, un pool principal doit être créé, qui distribue le trafic vers un ou plusieurs serveurs de passerelle. Suivez les étapes décrites dans la section de l’article de démarrage rapide [créer des ressources pour l’équilibrage de charge](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Lors de la configuration de la sonde d’intégrité, il doit être configuré pour utiliser le port TCP du serveur de passerelle. Dynamiquement, la sonde d’intégrité ajoute ou supprime les serveurs de passerelle à partir de la rotation d’équilibrage de charge en fonction de leur réponse aux contrôles d’intégrité. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurer l’agent Log Analytique et groupe d’administration Operations Manager
Dans cette section, vous verrez comment configurer des agents, un groupe d’administration Operations Manager ou Azure Automation Hybrid Runbook Workers Analytique de journal connectés directement avec la passerelle d’Analytique de journal pour communiquer avec Azure Automation ou Log Analytique.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurer un agent d’Analytique de journal autonome
Lorsque vous configurez l’agent d’Analytique de journal, remplacez la valeur du serveur proxy avec l’adresse IP du serveur de passerelle d’Analytique de journal et son numéro de port. Si vous avez déployé plusieurs serveurs de passerelle derrière un équilibreur de charge, la configuration du proxy agent Analytique de journal est l’adresse IP virtuelle de l’équilibreur de charge.  

>[!NOTE]
>Pour installer l’agent d’Analytique de journal sur la passerelle et les ordinateurs Windows qui se connectent directement à l’Analytique de journal, consultez [ordinateurs Windows de se connecter au service d’Analytique de journal dans Azure](agent-windows.md). Pour connecter des ordinateurs Linux, consultez [configurer un agent d’Analytique de journal pour les ordinateurs Linux dans un environnement hybride](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Après avoir installé l’agent sur le serveur de passerelle, configurez-le pour signaler à l’espace de travail ou les agents d’espace de travail qui communiquent avec la passerelle. Si l’agent Windows d’Analytique de journal n’est pas installé sur la passerelle, événement 300 est écrit dans le journal des événements passerelle OMS, indiquant que l’agent doit être installé. Si l’agent est installé mais pas configuré pour rapporter à l’espace de travail que les agents qui communiquent par son intermédiaire, événement 105 est écrit dans le même journal, indiquant que l’agent sur la passerelle doit être configuré pour signaler à l’espace de travail que les agents de ce co mmunicate avec la passerelle.

Après avoir terminé la configuration, redémarrez le service de passerelle OMS pour appliquer les modifications. Sinon, la passerelle rejette les agents qui tentent de communiquer avec l’Analytique de journal et signalent l’événement 105 dans le journal des événements passerelle OMS. Ceci peut également arriver lorsque vous ajoutez ou supprimez un espace de travail de la configuration de l’agent sur le serveur de passerelle.   

Pour plus d’informations pour les workers hybrides Automation, consultez [automatiser des ressources dans votre centre de données ou le cloud à l’aide de Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurer Operations Manager, où tous les agents utilisent le même serveur proxy
La configuration du proxy Operations Manager est automatiquement appliquée à tous les agents générant un rapport pour Operations Manager, même si le paramètre est vide.  

Pour utiliser la passerelle OMS pour prendre en charge d’Operations Manager, vous devez disposer de :

* Microsoft Monitoring Agent (version 8.0.10900.0 ou version ultérieure) installé sur le serveur de passerelle OMS et configuré avec les espaces de travail Analytique de journal mêmes que votre groupe d’administration est configuré pour rapporter à.
* Connexion Internet. Vous pouvez également passerelle OMS doit être connectée à un serveur proxy qui est connecté à internet.

> [!NOTE]
> Si vous ne spécifiez aucune valeur pour la passerelle, les valeurs vides sont transférées à tous les agents.
>

Si votre groupe d’administration Operations Manager est inscrit avec un espace de travail Analytique de journal pour la première fois, vous ne voyez l’option pour spécifier la configuration du proxy pour le groupe d’administration dans la console Opérateur. Cette option est disponible uniquement si le groupe d’administration a été inscrit auprès du service.  

Pour configurer l’intégration, vous devez mettre à jour la configuration du proxy système à l’aide de Netsh sur le système où vous exécutez la console Opérateur et sur tous les serveurs d’administration du groupe d’administration. Procédez comme suit :

1. Ouvrez une invite de commandes avec élévation de privilèges :

   a. Sélectionnez **Démarrer** et entrez **cmd**.  

   b. Avec le bouton droit **invite de commandes** et sélectionnez **exécuter en tant qu’administrateur**.  

1. Entrez la commande suivante :

   `netsh winhttp set proxy <proxy>:<port>`

À l’issue de l’intégration avec l’Analytique de journal, supprimez la modification en exécutant `netsh winhttp reset proxy`. Puis, dans la console Opérateur, utilisez le **configurer le serveur proxy** option pour spécifier le serveur de passerelle Analytique de journal. 

1. Dans la console Operations Manager, sous **Operations Management Suite**, sélectionnez **connexion**, puis sélectionnez **configurer le serveur Proxy**.

   ![Capture d’écran d’Operations Manager, montrant la sélection de configurer le serveur Proxy](./media/gateway/scom01.png)

1. Sélectionnez **utiliser un serveur proxy pour accéder à Operations Management Suite** , puis entrez l’adresse IP du serveur de passerelle Analytique de journal ou l’adresse IP virtuelle de l’équilibreur de charge. Veillez à démarrer avec le préfixe `http://`.

   ![Capture d’écran d’Operations Manager, montrant le proxy de l’adresse du serveur](./media/gateway/scom02.png)

1. Sélectionnez **Terminer**. Votre groupe d’administration Operations Manager est maintenant configuré pour communiquer via le serveur de passerelle pour le service Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurer Operations Manager, où des agents spécifiques utilisent un serveur proxy
Pour les environnements complexes ou volumineux, il est conseillé uniquement des serveurs spécifiques (ou groupes) à utiliser le serveur de passerelle Analytique de journal.  Pour ces serveurs, vous ne pouvez pas mettre à jour l’agent Operations Manager directement, car cette valeur est remplacée par la valeur globale du groupe d’administration.  Au lieu de cela, remplacer la règle utilisée pour transférer ces valeurs.  

> [!NOTE] 
> Utilisez cette technique de configuration si vous souhaitez autoriser pour plusieurs serveurs de passerelle Analytique de journal dans votre environnement.  Par exemple, vous pouvez exiger des serveurs de passerelle Analytique de journal spécifiques de la définir sur une base régionale.
>

Pour configurer des serveurs spécifiques ou des groupes à utiliser le serveur de passerelle Analytique de journal : 

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Création**.  
1. Dans l’espace de travail Création, sélectionnez **Règles**. 
1. Dans la barre d’outils Operations Manager, sélectionnez le **étendue** bouton. Si ce bouton n’est pas disponible, vérifiez que vous avez sélectionné un objet, et non un dossier, dans le **surveillance** volet. La boîte de dialogue **Objets du pack de gestion de l’étendue** affiche une liste des classes, groupes ou objets couramment ciblés. 
1. Dans le **recherchez** , entrez **Service d’intégrité** et sélectionnez-le dans la liste. Sélectionnez **OK**.  
1. Recherchez **règle du paramètre Proxy Advisor**. 
1. Dans la barre d’outils Operations Manager, sélectionnez **substitue** , puis pointez sur **remplacer la règle\pour un objet spécifique de la classe : Service d’intégrité** et sélectionner un objet dans la liste.  Ou créer un groupe personnalisé qui contient l’objet de service de contrôle d’intégrité des serveurs que vous souhaitez appliquer ce remplacement. Puis appliquer le remplacement à votre groupe personnalisé.
1. Dans le **propriétés du remplacement** boîte de dialogue zone, ajoutez une coche dans le **remplacer** colonne suivant pour le **WebProxyAddress** paramètre.  Dans le **remplacer la valeur** , entrez l’URL du serveur de passerelle Analytique de journal. Veillez à démarrer avec le préfixe `http://`.  

    >[!NOTE]
    > Vous n’avez pas besoin d’activer la règle. Elle a déjà gérée automatiquement avec un remplacement dans le pack d’administration Microsoft System Center Advisor Secure Reference Override qui cible le groupe Microsoft System Center Advisor Monitoring Server.
    > 

1. Sélectionnez un pack d’administration à partir de la **sélectionnez pack d’administration de destination** liste, ou créez un nouveau pack d’administration non scellé en sélectionnant **New**. 
1. Lorsque vous avez terminé, sélectionnez **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurer les runbooks Workers hybrides Automation
Si vous avez des Runbook Workers hybrides Automation dans votre environnement, suivez ces étapes pour les solutions manuelles de contournement temporaires configurer la passerelle OMS pour prendre en charge les workers.

Pour suivre les étapes décrites dans cette section, vous devez connaître la région Azure où se trouve le compte Automation. Pour trouver cet emplacement :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Sélectionnez le service Azure Automation.
1. Sélectionnez le compte Azure Automation approprié.
1. Regardez sa région sous **Emplacement**.

   ![Capture d’écran de l’emplacement du compte Automation dans le portail Azure](./media/gateway/location.png)

Utilisez les tableaux suivants pour identifier l’URL pour chaque emplacement.

**URL de service de données d’exécution de travail**

| **Lieu** | **URL** |
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

| **Lieu** | **URL** |
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

Si votre ordinateur est automatiquement inscrit en tant qu’un Runbook Worker hybride, utiliser la solution de gestion de la mise à jour pour gérer le correctif logiciel. Procédez comme suit :

1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle Log Analytics. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Redémarrez le service de passerelle Log Analytics en utilisant la cmdlet PowerShell suivante : `Restart-Service OMSGatewayService`

Si votre ordinateur est joint à Azure Automation à l’aide de l’applet de commande d’inscription Runbook Worker hybride, procédez comme suit :

1. Ajoutez l’URL d’enregistrement de service de l’agent à la liste d’hôtes autorisés sur la passerelle Log Analytics. Par exemple : `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Ajoutez les URL de service de données d’exécution de la tâche à la liste d’hôtes autorisés sur la passerelle Log Analytics. Par exemple : `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Redémarrez le service de passerelle Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Applets de commande PowerShell utiles
Vous pouvez utiliser les applets de commande pour effectuer les tâches pour mettre à jour les paramètres de configuration de la passerelle d’Analytique de journal. Avant d’utiliser des applets de commande, veillez à :

1. Installer la passerelle d’Analytique de journal (programme d’installation de Microsoft Windows).
1. Ouvrez une fenêtre de console PowerShell.
1. Importez le module en tapant cette commande : `Import-Module OMSGateway`
1. Si aucune erreur ne s’est produite lors des étapes précédentes, cela signifie que le module a été importé, et que les cmdlets peuvent être utilisées. Entrez `Get-Module OMSGateway`
1. Une fois que vous utilisez les applets de commande pour apporter des modifications, redémarrez le service de passerelle OMS.

Une erreur à l’étape 3 signifie que le module n’a pas été importé. L’erreur peut se produire lorsque PowerShell ne peut pas trouver le module. Vous pouvez rechercher le module dans le chemin d’installation de passerelle OMS : *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Applet de commande** | **Paramètres** | **Description** | **Exemple** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Clé |Récupère la configuration du service |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Clé (obligatoire) <br> Valeur |Modifie la configuration du service |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Récupère l’adresse du proxy de relais (en amont) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adresse<br> Nom d’utilisateur<br> Mot de passe |Définit l’adresse (et les informations d’identification) du proxy de relais (en amont) |1. Définir un proxy relais et des informations d’identification :<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Définir un proxy relais n’exigeant pas d’authentification : `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Effacer le paramètre de proxy relais :<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtient l’hôte actuellement autorisé (uniquement l’hôte autorisé configuré localement, pas automatiquement téléchargé les hôtes autorisés) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Hôte (obligatoire) |Ajoute l’hôte à la liste d’éléments autorisés |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Hôte (obligatoire) |Supprime l’hôte de la liste d’éléments autorisés |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Ajoute l’objet du certificat client à la liste d’éléments autorisés |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Objet (obligatoire) |Supprime l’objet du certificat client de la liste d’éléments autorisés |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtient les objets de certificat du client actuellement autorisés (uniquement les objets configurés localement, pas automatiquement téléchargés objets) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Résolution de problèmes
Pour collecter les événements journalisés par la passerelle, vous devez avoir installé l’agent d’Analytique de journal.

![Capture d’écran de la liste de l’Observateur d’événements dans le journal de la passerelle Analytique de journal](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>ID d’événement passerelle log Analytique et les descriptions

Le tableau suivant montre les ID d’événement et les descriptions des événements de journal de passerelle Analytique de journal.

| **Identifiant** | **Description** |
| --- | --- |
| 400 |Toute erreur d’application associé à aucun ID spécifique. |
| 401 |Configuration incorrecte. Par exemple, listenPort = « text » au lieu d’un entier. |
| 402 |Exception lors de l’analyse des messages de liaison TLS. |
| 403 |Erreur de mise en réseau. Par exemple, Impossible de se connecter au serveur cible. |
| 100 |Informations générales. |
| 101 |Le service a démarré. |
| 102 |Le service s’est arrêté. |
| 103 |Une commande HTTP CONNECT a été reçue à partir du client. |
| 104 |L’élément n’est pas une commande HTTP CONNECT. |
| 105 |Serveur de destination n’est pas dans la liste autorisée ou port de destination n’est pas sécurisé (443). <br> <br> Assurez-vous que l’agent MMA sur votre serveur de passerelle OMS et les agents qui communiquent avec la passerelle OMS sont connectés au même espace de travail Analytique de journal. |
| 105 |ERREUR TcpConnection – Certificat client non valide : CN = passerelle. <br><br> Vérifiez que vous utilisez la passerelle OMS version 1.0.395.0 ou supérieur. Assurez-vous également que l’agent MMA sur votre serveur de passerelle OMS et les agents communiquant avec la passerelle OMS sont connectés au même espace de travail Analytique de journal. |
| 106 |Version du protocole TLS/SSL non pris en charge.<br><br> La passerelle d’Analytique de journal prend en charge uniquement TLS 1.0, TLS 1.1 et 1.2. Elle ne prend pas en charge le protocole SSL.|
| 107 |La session TLS a été vérifiée. |

### <a name="performance-counters-to-collect"></a>Compteurs de performances à collecter

Le tableau suivant montre les compteurs de performances disponibles pour la passerelle Log Analytics. Utiliser l’Analyseur de performances pour ajouter les compteurs.

| **Nom** | **Description** |
| --- | --- |
| Passerelle Log Analytics/Connexion du client actif |Nombre de connexions du réseau client actif (TCP) |
| Passerelle Log Analytics/Nombre d’erreurs |Nombre d’erreurs |
| Passerelle Log Analytics/Client connecté |Nombre de clients connectés |
| Passerelle Log Analytics/Nombre de rejets |Nombre de rejets dus à des erreurs de validation de TLS |

![Interface de passerelle de capture d’écran de journal Analytique, montrant les compteurs de performances](./media/gateway/counters.png)

## <a name="assistance"></a>Assistance
Lorsque vous êtes connecté au portail Azure, vous pouvez obtenir de l’aide avec la passerelle d’Analytique de journal ou tout autre service Azure ou fonctionnalité.
Pour obtenir de l’aide, sélectionnez l’icône de point d’interrogation dans le coin supérieur droit du portail et sélectionnez **nouvelle demande de support**. Puis remplissez le nouveau formulaire de demande de prise en charge.

![Capture d’écran d’une demande de support](./media/gateway/support.png)

## <a name="next-steps"></a>Étapes suivantes
[Ajouter des sources de données](../../azure-monitor/platform/agent-data-sources.md) pour collecter des données à partir de sources connectées et stocker les données dans votre espace de travail Analytique de journal.