---
title: Déployer Azure Databricks dans votre réseau virtuel (version préliminaire)
description: Cet article décrit comment déployer Azure Databricks à votre réseau virtuel, également appelé l’injection de réseau virtuel.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003443"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Déployer Azure Databricks dans votre réseau virtuel (version préliminaire)

Le déploiement par défaut d’Azure Databricks est un service entièrement géré sur Azure : toutes les ressources de plan de données, y compris un réseau virtuel (VNet), sont déployées sur un groupe de ressource verrouillée. Si vous devez personnaliser de réseau, toutefois, vous pouvez déployer les ressources Azure Databricks dans votre propre réseau virtuel (également appelée injection de réseau virtuel), lorsque vous permet de :

* Connecter Azure Databricks à d’autres services Azure (par exemple, le stockage Azure) de manière plus sécurisée à l’aide de points de terminaison de service.
* Se connecter à des données localement sources pour une utilisation avec Azure Databricks, en tirant parti des itinéraires définis par l’utilisateur.
* Connecter Azure Databricks vers une appliance virtuelle réseau pour inspecter tout le trafic sortant et de prendre des mesures en fonction d’autoriser et refuser des règles.
* Configurer Azure Databricks pour utiliser DNS personnalisé.
* Configurer les règles de groupe (NSG) de sécurité réseau pour spécifier des restrictions de trafic sortant.
* Déployer des clusters Azure Databricks dans votre réseau virtuel existant.

