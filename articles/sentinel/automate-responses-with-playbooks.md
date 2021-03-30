---
title: Automatiser la réponse aux menaces à l’aide de playbooks dans Azure Sentinel | Microsoft Docs
description: Cet article explique l’automatisation dans Azure Sentinel et montre comment utiliser des playbooks pour automatiser la prévention et la réponse aux menaces.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608637"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatiser la réponse aux menaces à l’aide de playbooks dans Azure Sentinel

Cet article explique ce que sont les playbooks Azure Sentinel, et comment les utiliser pour implémenter vos opérations d’orchestration, d’automatisation et de réponse aux problèmes de sécurité (SOAR), en obtenant de meilleurs résultats tout en économisant du temps et des ressources.

## <a name="what-is-a-playbook"></a>Qu’est-ce qu’un playbook ?

Les équipes SIEM/SOC sont généralement submergées d’alertes de sécurité et d’incidents régulièrement, avec des volumes si conséquents que le personnel disponible est surchargé. Il en résulte trop souvent des situations où de nombreuses alertes sont ignorées et où les incidents ne peuvent pas être examinés. L’organisation reste donc vulnérable aux attaques qui passent inaperçues.

Beaucoup, voire la plupart, de ces alertes et incidents sont conformes à des modèles récurrents qui peuvent être traités par des ensembles d’actions de correction spécifiques et définies.

Un playbook est un ensemble de ces actions de correction qui peuvent être exécutées à partir d’Azure Sentinel en tant que routine. Un playbook peut vous aider à automatiser et orchestrer votre réponse aux menaces. Il peut être exécuté manuellement ou configuré pour s’exécuter automatiquement en réponse à des alertes ou des incidents spécifiques, lorsqu’il est déclenché par une règle d’analyse ou une règle d’automatisation, respectivement.

Les playbooks sont créés et appliqués au niveau de l’abonnement, mais l’onglet **Playbooks** (dans le nouveau panneau **Automatisation**) affiche tous les playbooks disponibles dans les abonnements sélectionnés.

### <a name="azure-logic-apps-basic-concepts"></a>Concepts de base d’Azure Logic Apps

Les playbooks dans Azure Sentinel sont basés sur des workflows intégrés à [Azure Logic Apps](../logic-apps/logic-apps-overview.md), service cloud qui vous permet de planifier, d’automatiser et d’orchestrer des tâches et des workflows à travers l’entreprise. Cela signifie que les playbooks peuvent tirer parti de toute la puissance et de la personnalisation des modèles intégrés de Logic Apps.

> [!NOTE]
> Comme Azure Logic Apps est une ressource distincte, des frais supplémentaires peuvent s’appliquer. Consultez la page sur la tarification [d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) pour en savoir plus.

Azure Logic Apps communique avec d’autres systèmes et services à l’aide de connecteurs. Voici une brève explication des connecteurs et de certains de leurs attributs importants :

- **Connecteur géré :** Ensemble d’actions et de déclencheurs qui encapsulent les appels d’API pour un produit ou service particulier. Azure Logic Apps propose des centaines de connecteurs pour communiquer avec des services Microsoft et non Microsoft.
  - [Liste de tous les connecteurs Logic Apps et leur documentation](/connectors/connector-reference/)

- **Connecteur personnalisé :** Vous pourriez souhaiter communiquer avec des services qui ne sont pas disponibles en tant que connecteurs prédéfinis. Les connecteurs personnalisés répondent à ce cas de figure en vous permettant de créer (et même de partager) un connecteur et de définir ses propres déclencheurs et actions.
  - [Créer vos propres connecteurs Logic Apps personnalisés](/connectors/custom-connectors/create-logic-apps-connector)

- **Connecteur Azure Sentinel :** Pour créer des playbooks qui interagissent avec Azure Sentinel, utilisez le connecteur Azure Sentinel.
  - [Documentation du connecteur Azure Sentinel](/connectors/azuresentinel/)

