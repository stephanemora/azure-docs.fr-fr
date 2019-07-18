---
title: Connexion à des réseaux virtuels Azure à partir d’Azure Logic Apps via un environnement de service d’intégration (ISE)
description: Créer un environnement de service d’intégration (ISE, Integration Service Environment) afin que les applications logiques et les comptes d’intégration puissent accéder aux réseaux virtuels Azure, tout en restant privés et isolés de l’environnement Azure public ou « mondial »
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: b48257cc8e10deb1ec922806f62a6c435069f66f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467096"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connexion à des réseaux virtuels Azure à partir d’Azure Logic Apps à l'aide d'un environnement de service d’intégration (ISE)

Pour les scénarios où vos applications logiques et vos comptes d’intégration ont besoin d’accéder à un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), créez un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un environnement de service d’intégration est un environnement privé et isolé, qui utilise un stockage dédié et d’autres ressources conservées séparément du service Logic Apps public ou « mondial ». Cette séparation réduit également l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications. Votre environnement ISE est *injecté* dans votre réseau virtuel Azure, qui déploie ensuite le service Logic Apps dans votre réseau virtuel. Quand vous créez une application logique ou un compte d’intégration, sélectionnez cet environnement ISE comme emplacement. Votre application logique ou votre compte d’intégration peut ensuite accéder directement à des ressources, comme des machines virtuelles, des serveurs, des systèmes et des services, dans votre réseau virtuel.

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Cet article vous explique comment effectuer ces tâches :

* Vérifiez que tous les ports nécessaires sur un réseau virtuel sont ouverts afin que le trafic puisse transiter à travers votre environnement de service d’intégration (ISE), entre les sous-réseaux dans ce réseau virtuel.

* Créez votre environnement de service d’intégration.

* Créez une application logique qui peut s’exécuter dans votre environnement de service d’intégration.

* Créez un compte d’intégration pour vos applications logiques dans votre environnement de service d’intégration.

