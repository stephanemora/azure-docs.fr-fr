---
title: Utiliser Monitor Windows Virtual Desktop Monitor – Azure
description: Comment utiliser Azure Monitor pour Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7da35f77dd232e5f523e2bdc3f125c68015ab871
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448183"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Monitoring d’un déploiement avec Azure Monitor pour Windows Virtual Desktop

Azure Monitor pour Windows Virtual Desktop est un tableau de bord basé sur des classeurs Azure Monitor qui aide les professionnels de l’informatique à comprendre leurs environnements Windows Virtual Desktop. Cette rubrique vous guide tout au long de la configuration d’Azure Monitor pour Windows Virtual Desktop pour surveiller vos environnements Windows Virtual Desktop.

## <a name="requirements"></a>Spécifications

Avant de commencer à utiliser Azure Monitor pour Windows Virtual Desktop, vous devez configurer les éléments suivants :

- Tous les environnements Windows Virtual Desktop que vous analysez doivent être basés sur la dernière version de Windows Virtual Desktop compatible avec Azure Resource Manager.
- Au moins un espace de travail Log Analytics configuré. Utilisez un espace de travail Log Analytics désigné pour vos hôtes de session Windows Virtual Desktop pour vous assurer que les compteurs de performances et les événements sont collectés uniquement à partir d’hôtes de session dans votre déploiement Windows Virtual Desktop.
- Activez la collecte de données pour les éléments suivants dans votre espace de travail Log Analytics :
    - Diagnostics de votre environnement Windows Virtual Desktop
    - Compteurs de performances recommandés en fonction de vos hôtes de session Windows Virtual Desktop
    - Journaux des événements Windows recommandés en fonction de vos hôtes de session Windows Virtual Desktop

 Le processus de configuration des données décrit dans cet article est le seul dont vous aurez besoin pour surveiller Windows Virtual Desktop. Vous pouvez désactiver tous les autres éléments envoyant des données à votre espace de travail Log Analytics pour réduire les coûts.

Toute personne surveillant Azure Monitor pour Windows Virtual Desktop pour votre environnement aura également besoin des autorisations d’accès en lecture suivantes :

- Accès en lecture aux abonnements Azure qui contiennent vos ressources Windows Virtual Desktop
- Accès en lecture aux groupes de ressources de l'abonnement qui contiennent vos hôtes de session Windows Virtual Desktop
- Accès en lecture à l’espace de travail ou aux espaces de travail Log Analytics

>[!NOTE]
> L’accès en lecture permet uniquement aux administrateurs d’afficher les données. Ils ont besoin d’autorisations différentes pour gérer les ressources dans le portail Windows Virtual Desktop.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Ouvrir Azure Monitor pour Windows Virtual Desktop

Vous pouvez ouvrir Azure Monitor pour Windows Virtual Desktop avec l’une des méthodes suivantes :

- Accédez à [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi).
- Recherchez et sélectionnez **Windows Virtual Desktop** dans le Portail Azure, puis sélectionnez **Insights**.
- Recherchez et sélectionnez **Azure Monitor** dans le Portail Azure. Sélectionnez **Hub Insights** sous **Insights**, puis **Windows Virtual Desktop**.
Une fois la page ouverte, entrez l’**Abonnement**, le **Groupe de ressources**, le **Pool d’hôtes** et l’**Intervalle de temps** de l’environnement à surveiller.

>[!NOTE]
>Actuellement, Windows Virtual Desktop prend uniquement en charge l’analyse d’un abonnement, d’un groupe de ressources et d’un pool d’hôtes à la fois. Si vous ne trouvez pas l’environnement que vous souhaitez analyser, consultez [notre documentation de dépannage](troubleshoot-azure-monitor.md) pour connaître les demandes de fonctionnalités et problèmes connus.

## <a name="log-analytics-settings"></a>Paramètres de Log Analytics

