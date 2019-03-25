---
title: Connexion à des réseaux virtuels Azure à partir d’Azure Logic Apps via un environnement de service d’intégration (ISE)
description: Créer un environnement de service d’intégration (ISE, Integration Service Environment) afin que les applications logiques et les comptes d’intégration puissent accéder aux réseaux virtuels Azure, tout en restant privés et isolés de l’environnement Azure public ou « mondial »
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 2f84c48092581a313ff7bead7a862221e0fe4eee
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400917"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connexion à des réseaux virtuels Azure à partir d’Azure Logic Apps à l'aide d'un environnement de service d’intégration (ISE)

> [!NOTE]
> Cette fonctionnalité est disponible en [ *version préliminaire publique*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour les scénarios où vos applications logiques et vos comptes d’intégration ont besoin d’accéder à un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), créez un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Une fenêtre ISE est un environnement privé et isolé qui utilise le stockage dédié et autres ressources qui sont maintenues séparées à partir du service Logic Apps public ou « global ». Cette séparation réduit également l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications. Votre environnement ISE est *injecté* dans votre réseau virtuel Azure, qui déploie ensuite le service Logic Apps dans votre réseau virtuel. Quand vous créez une application logique ou un compte d’intégration, sélectionnez cet environnement ISE comme emplacement. Votre application logique ou votre compte d’intégration peut ensuite accéder directement à des ressources, comme des machines virtuelles, des serveurs, des systèmes et des services, dans votre réseau virtuel.

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Cet article vous explique comment effectuer ces tâches :

* Configurez les ports de votre réseau virtuel Azure de manière à permettre au trafic de transiter via votre environnement de service d’intégration (ISE) sur plusieurs sous-réseaux de votre réseau virtuel.

* Créez votre environnement de service d’intégration.

* Créez une application logique qui peut s’exécuter dans votre environnement de service d’intégration.

* Créez un compte d’intégration pour vos applications logiques dans votre environnement de service d’intégration.