Pour plus d’informations sur les environnements de service d’intégration, consultez [Accéder à des ressources Réseau virtuel Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

  > [!IMPORTANT]
  > Les applications logiques, les déclencheurs et actions intégrés et les connecteurs qui s’exécutent dans votre ISE utilisent un autre plan de tarification que celui basé sur la consommation. Pour plus d’informations, consultez [Tarifs Logic Apps](../logic-apps/logic-apps-pricing.md).

* Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Si vous n’avez pas de réseau virtuel, découvrez comment [créer un réseau virtuel Azure](../virtual-network/quick-create-portal.md). 

  * Votre réseau virtuel doit avoir quatre sous-réseaux *vides* pour le déploiement et la création de ressources dans votre ISE. Vous pouvez créer ces sous-réseaux à l’avance, ou attendre de créer votre ISE où vous pouvez créer vos sous-réseaux simultanément. En savoir plus sur [exigences des sous-réseaux](#create-subnet). 
  
    > [!NOTE]
    > Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md), qui fournit une connexion privée aux services cloud Microsoft, vous devez [créer une table de routage](../virtual-network/manage-route-table.md) qui dispose de la route suivante, et lier cette table à chaque sous-réseau utilisé par votre ISE :
    > 
    > **Nom** : <*nom d’itinéraire*><br>
    > **Préfixe de l’adresse** : 0.0.0.0/0<br>
    > **Tronçon suivant** : Internet

  * Assurez-vous que votre réseau virtuel [mette à disposition ces ports](#ports) pour permettre le bon fonctionnement et l'accessibilité de votre ISE.

* Si vous souhaitez utiliser des serveurs DNS personnalisés pour votre réseau virtuel Azure, [configurez ces serveurs en suivant ces étapes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) avant de déployer votre ISE sur votre réseau virtuel. Sinon, chaque fois que vous modifiez votre serveur DNS, vous devez également redémarrer votre ISE, une fonctionnalité disponible avec la préversion publique de l'environnement.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="check-network-ports"></a>Vérifier les ports réseau

Lorsque vous utilisez un environnement de service d’intégration (ISE) avec un réseau virtuel, il arrive souvent qu'un ou plusieurs ports soient bloqués. Les connecteurs que vous utilisez pour créer les connexions entre votre ISE et le système de destination peuvent également avoir leurs propres exigences en matière de port. Par exemple, si vous communiquez avec un système FTP en utilisant le connecteur FTP, assurez-vous que le port que vous utilisez sur ce système FTP, comme le port 21 pour l’envoi de commandes, est disponible.

Pour contrôler le trafic entre les sous-réseaux du réseau virtuel dans lequel vous déployez votre ISE, vous pouvez éventuellement configurer [des groupes de sécurité réseau (NSG)](../virtual-network/security-overview.md) dans votre réseau virtuel en [filtrant le trafic réseau entre les sous-réseaux](../virtual-network/tutorial-filter-network-traffic.md). Si vous choisissez cette méthode, assurez-vous que votre ISE ouvre des ports spécifiques, comme décrit dans le tableau suivant, sur le réseau virtuel qui utilise les groupes de sécurité réseau. Si vous avez déjà des groupes de sécurité réseau ou un pare-feu dans votre réseau virtuel, assurez-vous qu’ils ouvrent ces ports. De cette façon, votre ISE reste accessible et peut fonctionner correctement, et vous ne perdez pas l’accès à votre ISE. Sinon, si certains des ports requis ne sont pas disponibles, votre ISE cesse de fonctionner.

Ces tableaux décrivent les ports du réseau virtuel que votre ISE utilise et où ces ports sont utilisés. Les [balises de service de Resource Manager](../virtual-network/security-overview.md#service-tags) représentent un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité.

> [!IMPORTANT]
> Pour la communication à l’intérieur de vos sous-réseaux, l’ISE nécessite que vous ouvriez tous les ports au sein de ces sous-réseaux.

| Objectif | Direction | Ports | Balise du service source | Identification de destination | Notes |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Communication depuis Azure Logic Apps | Règle de trafic sortant | 80 & 443 | VirtualNetwork | Internet | Le port dépend du service externe avec lequel le service Logic Apps communique |
| Azure Active Directory | Règle de trafic sortant | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Dépendance du Stockage Azure | Règle de trafic sortant | 80 & 443 | VirtualNetwork | Stockage | |
| Communication interne aux sous-réseaux | Trafic entrant et sortant | 80 & 443 | VirtualNetwork | VirtualNetwork | Pour les communications entre sous-réseaux |
| Communication vers Azure Logic Apps | Trafic entrant | 443 | Internet  | VirtualNetwork | L’adresse IP de l’ordinateur ou du service qui appelle n’importe quel déclencheur de requête ou webhook qui existe dans votre application logique. Fermer ou bloquer ce port empêche les appels HTTP vers Logic Apps avec les déclencheurs de requête.  |
| Historique des exécutions d’une application logique | Trafic entrant | 443 | Internet  | VirtualNetwork | L’adresse IP de l’ordinateur à partir duquel vous affichez l’historique des exécutions de l’application logique. Bien que la fermeture ou le blocage de ce port ne vous empêche pas d’afficher l’historique des exécutions, vous ne pouvez pas afficher les entrées et sorties pour chaque étape dans cet historique des exécutions. |
| Gestion des connexions | Règle de trafic sortant | 443 | VirtualNetwork  | Internet | |
| Publier des journaux de diagnostic et métriques | Règle de trafic sortant | 443 | VirtualNetwork  | AzureMonitor | |
| Communication à partir d’Azure Traffic Manager | Trafic entrant | 443 | AzureTrafficManager | VirtualNetwork | |
| Concepteur Logic Apps - Propriétés dynamiques | Trafic entrant | 454 | Internet  | VirtualNetwork | Les demandes proviennent des [adresses IP de point de terminaison d’accès entrantes de Logic Apps cette région](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Dépendance de gestion App Service | Trafic entrant | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Déploiement du connecteur | Trafic entrant | 454 & 3443 | Internet  | VirtualNetwork | Nécessaire pour le déploiement et la mise à jour des connecteurs. La fermeture ou le blocage de ce port entraîne l’échec des déploiements de l’ISE et empêche les correctifs ou mises à jour du connecteur. |
| Dépendance Azure SQL | Règle de trafic sortant | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Règle de trafic sortant | 1886 | VirtualNetwork | Internet | Pour publier l’état d’intégrité sur Resource Health |
| Gestion des API - Point de terminaison de gestion | Trafic entrant | 3443 | APIManagement  | VirtualNetwork | |
| Dépendance du journal pour la stratégie Event Hub et l’agent de surveillance | Règle de trafic sortant | 5672 | VirtualNetwork  | Event Hub | |
| Accès aux instances du Cache Azure pour Redis entre instances de rôle | Trafic entrant <br>Règle de trafic sortant | 6379-6383 | VirtualNetwork  | VirtualNetwork | En outre, pour qu’ISE fonctionne avec le cache Azure pour Redis, vous devez ouvrir les [ports entrants et sortants décrits dans la FAQ sur le cache Azure pour Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Trafic entrant | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Créer votre environnement de service d’intégration

Pour créer votre environnement de service d’intégration, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), dans le menu principal d’Azure, sélectionnez **Créer une ressource**.
Dans la zone de recherche, entrez « environnement de service d’intégration » comme filtre.

   ![Créer une ressource](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Dans le volet de la création de l’environnement de service d'intégration, choisissez **Créer**.

   ![Choisissez « Créer ».](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Spécifiez ces informations pour votre environnement, puis choisissez **Vérifier + créer**, par exemple :

   ![Spécifier les informations pour l’environnement](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | OUI | <*Azure-subscription-name*> | Abonnement Azure à utiliser pour votre environnement |
   | **Groupe de ressources** | OUI | <*nom-groupe-de-ressources-Azure*> | Groupe de ressources Azure où vous voulez créer votre environnement |
   | **Nom de l’environnement de service d’intégration** | OUI | <*nom-environnement*> | Nom à donner à votre environnement |
   | **Lieu** | OUI | <*région-centre de données-Azure*> | Région du centre de données Azure où déployer votre environnement |
   | **Capacité supplémentaire** | OUI | 0 à 10 | Le nombre d’unités de traitement supplémentaires à utiliser pour cette ressource ISE. Pour ajouter de la capacité après création, consultez [Ajouter de la capacité à l’ISE](#add-capacity). |
   | **Réseau virtuel** | OUI | <*Azure-virtual-network-name*> | Réseau virtuel Azure où vous voulez injecter votre environnement, pour que les applications logiques de cet environnement puissent accéder à votre réseau virtuel. Si vous n’avez pas de réseau, [créez d’abord un réseau virtuel Azure](../virtual-network/quick-create-portal.md). <p>**Important !** Vous pouvez effectuer cette injection *seulement*  quand vous créez votre ISE. |
   | **Sous-réseaux** | OUI | <*subnet-resource-list*> | Un environnement ISE nécessite quatre sous-réseaux *vides* pour la création des ressources dans votre environnement. Pour créer chaque sous-réseau, [suivez les étapes décrites dans ce tableau](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Créer un sous-réseau**

   Pour créer des ressources dans votre environnement, votre ISE a besoin de quatre sous-réseaux *vides* qui ne sont délégués à aucun service. 
   Vous *ne pouvez pas changer* ces adresses de sous-réseaux après avoir créé votre environnement. Chaque sous-réseau doit répondre aux critères suivants :

   * A un nom qui commence par un caractère alphabétique ou un trait de soulignement et ne comprend pas ces caractères : `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Il utilise le format [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) et un espace d’adressage de Classe B.

   * Utilise au moins un `/27` dans l’adresse de l’espace, car chaque sous-réseau doit avoir 32 adresses au *minimum*. Par exemple :

     * `10.0.0.0/27` a 32 adresses car 2<sup>(32-27)</sup> est égal à 2<sup>5</sup> soit 32.

     * `10.0.0.0/24` a 256 adresses car 2<sup>(32-24)</sup> est égal à 2<sup>8</sup> soit 256.

     * `10.0.0.0/28` a seulement 16 adresses et est trop petit, car 2<sup>(32-28)</sup> représente 2<sup>4</sup>, ou 16.

     Pour en savoir plus sur le calcul des adresses, consultez [Blocs CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md), n’oubliez pas de [créer une table de routage](../virtual-network/manage-route-table.md) qui possède l’itinéraire suivant et de lier cette table à chaque sous-réseau utilisé par votre ISE :

     **Nom** : <*nom d’itinéraire*><br>
     **Préfixe de l’adresse** : 0.0.0.0/0<br>
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

      > [!NOTE]
      > Si les sous-réseaux que vous tentez de créer ne sont pas valides, le portail Azure affiche un message mais ne bloque pas votre progression.

1. Une fois qu’Azure a validé les informations de votre ISE, choisissez **Créer**, par exemple :

   ![Après la validation, choisissez « Créer »](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure commence le déploiement de votre environnement, mais ce processus *peut prendre* jusqu’à deux heures. 
   Pour vérifier l’état du déploiement, dans votre barre d’outils Azure, choisissez l’icône Notifications, qui ouvre le volet Notifications.

   ![Vérifier l’état du déploiement](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Un fois le déploiement terminé, Azure montre cette notification :

   ![Déploiement réussi](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Sinon, suivez les instructions du portail Azure pour la résolution des problèmes de déploiement.

   > [!NOTE]
   > Si le déploiement échoue ou si vous supprimez votre ISE, Azure peut prendre jusqu’à une heure avant de libérer vos sous-réseaux. Ce délai signifie que vous devrez peut-être attendre avant de réutiliser ces sous-réseaux dans un autre ISE. 
   >
   > Si vous supprimez votre réseau virtuel, Azure a généralement besoin de jusqu'à deux heures pour libérer vos sous-réseaux, mais cette opération peut prendre plus longtemps. 
   > Lors de la suppression de réseaux virtuels, assurez-vous qu’aucune ressource n’est restée connectée. Consultez [Supprimer un réseau virtuel](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Pour voir votre environnement, choisissez **Accéder à la ressource** si Azure n’accède pas automatiquement à votre environnement une fois le déploiement terminé.  

Pour plus d’informations sur la création de sous-réseaux, consultez [Ajouter un sous-réseau à un réseau virtuel](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Créer une application logique - Environnement de service d’intégration

Pour créer des applications logiques qui s’exécutent dans votre environnement de service d’intégration (ISE), [créez vos applications logiques de la façon habituelle](../logic-apps/quickstart-create-first-logic-app-workflow.md), mais lorsque vous définissez la propriété **Emplacement**, sélectionnez votre ISE dans la section  **Environnements de service d’intégration**, par exemple :

  ![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

Pour connaître les différences dans le fonctionnement des déclencheurs et des actions et dans la façon dont ils sont étiquetés lorsque vous utilisez un ISE par rapport au service global Logic Apps, consultez [Isolé ou global dans la vue d’ensemble de l’ISE](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Créer un compte d’intégration - Environnement de service d’intégration

Si vous souhaitez utiliser un compte d’intégration avec des applications logiques dans un environnement de service d’intégration, ce compte d’intégration doit utiliser le *même environnement* que les applications logiques. Les applications logiques dans un environnement de service d’intégration peuvent référencer seulement des comptes d’intégration de ce même environnement.

Pour créer un compte d’intégration qui utilise une fenêtre ISE, [créez votre compte d’intégration de la façon habituelle](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), mais lorsque vous définissez la propriété **Emplacement** propriété, sélectionnez votre ISE à partir de la section **Environnements de service d’intégration**, par exemple :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Ajouter de la capacité à l’ISE

Votre unité de base d’ISE dispose d’une capacité fixe ; si vous avez besoin de davantage de débit, vous pouvez ajouter des unités d’échelle. Vous pouvez mise à l’échelle automatiquement en fonction des mesures de performances ou d’un nombre d’unités de traitement supplémentaires. Si vous choisissez la mise à l’échelle automatique en fonction des mesures, vous pouvez choisir à partir de différents critères et spécifier les conditions de seuil pour répondre à ces critères.

1. Dans le portail Azure, recherchez votre ISE.

1. Pour consulter les mesures de performances et d’utilisation pour votre ISE, sélectionnez **Vue d’ensemble** dans le menu principal de votre ISE.

   ![Afficher l’utilisation pour l’ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Pour configurer la mise à l’échelle automatique, sous **Paramètres**, sélectionnez **Monter en charge**. Sous l’onglet **Configurer**, choisissez **Activer la mise à l’échelle automatique**.

   ![Activer la mise à l’échelle automatique](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Pour **Nom du paramètre de mise à l’échelle automatique**, fournissez un nom pour votre paramètre.

1. Dans la section **Par défaut**, choisissez soit **Mettre à l’échelle selon une métrique** soit **Mettre à l’échelle d’un nombre d’instances spécifique**.

   * Si vous choisissez la mise à l’échelle basée sur les instances, entrez un nombre d’unités de traitement entre 0 et 10 (inclus).

   * Si vous choisissez la méthode basée sur les mesures, procédez comme suit :

     1. Dans la section **Règles**, choisissez **+ Ajouter une règle**.

     1. Dans le volet **Règle de mise à l’échelle**, configurez vos critères et l’action à effectuer lorsque la règle se déclenche.

     1. Une fois que vous avez terminé, sélectionnez **Ajouter**.

1. Lorsque vous avez terminé de configurer vos paramètres de mise à l’échelle, enregistrez vos modifications.

## <a name="next-steps"></a>Étapes suivantes

* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
