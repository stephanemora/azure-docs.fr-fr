---
title: Déployer l’espace de travail Azure Databricks dans votre réseau virtuel (Préversion)
description: Cet article explique comment déployer Azure Databricks sur votre réseau virtuel, ce que l’on appelle également une injection de réseau virtuel.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126679"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Déployer l’espace de travail Azure Databricks dans votre réseau virtuel (Préversion)

Le déploiement par défaut d’Azure Databricks est un service entièrement géré sur Azure : toutes les ressources de plan de données, y compris un réseau virtuel (VNet), sont déployées sur un groupe de ressources verrouillé. Cependant, si vous devez personnaliser votre réseau, vous pouvez déployer les ressources Azure Databricks dans votre propre réseau virtuel. Cette opération est nommée « injection de réseau virtuel ». Elle vous permet de :

* connecter Azure Databricks à d’autres services Azure (par exemple, Stockage Azure) de manière plus sécurisée à l’aide de points de terminaison de service ;
* vous connecter à des données sources locales pour les utiliser avec Azure Databricks, en tirant parti des itinéraires définis par l’utilisateur ;
* connecter Azure Databricks à une appliance de réseau virtuel pour inspecter tout le trafic sortant et prendre des mesures appropriées pour autoriser et refuser des règles ;
* configurer Azure Databricks pour utiliser un DNS personnalisé ;
* configurer des règles de groupe de sécurité réseau (NSG) pour spécifier des restrictions de trafic sortant ;
* déployer des clusters Azure Databricks dans votre réseau virtuel existant.