Pour plus d’informations sur les environnements de service d’intégration, consultez [Accéder à des ressources Réseau virtuel Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

  > [!IMPORTANT]
  > Les applications logiques, les actions intégrées et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan tarifaire que le tarif de facturation à l’utilisation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md).

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Si vous n’avez pas de réseau virtuel, découvrez comment [créer un réseau virtuel Azure](../virtual-network/quick-create-portal.md). 

  * Votre réseau virtuel doit avoir quatre *vide* sous-réseaux pour le déploiement et la création de ressources dans votre ISE. Vous pouvez créer ces sous-réseaux à l’avance, ou vous pouvez attendre jusqu'à ce que vous créez votre ISE où vous pouvez créer des sous-réseaux en même temps. En savoir plus sur [exigences du sous-réseau](#create-subnet). 
  
    > [!NOTE]
    > Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md), qui fournit une connexion privée aux services cloud Microsoft, vous devez [créer une table de routage](../virtual-network/manage-route-table.md) qui possède ce qui suit acheminer et lier cette table avec chaque sous-réseau utilisé par votre ISE :
    > 
    > **Nom**: <*nom d’itinéraire*><br>
    > **Préfixe d’adresse**: 0.0.0.0/0<br>
    > **Tronçon suivant** : Internet

  * Assurez-vous que votre réseau virtuel [rend ces ports disponibles](#ports) afin que votre ISE fonctionne correctement et reste accessible.

* Si vous souhaitez utiliser des serveurs DNS personnalisés pour votre réseau virtuel Azure, [configurer ces serveurs en suivant ces étapes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) avant de déployer votre ISE à votre réseau virtuel. Sinon, chaque fois que vous modifiez votre serveur DNS, vous devez également redémarrer votre ISE, une fonctionnalité disponible avec la préversion publique de l'environnement.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Configurer des ports réseau

Pour fonctionner correctement et rester accessible, votre environnement de service d’intégration (ISE) doit disposer de ports spécifiques sur votre réseau virtuel. Dans le cas contraire, si un de ces ports n'est pas disponible, vous risquez de perdre l’accès à votre ISE, et ce dernier peut cesser de fonctionner. Lorsque vous utilisez un ISE dans un réseau virtuel, il arrive souvent qu'un ou plusieurs ports soient bloqués. Pour les connexions entre votre ISE et le système de destination, le connecteur que vous utilisez peut également avoir ses propres exigences en matière de port. Par exemple, si vous communiquez avec un système FTP en utilisant le connecteur FTP, assurez-vous que le port que vous utilisez sur ce système FTP, comme le port 21 pour l’envoi de commandes, est disponible.

Pour contrôler le trafic entre les sous-réseaux du réseau virtuel dans lequel vous déployez votre ISE, vous pouvez configurer [groupes de sécurité réseau](../virtual-network/security-overview.md) pour ces sous-réseaux par [filtrage du trafic réseau entre les sous-réseaux](../virtual-network/tutorial-filter-network-traffic.md). Ces tableaux décrivent les ports du réseau virtuel que votre ISE utilise et où ces ports sont utilisés. La [balise de service](../virtual-network/security-overview.md#service-tags) représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité.

> [!IMPORTANT]
> Pour la communication interne à l’intérieur de vos sous-réseaux, ISE nécessite que vous ouvrez tous les ports au sein de ces sous-réseaux.

| Objectif | Direction | Ports | Balise du service source | Identification de destination | Notes |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Communication depuis Azure Logic Apps | Règle de trafic sortant | 80 & 443 | VIRTUAL_NETWORK | INTERNET | Le port dépend du service externe avec lequel communique le service Logic Apps |
| Azure Active Directory | Règle de trafic sortant | 80 & 443 | VIRTUAL_NETWORK | AzureActiveDirectory | |
| Dépendance du Stockage Azure | Règle de trafic sortant | 80 & 443 | VIRTUAL_NETWORK | Stockage | |
| Communication intersubnet | Trafic entrant et sortant | 80 & 443 | VIRTUAL_NETWORK | VIRTUAL_NETWORK | Pour la communication entre les sous-réseaux |
| Communication vers Azure Logic Apps | Trafic entrant | 443 | INTERNET  | VIRTUAL_NETWORK | L’adresse IP de l’ordinateur ou un service qui appelle n’importe quel déclencheur de requête ou un webhook qui existe dans votre application logique. Fermeture ou de blocage de ce port empêche les appels HTTP vers logic apps avec déclencheurs de demande.  |
| Exécution de l’historique de l’application logique | Trafic entrant | 443 | INTERNET  | VIRTUAL_NETWORK | L’adresse IP de l’ordinateur à partir duquel vous affichez l’application logique de l’historique d’exécution. Bien que la fermeture ou de blocage de ce port ne vous empêche pas d’afficher l’historique d’exécution, vous ne pouvez pas afficher les entrées et sorties de chaque étape que l’historique d’exécution. |
| Gestion des connexions | Règle de trafic sortant | 443 | VIRTUAL_NETWORK  | INTERNET | |
| Publier des journaux de diagnostic et métriques | Règle de trafic sortant | 443 | VIRTUAL_NETWORK  | AzureMonitor | |
| Concepteur Logic Apps - Propriétés dynamiques | Trafic entrant | 454 | INTERNET  | VIRTUAL_NETWORK | Les demandes proviennent d’applications logique [point de terminaison d’accès entrant des adresses IP dans cette région](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Dépendance de gestion App Service | Trafic entrant | 454 & 455 | AppServiceManagement | VIRTUAL_NETWORK | |
| Déploiement du connecteur | Trafic entrant | 454 & 3443 | INTERNET  | VIRTUAL_NETWORK | Nécessaire pour le déploiement et la mise à jour des connecteurs. Fermeture ou de blocage de ce port entraîne des déploiements ISE à échouer et empêche les correctifs ou mises à jour du connecteur. |
| Dépendance SQL Azure | Règle de trafic sortant | 1433 | VIRTUAL_NETWORK | SQL |
| Azure Resource Health | Règle de trafic sortant | 1886 | VIRTUAL_NETWORK | INTERNET | Pour la publication de l’état d’intégrité dans Resource Health |
| Gestion des API - Point de terminaison de gestion | Trafic entrant | 3443 | APIManagement  | VIRTUAL_NETWORK | |
| Dépendance du journal pour la stratégie Event Hub et l’agent de surveillance | Règle de trafic sortant | 5672 | VIRTUAL_NETWORK  | Event Hub | |
| Accès aux instances du Cache Azure pour Redis entre instances de rôle | Trafic entrant <br>Règle de trafic sortant | 6379-6383 | VIRTUAL_NETWORK  | VIRTUAL_NETWORK | En outre, pour ISE travailler avec le Cache Azure pour Redis, vous devez ouvrir ces [décrites dans le Cache de Azure pour Redis Forum aux questions sur les ports entrants et sortants](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Trafic entrant | * | AZURE_LOAD_BALANCER | VIRTUAL_NETWORK |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Créer votre environnement de service d’intégration

Pour créer votre environnement de service d’intégration, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), dans le menu principal d’Azure, sélectionnez **Créer une ressource**.

   ![Créer une ressource](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Dans la zone de recherche, entrez « environnement de service d’intégration » comme filtre.
Dans la liste des résultats, sélectionnez **Environnement de service d’intégration (préversion)**, puis choisissez **Créer**.

   ![Sélectionner « Environnement de service d’intégration »](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Choisissez « Créer ».](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Spécifiez ces informations pour votre environnement, puis choisissez **Vérifier + créer**, par exemple :

   ![Spécifier les informations pour l’environnement](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | Oui | <*Azure-subscription-name*> | Abonnement Azure à utiliser pour votre environnement |
   | **Groupe de ressources** | Oui | <*nom-groupe-de-ressources-Azure*> | Groupe de ressources Azure où vous voulez créer votre environnement |
   | **Nom de l’environnement de service d’intégration** | Oui | <*nom-environnement*> | Nom à donner à votre environnement |
   | **Lieu** | Oui | <*région-centre de données-Azure*> | Région du centre de données Azure où déployer votre environnement |
   | **Capacité supplémentaire** | Oui | 0, 1, 2, 3 | Le nombre d’unités de traitement à utiliser pour cette ressource ISE. Pour ajouter capacité après sa création, consultez [Ajouter capacité](#add-capacity). |
   | **Réseau virtuel** | Oui | <*Azure-virtual-network-name*> | Réseau virtuel Azure où vous voulez injecter votre environnement, pour que les applications logiques de cet environnement puissent accéder à votre réseau virtuel. Si vous n’avez pas de réseau, vous devez en créer un ici. <p>**Important** : Vous pouvez effectuer cette injection *seulement*  quand vous créez votre ISE. Cependant, avant de pouvoir créer cette relation, vérifiez que vous avez déjà [configuré le contrôle d’accès en fonction du rôle dans votre réseau virtuel pour Azure Logic Apps](#vnet-access). |
   | **Sous-réseaux** | Oui | <*subnet-resource-list*> | Un environnement ISE nécessite quatre sous-réseaux *vides* pour la création des ressources dans votre environnement. Pour créer chaque sous-réseau, [suivez les étapes décrites dans ce tableau](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Créer un sous-réseau**

   Pour créer des ressources dans votre environnement, votre ISE a besoin de quatre *vide* sous-réseaux qui ne sont pas délégués à n’importe quel service. 
   Vous *ne peut pas* modifier ces adresses de sous-réseau après avoir créé votre environnement. Chaque sous-réseau doit répondre aux critères suivants :

   * A un nom qui commence par un caractère alphabétique ou un trait de soulignement et n’a pas ces caractères : `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Utilise le [Interdomain routage format CIDR (Classless)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) et un espace d’adressage de classe B.

   * Utilise au moins un `/27` dans l’adresse de l’espace, car chaque sous-réseau doit avoir 32 adresses comme la *minimale*. Par exemple : 

     * `10.0.0.0/27` a 32 adresses car 2<sup>(32-27)</sup> est 2<sup>5</sup> ou 32.

     * `10.0.0.0/24` a 256 adresses car 2<sup>(32-24)</sup> est 2<sup>8</sup> ou 256.

     * `10.0.0.0/28` a 16 adresses seulement et est trop petite, car 2<sup>(32-28)</sup> est 2<sup>4</sup> ou 16.

     Pour en savoir plus sur le calcul des adresses, consultez [blocs CIDR de IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md), n’oubliez pas de [créer une table de routage](../virtual-network/manage-route-table.md) qui possède ce qui suit acheminer et lier cette table avec chaque sous-réseau utilisé par votre ISE :

     **Nom**: <*nom d’itinéraire*><br>
     **Préfixe d’adresse**: 0.0.0.0/0<br>
     **Tronçon suivant** : Internet

   1. Sous la liste **Sous-réseaux**, choisissez **Gérer la configuration du sous-réseau**.

      ![Gérer la configuration du sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Dans le volet **Sous-réseaux**, choisissez **Sous-réseau**.

      ![Ajouter un sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Dans le volet **Ajouter un sous-réseau**, spécifiez ces informations.

      * **Nom** : Nom de votre sous-réseau
      * **Plage d’adresses (bloc CIDR)** : Plage de votre sous-réseau dans votre réseau virtuel, au format CIDR

      ![Détails de l’ajout d’un sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Une fois que vous avez terminé, sélectionnez **OK**.

   1. Répétez ces étapes pour trois autres sous-réseaux.

1. Une fois qu’Azure a validé les informations de votre ISE, choisissez **Créer**, par exemple :

   ![Après la validation, choisissez « Créer »](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure commence le déploiement de votre environnement, mais ce processus *peut prendre* jusqu’à deux heures. 
   Pour vérifier l’état du déploiement, dans votre barre d’outils Azure, choisissez l’icône Notifications, qui ouvre le volet Notifications.

   ![Vérifier l’état du déploiement](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Un fois le déploiement terminé, Azure montre cette notification :

   ![Déploiement réussi](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Si le déploiement échoue ou si vous supprimez votre ISE, Azure *peut* prendre jusqu’à une heure avant de libérer vos sous-réseaux. Par conséquent, vous devrez peut-être attendre avant de réutiliser ces sous-réseaux dans un autre ISE.

1. Pour voir votre environnement, choisissez **Accéder à la ressource** si Azure n’accède pas automatiquement à votre environnement une fois le déploiement terminé.  

<a name="add-capacity"></a>

### <a name="add-capacity"></a>Ajouter de la capacité

Votre unité de base ISE a résolu la capacité, si vous avez besoin de davantage de débit, vous pouvez donc ajouter des unités d’échelle. Vous pouvez l’échelle automatique basée sur les mesures de performances ou sur un nombre d’unités de traitement. Si vous choisissez la mise à l’échelle en fonction de mesures, vous pouvez choisir à partir de différents critères et spécifier les conditions de seuil pour répondre à ces critères.

1. Dans le portail Azure, recherchez votre ISE.

1. Pour afficher les mesures de performances pour votre ISE, sur le menu principal de votre ISE, choisissez **vue d’ensemble**.

1. Comment configurer la mise à l’échelle, sous **paramètres**, sélectionnez **monter en charge**. Sur le **configurer** , choisir **activer la mise à l’échelle**.

1. Dans le **par défaut** , choisissez soit **mise à l’échelle selon une mesure** ou **mise à l’échelle à un nombre d’instances spécifique**.

1. Si vous choisissez basée sur l’instance, entrez le nombre d’unités de traitement comprise entre 0 et 3 (inclus). Sinon, de suivi basé sur la mesure, comme suit :

   1. Dans le **par défaut** , choisissez **ajouter une règle**.

   1. Sur le **règle de mise à l’échelle** volet, configurer vos critères et l’action à prendre lorsque la règle se déclenche.

   1. Lorsque vous avez terminé, choisissez **ajouter**.

1. Lorsque vous avez terminé, pensez à enregistrer vos modifications.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Créer une application logique - Environnement de service d’intégration

Pour créer des applications logiques qui utilisent votre environnement de service d’intégration, effectuez les étapes de [Guide pratique pour créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md), mais avec ces différences : 

* Quand vous créez votre application logique, sous la propriété **Emplacement**, sélectionnez votre ISE dans la section **Environnements de service d’intégration**, par exemple :

  ![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Vous pouvez utiliser les mêmes déclencheurs intégrés et les actions tels que HTTP, qui s’exécutent dans l’environnement ISE même que votre application logique. Les connecteurs avec l’étiquette **ISE** peuvent également s’exécuter dans le même environnement de service d’intégration votre application logique. Les connecteurs ne présentant pas le libellé **ISE** s’exécutent dans le service Logic Apps global.

  ![Sélection de connecteurs ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Après avoir injecté votre ISE dans un réseau virtuel Azure, les applications logiques de votre environnement de service d’intégration peuvent accéder directement aux ressources de ce réseau virtuel. Pour les systèmes locaux qui sont connectés à un réseau virtuel, injectez un environnement de service d’intégration dans ce réseau, pour que vos applications logiques puissent accéder directement à ces systèmes en utilisant un de ces éléments : 

  * Connecteur ISE pour ce système, par exemple, SQL Server
  
  * Action HTTP 
  
  * Connecteur personnalisé

  Pour les systèmes locaux qui ne se trouvent pas dans un réseau virtuel ou qui n’ont pas de connecteurs ISE, [configurez d’abord la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Créer un compte d’intégration - Environnement de service d’intégration

Pour utiliser un compte d’intégration avec des applications logiques dans un environnement de service d’intégration, ce compte d’intégration doit utiliser le *même environnement* que les applications logiques. Les applications logiques dans un environnement de service d’intégration peuvent référencer seulement des comptes d’intégration de ce même environnement. 

Pour créer un compte d’intégration qui utilise un environnement de service d’intégration, effectuez les étapes de [Guide pratique pour créer des comptes d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), sauf pour la propriété **Emplacement**, où la section  **Environnements de service d’intégration** apparaît maintenant. À la place, sélectionnez votre environnement de service d’intégration au lieu d’une région, par exemple :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum Azure Logic Apps</a>.
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le <a href="https://aka.ms/logicapps-wish" target="_blank">site de commentaires des utilisateurs Logic Apps</a>.

## <a name="next-steps"></a>Étapes suivantes

* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
