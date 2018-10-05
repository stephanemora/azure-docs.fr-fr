---
title: Se connecter à des réseaux virtuels Azure à partir d’Azure Logic Apps
description: Pour accéder à des réseaux virtuels Azure à partir d’Azure Logic Apps, vous pouvez créer des environnements de service d’intégration privés, dédiés et isolés, qui séparent les applications et les autres ressources d’Azure public ou « mondial »
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: 354c31014448b914b33d2bef5483efc78092f726
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391919"
---
# <a name="create-isolated-environments-to-access-azure-virtual-networks-from-azure-logic-apps"></a>Créer des environnements isolés pour accéder à des réseaux virtuels Azure à partir d’Azure Logic Apps

> [!NOTE]
> Cette fonctionnalité est disponible en *préversion privée*. Pour demander l’accès, [créez votre demande de participation ici](https://aka.ms/iseprivatepreview).

Pour les scénarios d’intégration où vos applications logiques et vos comptes d’intégration ont besoin d’accéder à un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), vous pouvez créer un [*environnement de service d’intégration*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) qui établit un lien avec votre réseau virtuel et déploie le service Logic Apps dans votre réseau. Quand vous créez des applications logiques et des comptes d’intégration, vous sélectionnez pour leur emplacement cet environnement de service d’intégration. Vos applications logiques et vos comptes d’intégration peuvent ensuite accéder directement à des ressources, comme des machines virtuelles, des serveurs, des systèmes et des services, dans votre réseau virtuel. 

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Votre environnement de service d’intégration est un environnement privé et isolé, qui utilise un stockage dédié et d’autres ressources qui existent séparément du service Logic Apps public ou *mondial*. Cette séparation permet également de réduire l’impact que d’autres locataires Azure peuvent avoir sur les performances de vos applications. 

Cet article vous explique comment effectuer ces tâches :

* Définissez les autorisations sur votre réseau virtuel Azure afin que l’instance privée de Logic Apps puisse accéder à votre réseau virtuel.

* Créez votre environnement de service d’intégration. 

* Créez une application logique qui peut s’exécuter dans votre environnement de service d’intégration. 

* Créez un compte d’intégration pour vos applications logiques dans votre environnement de service d’intégration.