Pour commencer à utiliser Azure Monitor pour Windows Virtual Desktop, vous avez besoin d’au moins un espace de travail Log Analytics. Utilisez un espace de travail Log Analytics désigné pour vos hôtes de session Windows Virtual Desktop pour vous assurer que les compteurs de performances et les événements sont collectés uniquement à partir d’hôtes de session dans votre déploiement Windows Virtual Desktop. Si vous disposez déjà d’un espace de travail configuré, passez directement à la section [Configurer à l’aide du classeur de configuration](#set-up-using-the-configuration-workbook). Pour en configurer un, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Des frais de stockage de données standard pour Log Analytics s’appliqueront. Pour commencer, nous vous recommandons de choisir le modèle de paiement à l’utilisation et de l’ajuster à mesure que vous mettez à l’échelle votre déploiement et que vous prenez plus de données. Pour en savoir plus, consultez la section [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Configurer à l’aide du classeur de configuration

La première fois que vous ouvrez Azure Monitor pour Windows Virtual Desktop, vous devez configurer Azure Monitor pour votre environnement Windows Virtual Desktop. Pour configurer vos ressources :

1. Ouvrez Azure Monitor pour Windows Virtual Desktop dans le portail Azure à l’adresse [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi), puis sélectionnez **Classeur de configuration**.
2. Sélectionnez un environnement à configurer sous **Abonnement**, **Groupe de ressources** et **Pool d’hôtes**.

Le classeur de configuration configure votre environnement d’analyse et vous permet de vérifier la configuration une fois que vous avez terminé le processus d’installation. Il est important de vérifier votre configuration si les éléments du tableau de bord ne s’affichent pas correctement, ou lorsque le groupe de produits publie des mises à jour qui nécessitent de nouveaux paramètres.

### <a name="resource-diagnostic-settings"></a>Paramètres de diagnostic des ressources

Pour collecter des informations sur votre infrastructure Windows Virtual Desktop, vous devez activer plusieurs paramètres de diagnostic sur vos pools d’hôtes et espaces de travail Windows Virtual Desktop (il s’agit de votre espace de travail Windows Virtual Desktop, pas de l’espace de travail Log Analytics). Pour en savoir plus sur les pools d’hôtes, les espaces de travail et d’autres objets ressources Windows Virtual Desktop, consultez notre [guide de l’environnement](environment-setup.md).

Pour en savoir plus sur les diagnostics Windows Virtual Desktop et les tables de diagnostics prises en charge, consultez [Envoyer des diagnostics Windows Virtual Desktop à log Analytics](diagnostics-log-analytics.md).

Pour définir vos paramètres de diagnostic des ressources, dans le classeur de configuration : 

1. Dans le classeur configuration, sélectionnez l’onglet **Paramètres de diagnostic des ressources**. 
2. Sélectionnez **Espace de travail Log Analytics** pour envoyer les diagnostics Windows Virtual Desktop. 

#### <a name="host-pool-diagnostic-settings"></a>Paramètres de diagnostic du pool d’hôtes

Pour configurer les diagnostics du pool d’hôtes à l’aide de la section Paramètres de diagnostic des ressources du classeur de configuration :

1. Sous **Pool d’hôtes**, vérifiez si les diagnostics Windows Virtual Desktop sont activés. Si ce n’est pas le cas, un message d’erreur s’affiche indiquant qu’aucune configuration de diagnostics existante n’a été trouvée pour le pool d’hôtes sélectionné. Vous allez devoir activer les tables de diagnostics prises en charge suivantes :

    - Point de contrôle
    - Error
    - Gestion
    - Connexion
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Si vous ne voyez pas le message d’erreur, vous n’avez pas besoin de suivre les étapes 2 à 4.

2. Sélectionnez **Configurer le pool d’hôtes**.
3. Sélectionnez **Déployer**.
4. Actualisez le classeur de configuration.

#### <a name="workspace-diagnostic-settings"></a>Paramètres de diagnostic de l’espace de travail 

Pour configurer les diagnostics de l’espace de travail à l’aide de la section Paramètres de diagnostic des ressources du classeur de configuration :

 1. Sous **Espace de travail**, vérifiez si les diagnostics Windows Virtual Desktop sont activés pour l’espace de travail Windows Virtual Desktop. Si ce n’est pas le cas, un message d’erreur s’affiche indiquant qu’aucune configuration de diagnostics existante n’a été trouvée pour l’espace de travail sélectionné. Vous allez devoir activer les tables de diagnostics prises en charge suivantes :
 
    - Point de contrôle
    - Error
    - Gestion
    - Flux
    
    >[!NOTE]
    > Si vous ne voyez pas le message d’erreur, vous n’avez pas besoin de suivre les étapes 2 à 4.

2. Sélectionnez **Configurer l’espace de travail**.
3. Sélectionnez **Déployer**.
4. Actualisez le classeur de configuration.

### <a name="session-host-data-settings"></a>Paramètres des données d’hôte de session

Pour collecter des informations sur vos hôtes de session Windows Virtual Desktop, vous devez installer l’agent Log Analytics sur tous les hôtes de session dans le pool hôte, vous assurer que les hôtes de session envoient des données à un espace de travail Log Analytics, et configurer les paramètres de votre agent Log Analytics pour qu’il collecte les données de performances et les journaux des événements Windows.

L’espace de travail Log Analytics auquel vous envoyez les données d’hôte de session n’a pas besoin d’être le même que celui auquel vous envoyez les données de diagnostic. Si vous avez des hôtes de session Azure en dehors de votre environnement Windows Virtual Desktop, nous vous recommandons d’avoir un espace de travail Log Analytics désigné pour les hôtes de session Windows Virtual Desktop. 

Pour définir l’espace de travail Log Analytics dans lequel vous souhaitez collecter les données d’hôte de session : 

1. Dans le classeur de configuration, sélectionnez l’onglet **Paramètres des données d’hôte de session**. 
2. Sélectionnez l’**espace de travail Log Analytics** pour l’envoi des données d’hôte de session. 

#### <a name="session-hosts"></a>Hôtes de session

Vous allez devoir installer l’agent Log Analytics sur tous les hôtes de session du pool d’hôtes pour envoyer des données de ces hôtes à votre espace de travail Log Analytics sélectionné. Si Log Analytics n’est pas configuré pour tous les hôtes de session du pool d’hôtes, vous verrez une section **Hôtes de session** en haut de l’onglet **Paramètres des données d’hôte de session**, avec le message « Certains hôtes dans le pool d’hôtes n’envoient pas de données à l’espace de travail Log Analytics sélectionné. »

>[!NOTE]
> Si vous ne voyez pas la section **Hôtes de session** ou le message d’erreur, cela signifie que tous les hôtes de session sont configurés correctement. Passez aux instructions de configuration pour les [Compteurs de performances de l’espace de travail](#workspace-performance-counters).

Pour configurer vos hôtes de session restants à l’aide du classeur de configuration :

1. Sélectionnez **Ajouter des hôtes à l’espace de travail**. 
2. Actualisez le classeur de configuration.

>[!NOTE]
>L’ordinateur hôte doit être en cours d’exécution pour installer l’extension de Log Analytics. Si le déploiement automatique ne fonctionne pas, vous pouvez installer manuellement l’extension sur un hôte. Pour savoir comment installer l’extension manuellement, consultez [Extension de machine virtuelle Log Analytics pour Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Compteurs de performances de l’espace de travail

Vous allez devoir activer des compteurs de performances spécifiques pour collecter des informations sur les performances de vos hôtes de session et les envoyer à l’espace de travail Log Analytics.

Si vous avez déjà activé des compteurs de performances et que vous souhaitez les supprimer, suivez les instructions de [Configuration des compteurs de performances](../azure-monitor/agents/data-sources-performance-counters.md). Vous pouvez ajouter et supprimer des compteurs de performances au même emplacement.

Pour configurer des compteurs de performances à l’aide du classeur de configuration : 

1. Dans le classeur de configuration, sous **Compteurs de performances de l’espace de travail**, vérifiez les **Compteurs configurés** pour voir les compteurs que vous avez déjà activés pour l’envoi de données à l’espace de travail Log Analytics. Vérifiez les **Compteurs manquants** pour vous assurer que vous avez activé tous les compteurs requis.
2. Si des compteurs sont manquants, sélectionnez **Configurer les compteurs de performances**.
3. Sélectionnez **Appliquer la configuration**.
4. Actualisez le classeur de configuration.
5. Vérifiez que tous les compteurs requis sont activés en consultant la liste **Compteurs manquants**. 

#### <a name="configure-windows-event-logs"></a>Configurer des journaux des événements Windows

Vous allez également devoir activer des journaux des événements Windows spécifiques pour collecter les erreurs, les avertissements et les informations des hôtes de session, et les envoyer à l’espace de travail Log Analytics.

Si vous avez déjà activé les journaux des événements Windows et souhaitez les supprimer, suivez les instructions fournies dans [Configuration des journaux des événements Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Vous pouvez ajouter et supprimer des journaux des événements Windows au même emplacement.

Pour configurer les journaux des événements Windows à l’aide du classeur de configuration :

1. Sous **Configuration des journaux des événements Windows**, vérifiez les **Journaux des événements configurés** pour voir les journaux des événements que vous avez déjà activés pour l’envoi de données à l’espace de travail log Analytics. Vérifiez les **Journaux des événements manquants** pour vous assurer que vous avez activé tous les journaux des événements Windows.
2. Si des journaux des événements Windows sont manquants, sélectionnez **Configurer des événements**.
3. Sélectionnez **Déployer**.
4. Actualisez le classeur de configuration.
5. Vérifiez que tous les journaux des événements Windows requis sont activés en consultant la liste **Journaux des événements manquants**. 

>[!NOTE]
>Si le déploiement d’événement automatique échoue, dans le classeur de configuration, sélectionnez **Ouvrir la configuration de l’agent** pour ajouter manuellement tous les journaux des événements Windows manquants. 

## <a name="optional-configure-alerts"></a>Facultatif : configurer des alertes

Azure Monitor pour Windows Virtual Desktop vous permet de surveiller les alertes Azure Monitor qui se produisent dans votre abonnement sélectionné dans le contexte de vos données Windows Virtual Desktop. Les alertes Azure Monitor sont une fonctionnalité facultative de vos abonnements Azure, et vous devez les configurer séparément d’Azure Monitor pour Windows Virtual Desktop. Vous pouvez utiliser l’infrastructure d’alertes Azure Monitor pour définir des alertes personnalisées sur des événements, diagnostics et ressources Windows Virtual Desktop. Pour en savoir plus sur les alertes Azure Monitor, consultez [Répondre à des événements avec des alertes Azure Monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation

Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Azure Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service.

Pour fournir des fonctionnalités de résolution des problèmes précises et efficaces, les données collectées incluent l’ID de session du portail, l’ID d’utilisateur Azure Active Directory et le nom de l’onglet du portail dans lequel l’événement s’est produit. Microsoft ne collecte pas de nom, d’adresse ou d’autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, voir la [Déclaration de confidentialité des Services en ligne de Microsoft](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Pour en savoir plus sur l’affichage ou la suppression des données personnelles collectées par le service, consultez [Requêtes DSR (droits de la personne concernée) Azure pour le RGPD](/microsoft-365/compliance/gdpr-dsr-azure). Pour plus d’informations sur le RGPD, consultez la [section RGPD du portail Service Trust](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré Azure Monitor pour votre environnement Windows Virtual Desktop, voici quelques ressources qui peuvent vous aider à démarrer la surveillance de votre environnement :

- Consultez notre [glossaire](azure-monitor-glossary.md) pour en savoir plus sur les termes et les concepts liés à Azure Monitor pour Windows Virtual Desktop.
- Pour estimer, mesurer et gérer les coûts de stockage de vos données, consultez [Estimer les coûts d’Azure Monitor](azure-monitor-costs.md).
- Si vous rencontrez un problème, consultez notre [guide de résolution des problèmes](troubleshoot-azure-monitor.md) pour obtenir de l’aide en lien avec les problèmes connus.