- **Déclencheur :** Un composant de connecteur qui démarre un playbook. Il définit le schéma que le playbook s’attend à recevoir lorsqu’il est déclenché. Le connecteur Azure Sentinel a actuellement deux déclencheurs :
  - [Déclencheur d’alerte](/connectors/azuresentinel/#triggers) : Le playbook reçoit l’alerte en tant qu’entrée.
  - [Déclencheur d’incident](/connectors/azuresentinel/#triggers) : Le playbook reçoit l’incident comme entrée, ainsi que toutes les alertes et les entités incluses.

    > [!IMPORTANT]
    >
    > - La fonctionnalité de **déclenchement d’incident** pour les playbooks est actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

- **Actions :** les actions sont toutes les étapes qui se produisent après le déclencheur. Elles peuvent être réorganisées de façon séquentielle, en parallèle ou dans une matrice de conditions complexes.

- **Champs dynamiques :** Les champs temporaires, déterminés par le schéma de sortie des déclencheurs et des actions, et remplis par leur sortie réelle, qui peuvent être utilisés dans les actions qui suivent.

### <a name="permissions-required"></a>Autorisations requises

 Pour donner à votre équipe SecOps la possibilité d’utiliser Logic Apps pour les opérations d’orchestration, d’automatisation et de réponse aux problèmes de sécurité (SOAR), c’est-à-dire pour créer et exécuter des playbooks, dans Azure Sentinel, vous pouvez affecter des rôles Azure, soit à des membres spécifiques de votre équipe d’opérations de sécurité, soit à l’équipe entière. Ce qui suit décrit les différents rôles disponibles et les tâches pour lesquelles ils doivent être attribués :

#### <a name="azure-roles-for-logic-apps"></a>Rôles Azure pour Logic Apps

- Le **contributeur d’application logique** vous permet de gérer des applications logiques et d’exécuter des playbooks, mais vous ne pouvez pas modifier l’accès à ceux-ci (pour cela, vous avez besoin du rôle de **propriétaire**).
- **Opérateur d’application logique** vous permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ni de les mettre à jour.

#### <a name="azure-roles-for-sentinel"></a>Rôles Azure pour Sentinel

- Le rôle **contributeur Azure Sentinel** vous permet de joindre un playbook à une règle d’analyse.
- Le rôle de **répondeur Sentinel Azure** vous permet d’exécuter un playbook manuellement.
- Le **contributeur d’automatisation Azure Sentinel** permet aux règles d’automatisation d’exécuter des playbooks. Il n’est pas utilisé à d’autres fins.

#### <a name="learn-more"></a>En savoir plus

- [En savoir plus sur les rôles Azure dans Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [En savoir plus sur les rôles Azure dans Azure Sentinel](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Étapes de création d’un playbook

- [Définissez le scénario d’automatisation](#use-cases-for-playbooks).

- [Générez l’application logique Azure](tutorial-respond-threats-playbook.md).

- [Testez votre application logique](#run-a-playbook-manually-on-an-alert).

- Attachez le playbook à une [règle d’automatisation](#incident-creation-automated-response) ou à une [règle d’analyse](#alert-creation-automated-response), ou [exécutez-le manuellement si nécessaire](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Cas d’usage pour les playbooks

La plateforme Azure Logic Apps offre des centaines d’actions et de déclencheurs, de sorte que presque tout scénario d’automatisation peut être créé. Azure Sentinel recommande de commencer avec les scénarios SOC suivants :

#### <a name="enrichment"></a>Enrichissement

**Collectez des données et attachez-les à l’incident afin de prendre des décisions plus intelligentes.**

Par exemple :

Un incident Azure Sentinel a été créé à partir d’une alerte par une règle d’analyse qui génère des entités d’adresse IP.

L’incident déclenche une règle d’automatisation qui exécute un playbook avec les étapes suivantes :

- Démarrez quand un [nouvel incident Azure Sentinel est créé](/connectors/azuresentinel/#triggers). Les entités représentées dans l’incident sont stockées dans les champs dynamiques du déclencheur d’incident.

- Pour chaque adresse IP, interrogez un fournisseur d’informations sur les menaces externes, tel que le [Virus Total](https://www.virustotal.com/), pour récupérer plus de données.

- Ajoutez les données et insights renvoyés en tant que commentaires de l’incident.

#### <a name="bi-directional-sync"></a>Synchronisation bidirectionnelle

**Les playbooks peuvent être utilisés pour synchroniser vos incidents Azure Sentinel avec d’autres systèmes de tickets.**

Par exemple :

Créez une règle d’automatisation pour tous les cas de création d’incident, et joignez un playbook qui ouvre un ticket dans ServiceNow :

- Démarrez quand un [nouvel incident Azure Sentinel est créé](/connectors/azuresentinel/#triggers).

- Créez un nouveau ticket dans [ServiceNow](/connectors/service-now/).

- Incluez dans le ticket le nom de l’incident, les champs importants et une URL vers l’incident Azure Sentinel pour faciliter le croisement.

#### <a name="orchestration"></a>Orchestration

**Utilisez la plateforme de conversation SOC pour mieux contrôler la file d’attente d’incidents.**

Par exemple :

Un incident Azure Sentinel a été créé à partir d’une alerte par une règle d’analyse qui génère des entités de nom d’utilisateur et d’adresse IP.

L’incident déclenche une règle d’automatisation qui exécute un playbook avec les étapes suivantes :

- Démarrez quand un [nouvel incident Azure Sentinel est créé](/connectors/azuresentinel/#triggers).

- Envoyez un message à votre canal d’opérations de sécurité dans [Microsoft Teams](/connectors/teams/) ou [Slack](/connectors/slack/) pour vous assurer que vos analystes de sécurité sont conscients de l’incident.

- Envoyez toutes les informations de l’alerte par e-mail à vos administrateur réseau et administrateur de sécurité supérieurs. L’e-mail comprendra les boutons d’option utilisateur **Bloquer** et **Ignorer**.

- Attendez qu’une réponse soit reçue des administrateurs, puis continuez à exécuter.

- Si les administrateurs ont choisi **Bloquer**, envoyez une commande au pare-feu pour bloquer l’adresse IP dans l’alerte, et une autre à Azure AD pour désactiver l’utilisateur.

#### <a name="response"></a>response

**Répondez immédiatement aux menaces, avec des dépendances humaines minimales.**

Deux exemples :

**Exemple 1 :** Répondez à une règle d’analyse qui indique un utilisateur compromis, tel qu’il est découvert par [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) :

   - Démarrez quand un [nouvel incident Azure Sentinel est créé](/connectors/azuresentinel/#triggers).

   - Pour chaque entité d’utilisateur dans l’incident soupçonné d’être compromis :

     - Envoyez un message Teams à l’utilisateur, en demandant confirmation que l’utilisateur a effectué l’action suspecte.

     - Vérifiez avec Azure AD Identity Protection pour [confirmer que l’état de l’utilisateur est compromis](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection marque l’utilisateur comme étant **à risque** et applique une stratégie déjà configurée (par exemple, pour demander à l’utilisateur d’utiliser l’authentification multifacteur lors de la prochaine connexion).

        > [!NOTE]
        > Le playbook ne lance aucune action d’application sur l’utilisateur et ne lance aucune configuration de stratégie d’application. Il demande uniquement à Azure AD Identity Protection d’appliquer les stratégies déjà définies, le cas échéant. Toute application dépend entièrement des stratégies appropriées définies dans Azure AD Identity Protection.

**Exemple 2 :** Répondre à une règle d’analyse qui indique un ordinateur compromis, tel qu’il est détecté par [Microsoft Defender pour point de terminaison](/windows/security/threat-protection/) :

   - Démarrez quand un [nouvel incident Azure Sentinel est créé](/connectors/azuresentinel/#triggers).

   - Utilisez l’action **Entités - Récupérer les hôtes** dans Azure Sentinel pour analyser les ordinateurs suspects qui sont inclus dans les entités de l’incident.

   - Émettez une commande à Microsoft Defender pour point de terminaison pour [isoler les machines](/connectors/wdatp/#actions---isolate-machine) dans l’alerte.

## <a name="how-to-run-a-playbook"></a>Comment exécuter un playbook

Les playbooks peuvent être exécutés **manuellement** ou **automatiquement**.

L’exécution manuelle signifie que, lorsque vous recevez une alerte, vous pouvez choisir d’exécuter un playbook à la demande en réponse à celle-ci. Actuellement, cette fonctionnalité est prise en charge uniquement pour les alertes, pas pour les incidents.

Leur exécution automatique signifie que vous les définissez en tant que réponse automatisée dans une règle d’analyse (pour les alertes) ou en tant qu’action dans une règle d’automatisation (pour les incidents). [En savoir plus sur les règles d’automatisation](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Définir une réponse automatisée

Les équipes d’opérations de sécurité peuvent réduire considérablement leur charge de travail, ce qui leur permet de se concentrer sur des incidents et alertes uniques, d’analyser des modèles et de repérer les menaces, entre autres.

La définition de la réponse automatisée signifie que chaque fois qu’une règle d’analyse est déclenchée, en plus de créer une alerte, la règle exécute un playbook, qui reçoit comme entrée l’alerte créée par la règle.

Si l’alerte crée un incident, l’incident déclenche une règle d’automatisation qui peut à son tour exécuter un playbook, qui recevra en tant qu’entrée l’incident créé par l’alerte.

#### <a name="alert-creation-automated-response"></a>Réponse automatisée de création d’alerte

Pour les règles qui sont déclenchées par la création d’une alerte et qui reçoivent des alertes comme entrées (la première étape est « Quand une alerte Azure Sentinel est déclenchée »), attachez le playbook à une règle d’analyse :

1. Modifiez la [règle d’analyse](tutorial-detect-threats-custom.md) qui génère l’alerte pour laquelle vous souhaitez définir une réponse automatisée.

1. Sous **Automatisation des alertes** dans l’onglet **Réponse automatisée**, sélectionnez le ou les playbooks que cette règle d’analyse déclenchera lors de la création d’une alerte.

#### <a name="incident-creation-automated-response"></a>Réponse automatisée de création d’incident

Pour les playbooks qui sont déclenchés par la création d’un incident et qui reçoivent des incidents en tant qu’entrées (la première étape est « Quand un incident Azure Sentinel est déclenché »), créez une règle d’automatisation et définissez une action d’**Exécuter le playbook** dans celle-ci. Cette opération peut être réalisée de 2 manières :

- Modifiez la règle d’analyse qui génère l’incident pour lequel vous souhaitez définir une réponse automatisée. Sous **Automatisation des incidents**, dans l’onglet **Réponse automatisée**, créez une règle d’automatisation. Cela permet de créer une réponse automatisée uniquement pour cette règle d’analyse.

- À partir de l’onglet **Règles d’automatisation** dans le panneau **Automatisation**, créez une nouvelle règle d’automatisation et spécifiez les conditions appropriées et les actions souhaitées. Cette règle d’automatisation sera appliquée à toutes les règles d’analyse qui remplissent les conditions spécifiées.

    > [!NOTE]
    > Pour exécuter un playbook à partir d’une règle d’automatisation, Azure Sentinel utilise un compte de service spécifiquement autorisé à le faire. L’utilisation de ce compte (par opposition à votre compte d’utilisateur) augmente le niveau de sécurité du service et permet à l’API de règles d’automatisation de prendre en charge les cas d’utilisation CI/CD.
    >
    > Ce compte doit disposer d’autorisations explicites sur le groupe de ressources dans lequel se trouve le playbook. À ce stade, toute règle d’automatisation sera en mesure d’exécuter n’importe quel playbook dans ce groupe de ressources.
    >
    > Lorsque vous ajoutez l’action **Exécuter le playbook** à une règle d’automatisation, une liste déroulante de playbooks s’affiche. Les playbooks pour lesquels Azure Sentinel n’a pas d’autorisations s’affichent comme étant non disponibles (« grisés »). Vous pouvez accorder l’autorisation à Azure Sentinel immédiatement en sélectionnant le lien **Gérer les autorisations des playbooks**.
    >
    > Dans un scénario à plusieurs locataires ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)), vous devez définir les autorisations sur le locataire dans lequel le playbook se trouve, même si la règle d’automatisation qui appelle le playbook est dans un locataire différent. Pour ce faire, vous devez disposer des autorisations de **propriétaire** sur le groupe de ressources du playbook.

Consultez les [instructions complètes pour la création de règles d’automatisation](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Exécuter manuellement un playbook sur une alerte

Le déclenchement manuel est disponible à partir du portail Azure Sentinel dans les panneaux suivants :

- Dans la vue **Incidents**, choisissez un incident spécifique, ouvrez son onglet **Alertes**, puis choisissez une alerte.

- Dans **Investigation**, choisissez une alerte spécifique.

1. Cliquez sur **Afficher les playbooks** de l’alerte choisie. Vous obtiendrez une liste de tous les playbooks qui commencent par **Quand une alerte Azure Sentinel est déclenchée** et auxquels vous avez accès.

1. Cliquez sur **Exécuter** sur la ligne d’un playbook spécifique pour le déclencher.

1. Sélectionnez l’onglet **Exécutions** pour afficher une liste de toutes les fois où un playbook a été exécuté sur cette alerte. L’affichage des exécutions qui viennent d’être terminées dans cette liste peut prendre quelques secondes.

1. Cliquez sur une exécution spécifique pour ouvrir le journal complet de l’exécution dans Logic Apps.

### <a name="run-a-playbook-manually-on-an-incident"></a>Exécuter manuellement un playbook sur un incident

Pas encore pris en charge. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Gérer vos playbooks

L’onglet **Playbooks** affiche une liste de tous les playbooks auxquels vous avez accès, filtrés par les abonnements qui sont actuellement affichés dans Azure. Le filtre d’abonnement est disponible dans le menu **Répertoire + abonnement** de l’en-tête global de la page.

En cliquant sur un nom de playbook, vous êtes dirigé vers la page principale du playbook dans Logic Apps. La colonne **État** indique s’il est activé ou désactivé.

Le **Type de déclencheur** représente le déclencheur Logic Apps qui démarre ce playbook.

| Type de déclencheur | Indique les types de composants dans le playbook |
|-|-|
| **Incident/Alerte Azure Sentinel** | Le playbook est démarré avec l’un des déclencheurs Sentinel (alerte, incident) |
| **Utilisation d’une action Azure Sentinel** | Le playbook est démarré avec un déclencheur non Sentinel, mais utilise une action Azure Sentinel |
| **Autres** | Le playbook n’inclut aucun composant Sentinel |
| **Non initialisé** | Le playbook a été créé, mais il ne contient aucun composant (déclencheurs ou actions). |
|

Dans la page de l’application logique du playbook, vous pouvez obtenir plus d’informations sur le playbook, y compris un journal de toutes les heures d’exécution et le résultat (réussite ou échec, ainsi que d’autres détails). Vous pouvez également entrer dans le concepteur Logic Apps et modifier le playbook directement, si vous disposez des autorisations appropriées.

### <a name="api-connections"></a>Connexions d’API

Les connexions d’API sont utilisées pour connecter Logic Apps à d’autres services. Chaque fois qu’une nouvelle authentification à un connecteur Logic Apps est effectuée, une nouvelle ressource de type **Connexion d’API** est créée et contient les informations fournies lors de la configuration de l’accès au service.

Pour afficher toutes les connexions d’API, entrez *Connexions d’API* dans la zone de recherche d’en-tête du portail Azure. Notez les colonnes suivantes :

- Nom d’affichage : le nom « convivial » que vous attribuez à la connexion chaque fois que vous en créez une.
- État : indique l’état de la connexion : erreur, connecté.
- Groupe de ressources : les connexions d’API sont créées dans le groupe de ressources de la ressource du playbook (Logic Apps).

Pour afficher les connexions d’API, vous pouvez également accéder au panneau **Toutes les ressources** et les filtrer par type de *connexion d’API*. Ainsi, vous pouvez sélectionner, marquer et supprimer plusieurs connexions à la fois.

Pour modifier l’autorisation d’une connexion existante, entrez la ressource de connexion, puis sélectionnez **Modifier la connexion d’API**.

## <a name="next-steps"></a>Étapes suivantes

- [Didacticiel : utiliser des playbooks pour automatiser les réponses aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md)