Pour plus d’informations sur les environnements de service d’intégration, consultez [Accéder à des ressources Réseau virtuel Azure à partir d’applications logiques Azure isolées](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Si vous n’avez pas de réseau virtuel Azure, découvrez comment [créer un réseau virtuel Azure](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Bien que vous n’ayez pas besoin d’un réseau virtuel Azure pour créer votre environnement, vous pouvez sélectionner*seulement* un réseau virtuel en tant que pair de votre environnement quand vous créez cet environnement. 

* Pour donner à vos applications logiques un accès direct à votre réseau virtuel Azure, [configurez des autorisations de contrôle d’accès en fonction du rôle (RBAC)](#vnet-access), pour que le service Logic Apps dispose des autorisations permettant d’accéder à votre réseau virtuel. 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Définir des autorisations de réseau virtuel

Quand vous créez votre environnement de service d’intégration, vous pouvez sélectionner un réseau virtuel Azure comme *pair* pour votre environnement. Cependant, vous pouvez effectuer cette étape, appelée *appairage*, seulement quand vous créez votre environnement de service d’intégration. Cette relation permet au service Logic Apps de se connecter directement aux ressources de ce réseau virtuel et donne à votre environnement l’accès à ces ressources. 

Avant de pouvoir sélectionner votre réseau virtuel, vous devez configurer les autorisations de contrôle d’accès en fonction du rôle (RBAC) dans votre réseau virtuel. Pour effectuer cette tâche, vous devez attribuer des rôles spécifiques au service Azure Logic Apps.

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre réseau virtuel. 

1. Dans le menu de votre réseau virtuel, sélectionnez **Contrôle d’accès (IAM)**. 

1. Sous **Contrôle d’accès**, sélectionnez **Attribution de rôle** s’il n’est pas déjà sélectionné. Dans la barre d’outils **Attribution de rôle**, choisissez **Ajouter**. 

   ![Ajouter des attributions de rôle](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Dans le volet **Ajouter des autorisations**, configurez chaque rôle de ce tableau pour le service Azure Logic Apps. N’oubliez pas de choisir **Enregistrer** une fois que vous avez terminé chaque rôle :

   | Rôle | Attribuer l’accès à | Sélectionnez | 
   |------|------------------|--------|
   | **Contributeur de réseau** | **Utilisateur, groupe ou application Azure AD** | Entrez **Azure Logic Apps**. Une fois que la liste des membres apparaît, sélectionnez la même valeur. <p>**Conseil** : Si vous ne trouvez pas ce service, entrez l’ID d’application du service Logic Apps : `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Contributeur classique** | **Utilisateur, groupe ou application Azure AD** | Entrez **Azure Logic Apps**. Une fois que la liste des membres apparaît, sélectionnez la même valeur. <p>**Conseil** : Si vous ne trouvez pas ce service, entrez l’ID d’application du service Logic Apps : `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Par exemple : 

   ![Ajout d’autorisations](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   Pour plus d’informations sur les autorisations de rôle nécessaires au peering, consultez la [section Autorisations de l’article Créer, modifier ou supprimer une homologation de réseau virtuel](../virtual-network/virtual-network-manage-peering.md#permissions). 

Si votre réseau virtuel est connecté via Azure ExpressRoute, un VPN Azure point à site ou un VPN Azure site à site, passez à la section suivante, où vous pouvez ajouter le sous-réseau de passerelle nécessaire. Sinon, poursuivez et [créez votre environnement](#create-environment).

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>Ajouter un sous-réseau de passerelle pour les réseaux virtuels avec ExpressRoute ou des VPN

Une fois que vous avez terminé les étapes précédentes, pour donner à votre environnement de service d’intégration l’accès à un réseau virtuel Azure qui est connecté via [Azure ExpressRoute](../expressroute/expressroute-introduction.md), un [VPN Azure point à site](../vpn-gateway/point-to-site-about.md) ou un [VPN Azure site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), vous devez également ajouter un [*sous-réseau de passerelle*](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) à votre réseau virtuel :

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre réseau virtuel. Dans le menu de votre réseau virtuel, sélectionnez **Sous-réseaux**, puis choisissez **Sous-réseau de passerelle** > **OK**.

   ![Ajouter un sous-réseau de passerelle](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. Maintenant, créez une [ *table de routage*](../virtual-network/manage-route-table.md), que vous allez associer au sous-réseau de passerelle que vous avez créé précédemment.

   1. Dans le menu principal d’Azure, choisissez **Créer une ressource** > 
   **Mise en réseau** > **Table de routage**.

      ![Créer une table de routage](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. Spécifiez les informations sur la table de routage, comme le nom, votre abonnement Azure à utiliser, le groupe de ressources Azure et l’emplacement. Vérifiez que la propriété **Propagation de route BGP** est définie sur **Activé**, puis choisissez **Créer**.

      ![Spécifier les détails de la table de routage](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. Dans le menu de la table de routage, sélectionnez **Sous-réseaux**, puis choisissez **Associer**. 

      ![Connecter la table de routage au sous-réseau](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. Sélectionnez **Réseau virtuel**, puis sélectionnez votre réseau virtuel.
   
   1. Sélectionnez **Sous-réseau**, puis sélectionnez votre sous-réseau de passerelle créé précédemment.

   1. Une fois que vous avez terminé, sélectionnez **OK**.

1. Si vous avez un VPN point à site, effectuez aussi ces étapes :

   1. Dans Azure, recherchez et sélectionnez votre ressource de passerelle de réseau virtuel.

   1. Dans le menu de la passerelle, sélectionnez **Configuration de point à site**. 
   Choisissez ensuite **Télécharger le client VPN** de façon à disposer de la configuration la plus récente du client VPN.

      ![Télécharger la version la plus récente du client VPN](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

Vous avez maintenant terminé la configuration d’un sous-réseau de passerelle pour des réseaux virtuels qui utilisent ExpressRoute, des VPN point à site ou des VPN site à site. Pour poursuivre la création de votre environnement de service d’intégration, effectuez les étapes suivantes.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Créer votre environnement de service d’intégration

Pour créer votre environnement de service d’intégration, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), dans le menu principal d’Azure, sélectionnez **Créer une ressource**.

   ![Créer une ressource](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Dans la zone de recherche, entrez « environnement de service d’intégration » comme filtre.
Dans la liste des résultats, sélectionnez **Environnement de service d’intégration (préversion)**, puis choisissez **Créer**.

   ![Sélectionner « Environnement de service d’intégration »](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Choisissez « Créer ».](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Spécifiez ces informations pour votre environnement :

   ![Spécifier les informations pour l’environnement](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Name** | Oui | <*nom-environnement*> | Nom à donner à votre environnement | 
   | **Abonnement** | Oui | <*Azure-subscription-name*> | Abonnement Azure à utiliser pour votre environnement | 
   | **Groupe de ressources** | Oui | <*nom-groupe-de-ressources-Azure*> | Groupe de ressources Azure où vous voulez créer votre environnement |
   | **Lieu** | Oui | <*région-centre de données-Azure*> | Région du centre de données Azure où stocker les informations relatives à votre environnement |
   | **Réseau virtuel homologue** | Non  | <*nom-réseau virtuel-Azure*> | Réseau virtuel Azure à associer à votre environnement en tant que *pair*, pour que les applications logiques de cet environnement puissent accéder à votre réseau virtuel. Avant de pouvoir créer cette relation, vérifiez que vous avez déjà [configuré le contrôle d’accès en fonction du rôle dans votre réseau virtuel pour Azure Logic Apps](#vnet-access). <p>**Important** : Bien qu’un réseau virtuel ne soit pas nécessaire, vous pouvez en sélectionner un *seulement* quand vous créez votre environnement. | 
   | **Nom de l’homologation** | Oui, avec un réseau virtuel sélectionné | <*nom-appairage*> | Nom à donner à la relation d’appairage | 
   | **Plage d’adresses IP du réseau virtuel** | Oui, avec un réseau virtuel sélectionné | <*plage-adresses-IP*> | Plage d’adresses IP à utiliser pour créer des ressources dans votre environnement. Cette plage doit utiliser le [format CIDR (Classless Inter-Domain Routings)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), par exemple 10.0.0.1/16, et nécessite un espace d’adressage de classe B. La plage ne doit pas exister au sein de l’espace d’adressage du réseau virtuel sélectionné dans la propriété **Réseau virtuel homologue**, ni dans aucune autre adresse IP privée où le réseau pair est connecté, via l’appairage ou via des passerelles. <p><p>**Important** : Vous *ne pouvez pas changer* cette plage d’adresses après avoir créé votre environnement. |
   |||||
   
1. Lorsque vous êtes prêt, choisissez **Créer**. 

   Azure commence le déploiement de votre environnement, mais ce processus peut prendre *jusqu’à deux heures* pour se terminer. 
   Pour vérifier l’état du déploiement, dans votre barre d’outils Azure, choisissez l’icône Notifications, qui ouvre le volet Notifications.

   ![Vérifier l’état du déploiement](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Quand le déploiement se termine correctement, Azure montre cette notification :

   ![Déploiement réussi](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Pour voir votre environnement, choisissez **Accéder à la ressource** si Azure n’accède pas automatiquement à votre environnement une fois le déploiement terminé.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Créer une application logique - Environnement de service d’intégration

Pour créer des applications logiques qui utilisent votre environnement de service d’intégration, suivez les étapes habituelles dans [Guide pratique pour créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md), mais en tenant compte de ces différences et de ces considérations : 

* Quand vous créez votre application logique, la propriété **Emplacement** liste vos environnements de service d’intégration sous **Environnements de service d’intégration**, ainsi que les régions disponibles. Sélectionnez votre environnement de service d’intégration, plutôt qu’une région, par exemple :

  ![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Vous pouvez utiliser les mêmes éléments intégrés, comme le déclencheur HTTP ou l’action, qui s’exécutent dans le même environnement de service d’intégration comme application logique parente. Les connecteurs avec l’étiquette **ISE** peuvent également s’exécuter dans le même environnement de service d’intégration que l’application logique parente. Les connecteurs ne présentant pas le libellé **ISE** s’exécutent dans le service Logic Apps global.

  ![Sélection de connecteurs ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Si vous avez précédemment configuré votre environnement de service d’intégration avec un réseau virtuel Azure comme pair, les applications logiques de votre environnement de service d’intégration peuvent accéder directement aux ressources de ce réseau virtuel. Pour les systèmes locaux se trouvant dans un réseau virtuel qui est lié à un environnement de service d’intégration, les applications logiques peuvent accéder directement à ces systèmes en utilisant un de ces éléments : 

  * Connecteur ISE pour ce système, par exemple, SQL Server

  * Action HTTP 

  * Connecteur personnalisé

  Pour les systèmes locaux qui ne se trouvent pas dans un réseau virtuel ou qui n’ont pas de connecteurs d’environnement de service d’intégration, vous pouvez néanmoins toujours vous connecter après avoir [configuré et utilisé la passerelle de données locale](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Créer un compte d’intégration - Environnement de service d’intégration

Pour utiliser un compte d’intégration avec des applications logiques dans un environnement de service d’intégration, ce compte d’intégration doit utiliser le *même environnement* que les applications logiques. Les applications logiques dans un environnement de service d’intégration peuvent référencer seulement des comptes d’intégration de ce même environnement. 

Pour créer un compte d’intégration qui utilise un environnement de service d’intégration, effectuez les étapes habituelles de [Guide pratique pour créer des comptes d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), sauf pour la propriété **Emplacement**, qui liste maintenant vos environnements de service d’intégration sous  **Environnements de service d’intégration**, ainsi que les régions disponibles. Sélectionnez votre environnement de service d’intégration, plutôt qu’une région, par exemple :

![Sélection d’un environnement de service d’intégration](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum Azure Logic Apps</a>.
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le <a href="http://aka.ms/logicapps-wish" target="_blank">site de commentaires des utilisateurs Logic Apps</a>.

## <a name="next-steps"></a>Étapes suivantes

* En apprendre davantage sur le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md)
* Découvrir l’[Intégration d’un réseau virtuel pour les services Azure](../virtual-network/virtual-network-for-azure-services.md)