Le déploiement des ressources Azure Databricks vers votre propre réseau virtuel vous permet également de tirer parti des plages d’adresses CIDR flexibles (n’importe où entre /16-/24 pour le réseau virtuel et entre /18-/26 pour les sous-réseaux).

  > [!NOTE]
  > Vous ne pouvez pas remplacer le réseau virtuel pour un espace de travail existant. Si votre espace de travail actuel ne peut pas prendre en compte le nombre requis de nœuds de cluster actifs, créez un autre espace de travail dans un réseau virtuel plus étendu. Suivez [cette procédure de migration détaillée](howto-regional-disaster-recovery.md#detailed-migration-steps) pour copier les ressources (ordinateurs portables, configurations de cluster, tâches) de l’ancien espace de travail vers le nouveau.

## <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel

Vous pouvez utiliser l’interface de déploiement d’espace de travail Azure Databricks dans le portail Azure pour configurer automatiquement un réseau virtuel existant avec les sous-réseaux, le groupe de sécurité réseau et les paramètres de mise en liste verte requis. Ou vous pouvez utiliser des modèles Azure Resource Manager pour configurer votre réseau virtuel et déployer votre espace de travail.

Le réseau virtuel dans lequel vous déployez votre espace de travail Azure Databricks doit respecter les conditions suivantes :

### <a name="location"></a>Lieu

Le réseau virtuel doit résider dans le même emplacement que l’espace de travail Azure Databricks.

### <a name="subnets"></a>Sous-réseaux

Le réseau virtuel doit inclure deux sous-réseaux dédiés à Azure Databricks :

   1. Un sous-réseau privé avec un groupe de sécurité réseau configuré qui permet la communication interne au cluster

   2. Un sous-réseau public avec un groupe de sécurité réseau configuré qui permet la communication avec le plan de contrôle d’Azure Databricks.

### <a name="address-space"></a>Espace d’adressage

Un bloc CIDR entre /16 - /24 pour le réseau virtuel et un bloc CIDR entre /18 - /26 pour les sous-réseaux privés et publics.

### <a name="whitelisting"></a>Mise en liste verte

Tout le trafic entrant et sortant entre les sous-réseaux et le plan de contrôle d’Azure Databricks doit être mis en liste verte.

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Cette section décrit comment créer un espace de travail Azure Databricks dans le portail Azure et le déployer dans votre propre réseau virtuel existant. Azure Databricks met à jour le réseau virtuel avec deux nouveaux sous-réseaux et des groupes de sécurité réseau à l’aide de plages d’adresses CIDR que vous fournissez, des listes vertes de trafic de sous-réseau entrant et sortant, puis déploie l’espace de travail sur le réseau virtuel mis à jour.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’un réseau virtuel dans lequel vous allez déployer l’espace de travail Azure Databricks. Vous pouvez utiliser un réseau virtuel existant ou en créer un. Cependant, le réseau virtuel doit être dans la même zone géographique que l’espace de travail Azure Databricks que vous souhaitez créer. Une plage CIDR entre /16 - /24 est requise pour le réseau virtuel.

  > [!Warning]
  > Un espace de travail doté d’un réseau virtuel plus restreint (autrement dit, d’une plage CIDR inférieure) peut épuiser sa réserve d’adresses IP (espace réseau) plus rapidement qu’un espace de travail doté d’un réseau virtuel plus étendu. Par exemple, un espace de travail d’une taille de /24 en réseau virtuel et /26 en sous-réseaux peut avoir un maximum de 64 nœuds actifs en même temps, tandis qu’un espace de travail d’une taille de /20 en réseau virtuel et /22 en sous-réseaux pourra héberger jusqu’à 1024 nœuds.

  Vos sous-réseaux seront créés automatiquement lors de la configuration de votre espace de travail. Vous pourrez fournir la plage CIDR pour les sous-réseaux lors de la configuration.

## <a name="configure-the-virtual-network"></a>Configurer le réseau virtuel

1. Dans le portail Azure, sélectionnez **+ Créer une ressource > Analytics (Analytique) > Azure Databricks** pour ouvrir la boîte de dialogue du service Azure Databricks.

2. Suivez la procédure de configuration décrite à l’étape 2 : Créez un espace de travail Azure Databricks dans le Guide de démarrage, puis sélectionnez l’espace de travail de déploiement Azure Databricks dans votre option de réseau virtuel.

   ![Créer un service Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Sélectionnez le réseau virtuel que vous souhaitez utiliser.

   ![Options de réseau virtuel](./media/vnet-injection/select-vnet.png)

4. Fournissez les plages CIDR dans un bloc compris entre /18 - /26 pour deux sous-réseaux, dédiées à Azure Databricks :

   * Un sous-réseau public sera créé avec un groupe de sécurité réseau associé qui permet la communication avec le plan de contrôle d’Azure Databricks.
   * Un sous-réseau privé sera créé avec un groupe de sécurité réseau associé qui permet la communication interne au cluster.

5. Cliquez sur **Créer** pour déployer l’espace de travail Azure Databricks sur le réseau virtuel.

## <a name="advanced-resource-manager-configurations"></a>Configurations de gestionnaire de ressources avancées

Si vous souhaitez contrôler davantage la configuration du réseau virtuel : par exemple, si vous souhaitez utiliser des sous-réseaux existants, utilisez des groupes de sécurité réseau existants ou créez vos propres règles de sécurité. Vous pouvez utiliser les modèles Azure Resource Manager suivants au lieu de la configuration du réseau virtuel et du déploiement de l’espace de travail du portail.

### <a name="all-in-one"></a>Tout-en-un

Pour créer un réseau virtuel, des groupes de sécurité réseau et un espace de travail Azure Databricks tout-en-un, utilisez le [Modèle tout-en-un pour les espaces de travail Databricks injecté par réseau virtuel](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Lorsque vous utilisez ce modèle, vous n’avez pas besoin effectuer manuellement la mise en liste verte du trafic de sous-réseau.

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Pour créer des groupes de sécurité réseau avec les règles requises pour un réseau virtuel existant, utilisez le [Modèle de groupe de sécurité réseau pour l’Injection de réseau virtuel Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Lorsque vous utilisez ce modèle, vous n’avez pas besoin effectuer manuellement la mise en liste verte du trafic de sous-réseau.

### <a name="virtual-network"></a>Réseau virtuel

Pour créer un réseau virtuel doté de sous-réseaux privés et publics appropriés, utilisez le [Modèle de réseau virtuel pour l’injection de réseau virtuel Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Si vous utilisez ce modèle sans utiliser également le modèle de groupes de sécurité réseau, vous devrez ajouter manuellement les règles de mise en liste verte pour les groupes de sécurité réseau que vous utilisez avec le réseau virtuel.

### <a name="azure-databricks-workspace"></a>Espace de travail Azure Databricks

Pour déployer un espace de travail Azure Databricks dans un réseau virtuel existant dont les sous-réseaux publics et privés et les groupes de sécurité réseau sont déjà configurés correctement, utilisez le [Modèle d’espace de travail pour l’injection de réseau virtuel Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Si vous utilisez ce modèle sans utiliser également le modèle de groupes de sécurité réseau, vous devrez ajouter manuellement les règles de mise en liste verte pour les groupes de sécurité réseau que vous utilisez avec le réseau virtuel.

## <a name="whitelisting-subnet-traffic"></a>Mise en liste verte du trafic de sous-réseau

Si vous n’utilisez pas le [portail Microsoft Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) ou les [modèles Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) pour créer les groupes de sécurité de votre réseau, vous devez manuellement placer en liste verte les éléments suivants du trafic sur vos sous-réseaux.

|Direction|Protocole|Source|Port source|Destination|Port de destination|
|---------|--------|------|-----------|-----------|----------------|
|Trafic entrant|\*|VirtualNetwork|\*|\*|\*|
|Trafic entrant|\*|IP NAT de plan de contrôle|\*|\*|22|
|Trafic entrant|\*|IP NAT de plan de contrôle|\*|\*|5557|
|Règle de trafic sortant|\*|\*|\*|IP Webapp|\*|
|Règle de trafic sortant|\*|\*|\*|SQL (balise de service)|\*|
|Règle de trafic sortant|\*|\*|\*|Storage (balise de service)|\*|
|Règle de trafic sortant|\*|\*|\*|VirtualNetwork|\*|

Mettez sur liste verte le trafic du sous-réseau à l’aide de l’adresse IP suivante. Pour SQL (metastore) et Storage (stockage des journaux et des artefacts), vous devez utiliser les [balises de service](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) Sql et Storage.

|Zone géographie d’Azure Databricks|de diffusion en continu|Adresse IP publique|
|-----------------------|-------|---------|
|USA Est|NAT de plan de contrôle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Est 2|NAT de plan de contrôle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Centre Nord|NAT de plan de contrôle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Centre|NAT de plan de contrôle </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Centre Sud|NAT de plan de contrôle </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA Ouest|NAT de plan de contrôle </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA Ouest 2|NAT de plan de contrôle </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Centre du Canada|NAT de plan de contrôle </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Est du Canada|NAT de plan de contrôle </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Ouest du Royaume-Uni|NAT de plan de contrôle </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Sud du Royaume-Uni|NAT de plan de contrôle </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europe Ouest|NAT de plan de contrôle </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europe Nord|NAT de plan de contrôle </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Inde Centre|NAT de plan de contrôle </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Inde Sud|NAT de plan de contrôle </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Inde Ouest|NAT de plan de contrôle </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Asie Sud-Est|NAT de plan de contrôle </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Asie Est|NAT de plan de contrôle </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australie Est|NAT de plan de contrôle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australie Sud-Est|NAT de plan de contrôle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Centre de l’Australie|NAT de plan de contrôle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Centre de l’Australie 2|NAT de plan de contrôle </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japon Est|NAT de plan de contrôle </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japon Ouest|NAT de plan de contrôle </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="workspace-launch-errors"></a>Erreurs de lancement d’espace de travail

Le lancement d’un espace de travail dans un réseau virtuel personnalisé échoue sur l’écran de connexion à Azure Databricks en affichant l’erreur suivante : **« Désolé... Nous avons rencontré une erreur lors de la création de votre espace de travail. Veuillez vérifier que la configuration de votre réseau personnalisé est correcte, puis réessayez. »**

Cette erreur survient lorsque la configuration du réseau ne respecte pas la configuration requise. Vérifiez que vous avez suivi les instructions fournies dans cette rubrique lorsque vous avez créé l’espace de travail.

### <a name="cluster-creation-errors"></a>Erreurs lors de la création du cluster

**Instances inaccessibles : Nous n’avons pas pu accéder aux ressources via le protocole SSH.**

Cause probable : le trafic à partir du plan de contrôle vers les rôles de travail est bloqué. Veuillez corriger ceci en veillant à ce que les règles de sécurité de trafic entrant respectent la configuration requise. Si vous procédez au déploiement sur un réseau virtuel existant connecté à votre réseau local, passez en revue votre configuration en utilisant les informations fournies dans la section Connexion de votre espace de travail Azure Databricks à votre réseau local.

**Erreur imprévue lors du lancement : une erreur imprévue s’est produite lors de la configuration du cluster. Veuillez réessayer. Si le problème persiste, merci de contacter Azure Databricks. Message d’erreur interne : Expiration du délai d’attente lors du placement du nœud.**

Cause probable : le trafic des points de terminaison des rôles de travail jusqu’à ceux du Stockage Azure est bloqué. Veuillez corriger ceci en veillant à ce que les règles de sécurité sortantes respectent la configuration requise. Si vous utilisez des serveurs DNS personnalisés, veuillez aussi vérifier l’état des serveurs DNS dans votre réseau virtuel.

**Échec du lancement du fournisseur de cloud : une erreur de fournisseur de cloud s’est produite lors de la configuration du cluster. Veuillez consulter le guide relatif à Azure Databricks pour plus d’informations. Code d’erreur Azure : AuthorizationFailed/InvalidResourceReference.**

Cause probable : le réseau virtuel ou des sous-réseaux n’existent plus. Veuillez vérifier que le réseau virtuel et tous les sous-réseaux existent.

**Le cluster s’est arrêté. Raison : échec du démarrage de Spark : Spark n’a pas pu démarrer à temps. Ce problème peut provenir d’un disfonctionnement de metastore Hive, de configurations Spark non valides ou d’un disfonctionnement de scripts init. Veuillez consulter les journaux du pilote Spark pour résoudre ce problème. Si le problème persiste, merci de contacter Databricks. Message d’erreur interne : échec du démarrage de Spark : le pilote n’a pas pu démarrer à temps.**

Cause probable : Un conteneur ne peut pas communiquer avec l’instance d’hébergement ou le compte de stockage DBFS. Veuillez corriger ceci en ajoutant un itinéraire personnalisé aux sous-réseaux pour le compte de stockage DBFS où le tronçon suivant est Internet.

### <a name="notebook-command-errors"></a>Erreurs de commande de bloc-notes

**Une commande ne répond pas**

Cause probable : la communication entre les rôles de travail est bloquée. Veuillez corriger cela en vous assurant que les règles de sécurité de trafic entrant respectent la configuration requise.

**Le flux de travail du bloc-notes échoue avec l’exception : com.databricks.WorkflowException : org.apache.http.conn.ConnectTimeoutException**

Cause probable : le trafic des rôles de travail vers Azure Databricks Webapp est bloqué. Veuillez corriger cela en vous assurant que les règles de sécurité de trafic sortant respectent la configuration requise.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Extraire, transformer et charger des données à l’aide d’Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
