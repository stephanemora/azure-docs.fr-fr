---
title: Connecteurs pour Azure Logic Apps
description: Vue d’ensemble des connecteurs pour la création de workflows automatisés avec Azure Logic Apps. Découvrez comment fonctionnent les différents types de connecteurs, de déclencheurs et d’actions.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771350"
---
# <a name="connectors-for-azure-logic-apps"></a>Connecteurs pour Azure Logic Apps

Dans Azure Logic Apps, les *connecteurs* vous aident à accéder rapidement aux événements, données et actions d’autres applications, services, systèmes, protocoles et plateformes. Vous pouvez souvent effectuer ces tâches sans code supplémentaire. Vous pouvez également utiliser des connecteurs pour générer des workflows Logic Apps qui utilisent ces informations dans des environnements cloud, locaux ou hybrides.

Des centaines de connecteurs sont disponibles pour Logic Apps. Par conséquent, cette documentation se concentre sur certains des connecteurs les plus populaires et les plus couramment utilisés pour Logic Apps. Pour obtenir des informations complètes sur les connecteurs Logic Apps, Microsoft Power Automate et Microsoft Power Apps, consultez [Documentation sur les connecteurs](/connectors). 

Pour plus d’informations sur les tarifs, consultez le [modèle de tarification pour Logic Apps](../logic-apps/logic-apps-pricing.md) et les [détails de tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Pour intégrer votre application logique à un service ou à une API n’ayant pas de connecteur, vous pouvez appeler le service par le biais d’un protocole tel que HTTP, ou [créer un connecteur personnalisé](#custom-apis-and-connectors).

## <a name="what-are-connectors"></a>Qu’est-ce qu’un connecteur ?

Dans Azure Logic Apps, les connecteurs fournissent des *déclencheurs* et des *actions* que vous utilisez pour effectuer des tâches dans le workflow de votre application logique. Chaque déclencheur et action a des propriétés que vous pouvez configurer. Certains déclencheurs et actions exigent que vous [créiez et configuriez des connexions](#connection-configuration) afin que votre workflow puisse accéder à un service ou à un système spécifique.

### <a name="triggers"></a>Déclencheurs

Un *déclencheur* est toujours la première étape d’un workflow ; il spécifie l’événement qui démarre le workflow. Il existe plusieurs types de déclencheurs :

- Les *déclencheurs d’interrogation* vérifient régulièrement un service ou un système spécifique selon une planification spécifiée, afin de savoir s’il existe de nouvelles données ou un événement spécifique. Si de nouvelles données sont disponibles ou si l’événement spécifique se produit, ces déclencheurs créent et exécutent une nouvelle instance de votre workflow. Cette nouvelle instance peut ensuite utiliser les données transmises comme entrée.
- Les *déclencheurs Push* sont à l’écoute de nouvelles données ou d’un événement, sans interrogation. Lorsque de nouvelles données sont disponibles, ou lorsque l’événement se produit, ces déclencheurs créent et exécutent une nouvelle instance de votre application logique. Cette nouvelle instance peut ensuite utiliser les données transmises comme entrée.

Par exemple, vous souhaiterez peut-être créer un workflow qui effectue une opération lorsqu’un fichier est chargé sur votre serveur FTP. Vous pouvez ajouter le déclencheur de connecteur FTP nommé **Lorsqu’un fichier est ajouté ou modifié** en guise de première étape de votre workflow, et ensuite spécifier une planification afin de vérifier régulièrement les événements de chargement.

Un déclencheur transmet également les entrées et autres données requises dans votre workflow, où des actions ultérieures peuvent référencer et utiliser ces données dans l’ensemble du workflow. Par exemple, vous souhaiterez peut-être utiliser le déclencheur Office 365 Outlook nommé **À la réception d’un e-mail**. Vous pouvez configurer ce déclencheur afin qu’il transmette le contenu de chaque nouvel e-mail, tel que l’expéditeur, la ligne d’objet, le corps, les pièces jointes, et ainsi de suite. Ensuite, vous pouvez traiter ces informations dans votre application logique à l’aide d’actions.

### <a name="actions"></a>Actions

Une *action* est une opération qui suit le déclencheur et effectue un certain type de tâche dans votre workflow. Vous pouvez utiliser plusieurs actions dans votre application logique. Par exemple, vous disposez peut-être d’un déclencheur SQL qui détecte les nouvelles données client dans une base de données SQL. Votre workflow peut inclure une première action SQL qui obtient les données client, suivie d’une autre action qui n’est pas nécessairement une action SQL et qui traite les données.

## <a name="connector-categories"></a>Catégories de connecteurs

Dans Logic Apps, il existe généralement des versions de connecteurs intégrées ou managés de déclencheurs et d’actions. Un petit nombre de déclencheurs et d’actions sont disponibles dans les deux versions. Les versions spécifiques varient selon que vous créez une application logique multilocataire ou une application logique à locataire unique, qui n’est actuellement disponible que dans la [préversion de Logic Apps](../logic-apps/logic-apps-overview-preview.md).

Les [déclencheurs et actions intégrés](built-in.md) s’exécutent en mode natif sur le runtime Logic Apps, ne nécessitent pas la création de connexions, et effectuent ces types de tâches :

- [Exécution du code dans vos workflows](built-in.md#run-code-from-workflows)
- [Organisation et contrôle de vos données](built-in.md#control-workflow)
- [Gestion ou manipulation des données](built-in.md#manage-or-manipulate-data)

Les [connecteurs managés](managed.md) sont déployés, hébergés et managés par Microsoft. Ces connecteurs fournissent des déclencheurs et des actions pour les services cloud, les systèmes locaux, ou les deux. Il s’agit notamment des paramètres suivants :

- [Connecteurs locaux](managed.md#on-premises-connectors), qui vous permettent d’accéder aux données et aux ressources des systèmes locaux.
- [Connecteurs d’entreprise](managed.md#enterprise-connectors), qui sont des versions de certains connecteurs Logic Apps qui fournissent l’accès aux systèmes d’entreprise.
- [Connecteurs de compte d’intégration](managed.md#integration-account-connectors), qui prennent en charge les scénarios de communication B2B (Business-to-Business).
- [Connecteurs d’environnement de service d’intégration (ISE)](managed.md#ise-connectors), qui constituent un petit groupe de [connecteurs managés disponibles uniquement pour les ISE](#ise-and-connectors).

## <a name="connection-configuration"></a>Configuration de connexion

Pour la plupart des connecteurs, vous devez d’abord créer une *connexion* au service ou système cible, avant de pouvoir utiliser ses déclencheurs ou actions dans votre application logique. Pour créer une connexion, vous devez authentifier votre identité avec les informations d’identification du compte et parfois d’autres informations de connexion. Par exemple, pour pouvoir accéder à votre compte de messagerie Office 365 Outlook et l’utiliser, vous devez autoriser une connexion à ce compte.

Dans le cas des connecteurs qui utilisent l’authentification OAuth Azure AD (Azure Active Directory), tels qu’Office 365, Salesforce ou GitHub, vous devez vous connecter au service où votre jeton d’accès est [chiffré](../security/fundamentals/encryption-overview.md) et stocké de manière sécurisée dans un secret Azure. D’autres connecteurs, comme FTP et SQL, nécessitent une connexion comprenant des détails de configuration, tels que l’adresse du serveur, le nom d’utilisateur et le mot de passe. Ces informations de configuration de connexion sont également [chiffrées et stockées de manière sécurisée dans Azure](../security/fundamentals/encryption-overview.md).

Les connexions établies peuvent accéder au service ou système cible aussi longtemps que ce dernier l’autorise. Pour les services qui utilisent des connexions OAuth Azure AD, tels qu’Office 365 et Dynamics, Logic Apps actualise les jetons d’accès indéfiniment. D’autres services peuvent imposer des limites concernant la durée pendant laquelle le service Logic Apps peut utiliser un jeton sans actualisation. En règle générale, certaines actions, telles que la modification de votre mot de passe, invalident tous les jetons d’accès.

Bien que les connexions soient créées à partir d’un workflow, il s’agit de ressources Azure distinctes avec leurs propres définitions de ressource. Pour passer en revue ces définitions de ressources de connexion, [téléchargez votre application logique dans Visual Studio à partir d’Azure](../logic-apps/manage-logic-apps-with-visual-studio.md). C’est le moyen le plus simple de créer un modèle d’application logique paramétrisé, valide et dans l’ensemble prêt à être déployé.

> [!TIP]
> Si votre organisation ne vous autorise pas à accéder à des ressources spécifiques par le biais de connecteurs Logic Apps, vous pouvez [bloquer la possibilité de créer ce genre de connexion](../logic-apps/block-connections-connectors.md) avec [Azure Policy](../governance/policy/overview.md).

## <a name="recurrence-behavior"></a>Comportement lié à la périodicité

Les déclencheurs intégrés récurrents, tels que le [déclencheur Récurrence](../connectors/connectors-native-recurrence.md), s’exécutent en mode natif dans Azure Logic Apps et diffèrent des déclencheurs récurrents basés sur les connexions, tels que le déclencheur de connecteur Office 365 Outlook, où vous devez d’abord créer une connexion.

Quel que soit le type de déclencheur, si aucune date/heure de début n’est spécifiée pour une périodicité, la première occurrence s’exécute dès l’enregistrement ou le déploiement de l’application logique, quelle que soit la configuration de périodicité de votre déclencheur. Pour éviter ce comportement, indiquez la date et l'heure de début de l'exécution de la première occurrence.

### <a name="recurrence-for-built-in-triggers"></a>Périodicité des déclencheurs intégrés

Les déclencheurs intégrés récurrents respectent le calendrier que vous fixez, fuseaux horaires compris. Cela dit, si aucune autre option de planification avancée n’est spécifiée pour une périodicité, comme des heures précises pour l’exécution de futures occurrences, celles-ci sont basées sur la dernière exécution du déclencheur. Par conséquent, les heures de début de ces périodicités peuvent dériver en raison de facteurs tels que la latence lors des appels de stockage. Pour obtenir de l’aide sur la résolution des problèmes, consultez la section [Problèmes liés à la périodicité](#recurrence-issues).

### <a name="recurrence-for-connection-based-triggers"></a>Périodicité des déclencheurs basés sur la connexion

Dans les déclencheurs récurrents basés sur des connexions, tels qu’Office 365 Outlook, la planification n’est pas le seul élément qui contrôle l’exécution. Le fuseau horaire détermine uniquement l’heure de début initiale. Les exécutions suivantes dépendent de la planification de la périodicité, de la dernière exécution du déclencheur et d’autres facteurs qui peuvent décaler les heures d’exécution ou produire un comportement inattendu. Il s’agit notamment des paramètres suivants :

- Accès ou non par le déclencheur à un serveur contenant d'autres données, que le déclencheur tente immédiatement d'extraire
- Échecs ou nouvelles tentatives induites par le déclencheur
- Latence lors des appels de stockage
- Non-respect du calendrier fixé lors des passages à l'heure d'été et à l'heure d'hiver
- Autres facteurs susceptibles d'avoir une incidence sur l'heure de l'exécution suivante

Pour obtenir de l’aide sur la résolution des problèmes, consultez la section [Problèmes liés à la périodicité](#recurrence-issues). 

### <a name="recurrence-issues"></a>Problèmes liés à la périodicité

Pour que votre workflow s’exécute à l’heure de début spécifiée et ne manque aucune périodicité, en particulier lorsque la fréquence est définie en jours ou sur une valeur plus longue, essayez les solutions suivantes.

Au moment du passage à l’heure d’été, ajustez la périodicité manuellement afin que votre workflow continue de s’exécuter à l’heure prévue. Sinon, l'heure de début est avancée d'une heure lors du passage à l'heure d'été et reculée d'une heure lors du passage à l'heure d'hiver. Pour plus d'informations et pour obtenir des exemples, consultez [Périodicité pour l'heure d'été et l'heure d'hiver](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

Si vous utilisez un déclencheur **Récurrence**, spécifiez un fuseau horaire, et une date et une heure de début. Configurez également les heures spécifiques d’exécution des périodicités suivantes dans les propriétés **Aux heures indiquées** et **Aux minutes indiquées**, qui sont disponibles uniquement pour les fréquences **Jour** et **Semaine**. Certaines fenêtres temporelles peuvent néanmoins poser des problèmes lorsque l'heure change. 

Utilisez un [déclencheur **Fenêtre glissante**](../connectors/connectors-native-sliding-window.md) au lieu d’un déclencheur **Récurrence** pour éviter les périodicités manquées.

## <a name="custom-apis-and-connectors"></a>Connecteurs et API personnalisés

Pour appeler des API qui exécutent du code personnalisé ou qui ne sont pas disponibles en tant que connecteurs, vous pouvez étendre la plateforme Logic Apps [en créant des applications API personnalisées](../logic-apps/logic-apps-create-api-app.md). 

Vous pouvez également [créer des connecteurs personnalisés](../logic-apps/custom-connector-overview.md) pour n’importe quelle API REST ou SOAP, ce qui rend ces API disponibles pour n’importe quelle application logique dans votre abonnement Azure. 

Pour rendre les applications API ou les connecteurs personnalisés publics afin que tout le monde puisse les utiliser dans Azure, vous pouvez [soumettre des connecteurs à la certification Microsoft](/connectors/custom-connectors/submit-certification).

## <a name="ise-and-connectors"></a>ISE et connecteurs

Pour les workflows qui ont besoin d’un accès direct aux ressources d’un réseau virtuel Azure, vous pouvez créer un [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) dédié vous permettant de générer, de déployer et d’exécuter vos workflows sur des ressources dédiées. Pour plus d’informations sur la création d’environnements ISE, consultez l’article [Se connecter à des réseaux virtuels Azure à partir d’Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

Les connecteurs personnalisés créés au sein d’un ISE ne fonctionnent pas avec la passerelle de données locale. Toutefois, ces connecteurs peuvent accéder directement aux sources de données locales qui sont connectées à un réseau virtuel Azure hébergeant l’ISE. Par conséquent, les applications logiques d’un ISE n’ont généralement pas besoin de la passerelle de données lorsqu’elles communiquent avec ces ressources. Si vous avez des connecteurs personnalisés créés hors d’un ISE qui ont besoin de la passerelle de données locale, les applications logiques d’un ISE peuvent utiliser ces connecteurs.

Dans le concepteur d’applications logiques, lorsque vous parcourez les connecteurs que vous souhaitez utiliser pour les applications logiques d’un ISE, une étiquette **CORE** apparaît sur les déclencheurs et les actions intégrés, tandis que l’étiquette **ISE** apparaît sur certains connecteurs.

:::row:::
    :::column:::
        ![Exemple de connecteur CORE](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        Les déclencheurs et les actions intégrés portant cette étiquette s’exécutent dans le même environnement ISE que vos applications logiques.
    :::column-end:::
    :::column:::
        ![Exemple de connecteur ISE](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        Les connecteurs managés portant cette étiquette s’exécutent dans le même environnement ISE que vos applications logiques. Si vous disposez d’un système local connecté à un réseau virtuel Azure, l’environnement ISE permet à vos applications logiques d’accéder directement à ce système sans passer par la [passerelle de données locale](../logic-apps/logic-apps-gateway-connection.md). Au lieu de cela, vous pouvez utiliser le connecteur **ISE** du système s’il est disponible, une action HTTP ou un [connecteur personnalisé](connectors-overview.md#custom-apis-and-connectors). Pour les systèmes locaux qui n’ont pas de connecteurs **ISE**, utilisez la passerelle de données locale. Pour consulter les connecteurs ISE disponibles, consultez [Connecteurs ISE](#ise-and-connectors).
    :::column-end:::
    :::column:::
        ![Exemple de connecteur multilocataire](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Sans étiquette     \
        \
        Les connecteurs ne portant ni l’étiquette **CORE** ni l’étiquette **ISE**, qui peuvent toujours être utilisés, s’exécutent dans le service Logic Apps multilocataire global.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Problèmes connus

Voici les problèmes connus pour les connecteurs Logic Apps.

#### <a name="error-badgateway-client-request-id-guid"></a>Erreur : BadGateway. ID de requête client : '{GUID}'

Cette erreur est due à la mise à jour des étiquettes sur une application logique dans laquelle une ou plusieurs connexions ne prennent pas en charge l’authentification OAuth Azure Active Directory (Azure AD), telle que SFTP et SQL, entraînant une rupture de ces connexions. Pour éviter ce comportement, évitez de mettre à jour ces étiquettes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des API personnalisées que vous pouvez appeler à partir de Logic Apps](/logic-apps/logic-apps-create-api-app)