Déploiement des ressources d’Azure Databricks à votre propre réseau virtuel vous permet également de tirer parti des plages d’adresses CIDR flexibles (n’importe où entre /16-/ 24 pour le réseau virtuel et entre /18-/ 26 pour les sous-réseaux).

  > [!NOTE]
  > Vous ne pouvez pas remplacer le réseau virtuel pour un espace de travail existant. Si votre espace de travail actuel ne peut pas prendre en compte le nombre requis de nœuds actifs du cluster, créez un autre espace de travail dans un réseau virtuel plus large. Suivez [détaillée des étapes de migration](howto-regional-disaster-recovery.md#detailed-migration-steps) pour copier les ressources (ordinateurs portables, les configurations de cluster, travaux) à partir de l’ancien vers le nouvel espace de travail.

## <a name="virtual-network-requirements"></a>Configuration requise du réseau virtuel

Vous pouvez utiliser l’interface de déploiement d’espace de travail Azure Databricks dans le portail Azure pour configurer automatiquement un réseau virtuel existant avec les sous-réseaux requis, groupe de sécurité réseau et les paramètres de mise en liste verte, ou vous pouvez utiliser Azure Resource Manager modèles pour configurer votre réseau virtuel et de déployer votre espace de travail.

Le réseau virtuel que vous déployez votre espace de travail Azure Databricks doit remplir les conditions suivantes :

### <a name="location"></a>Lieu

Le réseau virtuel doit résider dans le même emplacement que l’espace de travail Azure Databricks.

### <a name="subnets"></a>Sous-réseaux

Le réseau virtuel doit inclure deux sous-réseaux dédiés à Azure Databricks :

   1. Un sous-réseau privé avec un groupe de sécurité réseau configurés qui permet la communication interne au cluster

   2. Un sous-réseau public avec un groupe de sécurité réseau configurés qui permet la communication avec le plan de contrôle d’Azure Databricks.

### <a name="address-space"></a>Espace d’adressage

Un bloc CIDR entre bulletin / /16-24 pour le réseau virtuel et un bloc CIDR entre /18-/26 pour les sous-réseaux privés et publics.

### <a name="whitelisting"></a>Mise en liste verte

Tout le trafic entrant et sortant entre les sous-réseaux et le plan de contrôle d’Azure Databricks doit être dans la liste verte.

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Cette section décrit comment créer un espace de travail Azure Databricks dans le portail Azure et le déployer dans votre propre réseau virtuel existant. Azure Databricks met à jour le réseau virtuel avec deux nouveaux sous-réseaux et les groupes de sécurité réseau à l’aide de plages d’adresses CIDR fournis par vous, en entrant des listes vertes et le trafic sortant de sous-réseau et déploie l’espace de travail sur le réseau virtuel mis à jour.

## <a name="prerequisites"></a>Conditions préalables

Vous devez disposer d’un réseau virtuel auquel vous allez déployer l’espace de travail Azure Databricks. Vous pouvez utiliser un réseau virtuel existant ou créez-en un, mais le réseau virtuel doit être dans la même région que l’espace de travail Azure Databricks que vous souhaitez créer. Une plage CIDR entre /16-/24 est requise pour le réseau virtuel.

  > [!Warning]
  > Un espace de travail avec un réseau virtuel le plus petit : autrement dit, une plage CIDR inférieur – peut manquer des adresses IP (espace de réseau) plus rapidement qu’un espace de travail avec un plus grand réseau virtuel. Par exemple, un espace de travail avec une taille/24 réseau virtuel et /26 sous-réseaux peuvent avoir un maximum de 64 nœuds actifs en même temps, tandis qu’un espace de travail avec un /20 réseau virtuel et /22 sous-réseaux peuvent héberger un maximum de nœuds de 1024.

  Vos sous-réseaux seront créés automatiquement lorsque vous configurez votre espace de travail, et avoir la possibilité de fournir la plage CIDR pour les sous-réseaux lors de la configuration.

## <a name="configure-the-virtual-network"></a>Configurer le réseau virtuel

1. Dans le portail Azure, sélectionnez **+ créer une ressource > Analytique > Azure Databricks** pour ouvrir la boîte de dialogue de service Azure Databricks.

2. Suivez les étapes de configuration décrites à l’étape 2 : Créer un espace de travail Azure Databricks dans le Guide de démarrage, puis sélectionnez l’espace de travail de déploiement Azure Databricks dans votre option de réseau virtuel.

   ![Créer le service Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Sélectionnez le réseau virtuel que vous souhaitez utiliser.

   ![Options de réseau virtuel](./media/vnet-injection/select-vnet.png)

4. Fournir les plages d’adresses CIDR dans un bloc entre /18-/26 pour deux sous-réseaux, dédiées à Azure Databricks :

   * Un sous-réseau public sera créé avec un groupe de sécurité réseau associé qui permet la communication avec le plan de contrôle d’Azure Databricks.
   * Un sous-réseau privé sera créé avec un groupe de sécurité réseau associé qui permet la communication interne au cluster.

5. Cliquez sur **créer** pour déployer l’espace de travail Azure Databricks sur le réseau virtuel.

## <a name="advanced-resource-manager-configurations"></a>Configurations de gestionnaire de ressources avancées

Si vous souhaitez contrôler davantage la configuration du réseau virtuel : par exemple, vous souhaitez utiliser des sous-réseaux existants, utilisez des groupes de sécurité réseau existant ou créer vos propres règles de sécurité – vous pouvez utiliser les modèles Azure Resource Manager suivants à la place de la déploiement de configuration et l’espace de travail de réseau virtuel du portail.

### <a name="all-in-one"></a>Tout en un

Pour créer un réseau virtuel, de groupes de sécurité réseau et d’espace de travail Azure Databricks tout en un, utilisez le [modèle tout-en-un pour les espaces de travail Databricks réseau virtuel injecté](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Lorsque vous utilisez ce modèle, vous n’avez pas besoin effectuer la mise en liste verte n’importe quel manuel du trafic de sous-réseau.

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Pour créer des groupes de sécurité réseau avec les règles requises pour un réseau virtuel existant, utilisez la [modèle de groupe de sécurité réseau pour l’Injection de réseau virtuel Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Lorsque vous utilisez ce modèle, vous n’avez pas besoin effectuer la mise en liste verte n’importe quel manuel du trafic de sous-réseau.

### <a name="virtual-network"></a>Réseau virtuel

Pour créer un réseau virtuel avec les sous-réseaux privés et publics appropriés, utilisez le [modèle de réseau virtuel pour l’Injection de réseau virtuel Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Si vous utilisez ce modèle sans utiliser également le modèle de groupes de sécurité réseau, vous devez ajouter manuellement les règles de mise en liste verte pour les groupes de sécurité réseau que vous utilisez avec le réseau virtuel.

### <a name="azure-databricks-workspace"></a>Espace de travail Azure Databricks

Pour déployer un espace de travail Azure Databricks à un réseau virtuel existant qui a des sous-réseaux publics et privés et les groupes de sécurité réseau configurés correctement déjà configurés, utilisez le [modèle d’espace de travail pour l’Injection de réseau virtuel Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Si vous utilisez ce modèle sans utiliser également le modèle de groupes de sécurité réseau, vous devez ajouter manuellement les règles de mise en liste verte pour les groupes de sécurité réseau que vous utilisez avec le réseau virtuel.

## <a name="whitelisting-subnet-traffic"></a>Mise en liste verte le trafic du sous-réseau

Si vous n’utilisez pas le [Azure portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) ou [modèles Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) pour créer des groupes de sécurité votre réseau, vous devez manuellement liste verte les éléments suivants du trafic sur vos sous-réseaux.

|Direction|Protocole|Source|Port source|Destination|Port de destination|
|---------|--------|------|-----------|-----------|----------------|
|Trafic entrant|\*|VirtualNetwork|\*|\*|\*|
|Trafic entrant|\*|Adresses IP NAT de plan de contrôle|\*|\*|22|
|Trafic entrant|\*|Adresses IP NAT de plan de contrôle|\*|\*|5557|
|Règle de trafic sortant|\*|\*|\*|IP de l’application Web|\*|
|Règle de trafic sortant|\*|\*|\*|SQL (balise de service)|\*|
|Règle de trafic sortant|\*|\*|\*|Stockage (balise de service)|\*|
|Règle de trafic sortant|\*|\*|\*|VirtualNetwork|\*|

Adresses de la liste verte le trafic du sous-réseau à l’aide de l’adresse IP suivante. Pour SQL (metastore) et le stockage (stockage des journaux et des artefacts), vous devez utiliser Sql et stockage [balises de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Région Azure Databricks|de diffusion en continu|Adresse IP publique|
|-----------------------|-------|---------|
|USA Est|Plan de contrôle NAT </br></br>Application Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Est 2|Plan de contrôle NAT </br></br>Application Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Centre Nord|Plan de contrôle NAT </br></br>Application Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Centre|Plan de contrôle NAT </br></br>Application Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Centre Sud|Plan de contrôle NAT </br></br>Application Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA Ouest|Plan de contrôle NAT </br></br>Application Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA Ouest 2|Plan de contrôle NAT </br></br>Application Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Centre du Canada|Plan de contrôle NAT </br></br>Application Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Est du Canada|Plan de contrôle NAT </br></br>Application Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Ouest du Royaume-Uni|Plan de contrôle NAT </br></br>Application Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Sud du Royaume-Uni|Plan de contrôle NAT </br></br>Application Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europe Ouest|Plan de contrôle NAT </br></br>Application Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europe Nord|Plan de contrôle NAT </br></br>Application Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Inde Centre|Plan de contrôle NAT </br></br>Application Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Inde Sud|Plan de contrôle NAT </br></br>Application Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Inde Ouest|Plan de contrôle NAT </br></br>Application Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Asie Sud-Est|Plan de contrôle NAT </br></br>Application Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Asie Est|Plan de contrôle NAT </br></br>Application Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australie Est|Plan de contrôle NAT </br></br>Application Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australie Sud-Est|Plan de contrôle NAT </br></br>Application Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Centre de l’Australie|Plan de contrôle NAT </br></br>Application Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Centre de l’Australie 2|Plan de contrôle NAT </br></br>Application Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japon Est|Plan de contrôle NAT </br></br>Application Web|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japon Ouest|Plan de contrôle NAT </br></br>Application Web|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="workspace-launch-errors"></a>Erreurs de lancement d’espace de travail

Lancement d’un espace de travail dans un réseau virtuel personnalisé échoue sur l’Azure Databricks connexion dans l’écran avec l’erreur suivante : **« Nous avons rencontré une erreur de création de votre espace de travail. Vérifiez que la configuration réseau personnalisée est correcte et réessayez. »**

Cette erreur est due à une configuration de réseau ne répond ne pas aux exigences. Vérifiez que vous avez suivi les instructions fournies dans cette rubrique lorsque vous avez créé l’espace de travail.

### <a name="cluster-creation-errors"></a>Erreurs de création de cluster

**Instances inaccessibles : Ressources n’étaient pas accessibles via le protocole SSH.**

Cause possible : le trafic à partir du plan de contrôle aux travailleurs est bloqué. Corriger en veillant à ce que les règles de sécurité de trafic entrant requise. Si vous déployez sur un réseau virtuel existant connecté à votre réseau local, passez en revue votre configuration en utilisant les informations fournies dans la connexion de votre espace de travail Azure Databricks à votre réseau local.

**Échec du lancement inattendue : Une erreur inattendue s’est produite lors de la configuration du cluster. Réessayez et si le problème persiste, contactez Azure Databricks. Message d’erreur interne : Délai d’attente tout en plaçant le nœud.**

Cause possible : le trafic de traitements aux points de terminaison de stockage Azure est bloqué. Corriger en veillant à ce que les règles de sécurité sortantes répondent aux exigences. Si vous utilisez des serveurs DNS personnalisés, également vérifier l’état des serveurs DNS dans votre réseau virtuel.

**Échec du lancement du fournisseur cloud : Une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster. Consultez le guide d’Azure Databricks pour plus d’informations. Code d’erreur Azure : AuthorizationFailed/InvalidResourceReference.**

Cause possible : le réseau virtuel ou des sous-réseaux n’existent plus. Assurez-vous que le réseau virtuel et les sous-réseaux existent.

**Cluster s’est arrêté. Raison : Échec du démarrage de Spark : Spark n’a pas pu démarrer dans le temps. Ce problème peut provenir d’un metastore Hive ne fonctionnant pas correctement, les configurations Spark non valides ou des scripts init ne fonctionnant pas correctement. Consultez les journaux du pilote Spark pour résoudre ce problème et si le problème persiste, contactez Databricks. Message d’erreur interne : Échec du démarrage de Spark : Échec du démarrage dans le temps du pilote.**

Cause possible : Conteneur ne peut pas communiquer avec hébergement instance ou le compte de stockage DBFS. Corriger en ajoutant un itinéraire personnalisé aux sous-réseaux pour le compte de stockage DBFS avec le tronçon suivant est en cours d’Internet.

### <a name="notebook-command-errors"></a>Erreurs de commande de bloc-notes

**Commande ne répond pas**

Cause possible : la communication du travail-à-worker est bloquée. Corriger en vous assurant que les règles de sécurité de trafic entrant requise.

**Flux de travail de bloc-notes échoue avec l’exception : com.databricks.WorkflowException : org.apache.http.conn.ConnectTimeoutException**

Cause possible : le trafic de traitements pour Azure Databricks Webapp est bloqué. Corriger en vous assurant que les règles de sécurité sortante répondre aux exigences.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Extraire, transformer et charger des données à l’aide d’Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
