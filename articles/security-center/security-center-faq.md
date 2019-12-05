---
title: FAQ de l’Azure Security Center | Microsoft Docs
description: Ces questions fréquentes (FAQ) répondent à des questions sur Azure Security Center, produit qui vous aide à prévenir, détecter et répondre aux menaces.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: memildin
ms.openlocfilehash: 896db06204188c4347fbdced0b1bb3f216f56ef9
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558658"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>FAQ du Centre de sécurité Azure
Cette FAQ répond aux questions concernant Azure Security Center, qui vous aide à prévenir, détecter et résoudre les menaces grâce à une meilleure visibilité et à un meilleur contrôle de la sécurité de vos ressources Microsoft Azure.

> [!NOTE]
> Security Center utilise Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Migration de plateforme Azure Security Center](security-center-platform-migration.md).
>
>

## <a name="general-questions"></a>Questions générales
### <a name="what-is-azure-security-center"></a>Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

### <a name="how-do-i-get-azure-security-center"></a>Comment obtenir le Centre de sécurité Azure ?
Azure Security Center est disponible avec votre abonnement Microsoft Azure et accessible à partir du [Portail Azure](https://azure.microsoft.com/features/azure-portal/). ([Connectez-vous au portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu’à **Centre de sécurité**).  

## <a name="billing"></a>Facturation
### <a name="how-does-billing-work-for-azure-security-center"></a>Comment fonctionne la facturation pour le Centre de sécurité Azure ?
Security Center est proposé en deux niveaux :

Le **niveau Gratuit** vous permet de voir l’état de sécurité de vos ressources Azure, vos stratégies de sécurité de base, vos recommandations de sécurité, ainsi que l’intégration des produits et services de sécurité de partenaires.

Le **niveau Standard** fournit des fonctionnalités de détection avancée des menaces, notamment des informations sur les menaces, une analyse comportementale, une détection des anomalies, des informations sur les incidents de sécurité et des rapports sur l’attribution des menaces. Vous pouvez démarrer une version d’évaluation Standard. Pour effectuer la mise à niveau, sélectionnez [Niveau tarifaire](https://docs.microsoft.com/azure/security-center/security-center-pricing) dans la stratégie de sécurité. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Comment puis-je suivre qui a effectué des modifications de niveau tarifaire dans Azure Security Center dans mon organisation
Des abonnements Azure peuvent avoir plusieurs administrateurs autorisés à modifier le niveau tarifaire. Pour savoir quel utilisateur a modifié un niveau tarifaire, utilisez le Journal d’activité Azure. Vous pourrez trouver plus d’informations [ici](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## <a name="permissions"></a>Autorisations
Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

Pour plus d’informations sur les rôles et les actions autorisées dans Security Center, consultez l’article [Permissions in Azure Security Center (Autorisations dans Azure Security Center)](security-center-permissions.md).

## <a name="data-collection-agents-and-workspaces"></a>Collecte de données, agents et espaces de travail
Azure Security Center collecte des données à partir de vos machines virtuelles Azure, groupes de machines virtuelles identiques, conteneurs IaaS et ordinateurs autres qu’Azure (y compris locaux) pour surveiller les menaces et vulnérabilités de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Est-ce que je suis facturé pour Journaux Azure Monitor sur des espaces de travail créés par Security Center ?
Non. Les espaces de travail créés par Security Center, bien qu’ils soient configurés pour une facturation Journaux Azure Monitor par nœud, n’entraînent pas de frais de journaux Azure Monitor. La facturation Security Center est toujours basée sur la stratégie de sécurité Security Center et les solutions installées sur l’espace de travail :

- **Niveau Gratuit** : Security Center active la solution « SecurityCenterFree » sur l’espace de travail par défaut. Vous n’êtes pas facturé pour le niveau Gratuit.
- **Niveau Standard** : Security Center active la solution « Security » sur l’espace de travail par défaut.

Pour plus d’informations sur la tarification, consultez la page de [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Le niveau tarifaire Log Analytics des espaces de travail créés par Security Center n’affecte pas la facturation Security Center.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Qu’est-ce qui rend une machine virtuelle apte pour le provisionnement automatique de l’installation de Microsoft Monitoring Agent ?
Les machines virtuelles Windows ou Linux IaaS sont retenues dans les cas suivants :

- L’extension Microsoft Monitoring Agent n’est pas actuellement installée sur la machine virtuelle.
- La machine virtuelle est en cours d’exécution.
- L’[agent de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) pour Windows ou Linux est installé.
- La machine virtuelle n’est pas utilisée comme pare-feu d’applications web ou comme pare-feu de nouvelle génération.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Puis-je supprimer les espaces de travail par défaut créés par Security Center ?
**Il n’est pas recommandé de supprimer l’espace de travail par défaut.** Security Center utilise les espaces de travail par défaut pour stocker les données de sécurité de vos machines virtuelles.  Si vous supprimez un espace de travail, Security Center n’est pas en mesure pas collecter ces données, et certaines recommandations de sécurité et alertes ne sont pas disponibles.

Pour effectuer une récupération, supprimez Microsoft Monitoring Agent sur les machines virtuelles connectées à l’espace de travail supprimé. Security Center réinstalle l’agent et crée des nouveaux espaces de travail par défaut.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Comment puis-je utiliser mon espace de travail Log Analytics existant ?

Vous pouvez sélectionner un espace de travail Log Analytics existant pour stocker les données collectées par Security Center. Pour utiliser votre espace de travail Log Analytics existant :

- L’espace de travail doit être associé à votre abonnement Azure sélectionné.
- Au minimum, vous devez avoir des autorisations de lecture pour accéder à l’espace de travail.

Pour sélectionner un espace de travail Log Analytics existant :

1. Dans **Stratégie de sécurité : collecte de données**, sélectionnez **Use another workspace** (Utiliser un autre espace de travail).

   ![Utiliser un autre espace de travail][5]

2. Dans le menu déroulant, sélectionnez un espace de travail pour stocker les données collectées.

   > [!NOTE]
   > Dans le menu déroulant, seuls les espaces de travail auxquels vous avez accès et se trouvant dans votre abonnement Azure sont affichés.
   >
   >

3. Sélectionnez **Enregistrer**.
4. Après avoir sélectionné **Enregistrer**, vous serez invité à reconfigurer les machines virtuelles surveillées.

   - Sélectionnez **Non** si vous souhaitez que les nouveaux paramètres de l’espace de travail **s’appliquent uniquement aux nouvelles machines virtuelles**. Les nouveaux paramètres de l’espace de travail s’appliquent uniquement aux nouvelles installations d’agent ; machines virtuelles nouvellement détectées qui n’ont pas Microsoft Monitoring Agent installé.
   - Sélectionnez **Oui** si vous souhaitez que les nouveaux paramètres de l’espace de travail **s’appliquent à toutes les machines virtuelles**. En outre, chaque machine virtuelle connectée à un espace de travail créé par Security Center est reconnectée au nouvel espace de travail cible.

   > [!NOTE]
   > Si vous sélectionnez Oui, vous ne devez pas supprimer les espaces de travail créés par Security Center tant que toutes les machines virtuelles n’ont pas été reconnectées au nouvel espace de travail cible. Cette opération échoue si un espace de travail est supprimé trop tôt.
   >
   >

   - Sélectionnez **Annuler** pour annuler l’opération.

### Que se passe-t-il si l’agent Microsoft Monitoring Agent est déjà installé en tant qu’extension sur la machine virtuelle ?<a name="mmaextensioninstalled"></a>
Lorsque l’Agent Monitoring est installé en tant qu’extension, la configuration de l’extension permet de générer uniquement un espace de travail unique. Security Center n’écrase pas les connexions existantes des espaces de travail utilisateur. Security Center stocke les données de sécurité à partir d’une machine virtuelle dans un espace de travail qui est déjà connecté, sous réserve que la solution « Security » ou « SecurityCenterFree » y soit installée. Security Center peut mettre à niveau la version de l'extension vers la dernière version lors de ce processus.

Pour plus d’informations, voir [Approvisionnement automatique en cas d’installation d’un agent préexistant](security-center-enable-data-collection.md#preexisting).


### Que se passe-t-il si Microsoft Monitoring Agent est installé directement sur la machine, mais pas en tant qu’extension (Direct Agent) ?<a name="directagentinstalled"></a>
Si l’agent Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas en tant qu’extension Azure), Security Center installe l’extension Microsoft Monitoring Agent, et peut mettre à niveau Microsoft Monitoring Agent vers la dernière version.
L’agent installé continue de rendre compte à ses espaces de travail déjà configurés et à l’espace de travail configuré dans Security Center (multihébergement pris en charge sur les machines Windows).
Si l’espace de travail configuré est un espace de travail utilisateur (pas un espace de travail par défaut de Security Center), vous devez installer la solution « Security/SecurityCenterFree » sur ce dernier pour que Security Center démarre le traitement des événements à partir des machines virtuelles et ordinateurs rendant compte à cet espace de travail.

Pour les machines Linux, le multihébergement d’agent n’est pas encore pris en charge, par conséquent, si une installation existante de l’agent est détectée, l’approvisionnement automatique n’a pas lieu et la configuration de la machine n’est pas modifiée.

Pour des machines existantes sur des abonnements intégrés à Security Center avant le 17 mars 2019, lors de la détection d’un agent existant, l’extension Microsoft Monitoring Agent n’est pas installée et la machine n’est pas affectée. Pour ces machines, consultez la recommandation « Résoudre les problèmes d’intégrité de l’agent d’analyse sur vos machines » pour résoudre les problèmes d’installation de l’agent sur ces machines.

 Pour plus d’informations, consultez la prochaine section, [Que se passe-t-il si l’agent direct System Center Operations Manager ou OMS est déjà installé sur ma machine virtuelle ?](#scomomsinstalled)

### Que se passe-t-il si un agent System Center Operations Manager est déjà installé sur ma machine virtuelle ?<a name="scomomsinstalled"></a>
Security Center installe l’extension Microsoft Monitoring Agent parallèlement à l’agent System Center Operations Manager existant. L’agent existant continue de rendre compte normalement au serveur System Center Operations Manager. Notez que l’agent Operations Manager et Microsoft Monitoring Agent partagent des bibliothèques runtime communes, qui sont mises à jour vers la dernière version lors de ce processus. Remarque : si la version 2012 de l’agent Operations Manager est installée, n’activez pas l’approvisionnement automatique (les fonctionnalités de facilité de gestion peuvent être perdues si la version du serveur Operations Manager date également de 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Quel est l’impact de la suppression de ces extensions ?
Si vous supprimez l’extension Microsoft Monitoring, Security Center n’est pas en mesure de collecter des données de sécurité sur la machine virtuelle et certaines recommandations de sécurité et les alertes ne sont pas disponibles. Sous 24 heures, Security Center détermine que l’extension est absente de la machine virtuelle et la réinstalle.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Comment empêcher l’installation automatique de l’agent et la création de l’espace de travail ?
Vous pouvez désactiver l’approvisionnement automatique pour vos abonnements dans la stratégie de sécurité, mais ce n’est pas recommandé. La désactivation de l’approvisionnement automatique a pour effet de limiter les alertes et recommandations de Security Center. Pour désactiver l’approvisionnement automatique :

1. Si votre abonnement est configuré pour le niveau Standard, ouvrez la stratégie de sécurité de cet abonnement, puis sélectionnez le niveau **Gratuit**.

   ![Niveau tarifaire][1]

2. Ensuite, désactivez l’approvisionnement automatique en sélectionnant **Non** sur le page **Stratégie de sécurité : collecte de données**.
   ![Collecte de données][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Dois-je refuser l’installation automatique de l’agent et la création de l’espace de travail ?

> [!NOTE]
> Veillez à consulter les sections [Quelles sont les implications d’un refus ?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) et [Étapes recommandées en cas de refus](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) si vous choisissez de refuser le provisionnement automatique.
>
>

Vous pouvez souhaiter refuser le provisionnement automatique si les points suivants s’appliquent à vous :

- L’installation automatique de l’agent par Security Center s’applique à l’ensemble de l’abonnement. Vous ne pouvez pas appliquer l’installation automatique à un sous-ensemble de machines virtuelles. Si des machines virtuelles critiques ne peuvent pas être installées avec Microsoft Monitoring Agent, vous devez refuser le provisionnement automatique.
- L’installation de l’extension Microsoft Monitoring Agent (MMA) met à jour la version de l’agent. Cela s’applique à un agent direct et un agent System Center Operations Manager (dans le dernier cas, les bibliothèques Operations Manager et MMA partagent des bibliothèques runtime communes, qui sont mises à jour dans le processus). Si l’agent Operations Manager installé est en version 2012 et qu’il est mis à niveau, les fonctionnalités de facilité de gestion peuvent être perdues quand le serveur Operations Manager est également en version 2012. Vous pouvez refuser le provisionnement automatique si l’agent Operations Manager installé est en version 2012.
- Si vous disposez d’un espace de travail personnalisé externe à l’abonnement (un espace de travail centralisé), vous devez refuser l’approvisionnement automatique. Vous pouvez installer manuellement l’extension Microsoft Monitoring Agent et la connecter à votre espace de travail sans que Security Center ne remplace la connexion.
- Si vous souhaitez éviter de créer plusieurs espaces de travail par abonnement et que vous disposez de votre propre espace de travail personnalisé dans l’abonnement, deux options s’offrent à vous :

   1. Vous pouvez refuser le provisionnement automatique. Après la migration, définissez les paramètres d’espace de travail par défaut comme décrit dans [Comment puis-je utiliser mon espace de travail Log Analytics existant ?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Vous pouvez aussi autoriser l’exécution de la migration, l’installation de l’agent Microsoft Monitoring Agent sur les machines virtuelles et la connexion des machines virtuelles à l’espace de travail créé. Sélectionnez ensuite votre propre espace de travail personnalisé en définissant le paramètre d’espace de travail par défaut avec l’activation de la reconfiguration des agents déjà installés. Pour plus d’informations, consultez [Comment puis-je utiliser mon espace de travail Log Analytics existant ?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quelles sont les implications d’un refus du provisionnement automatique ?
Une fois la migration terminée, Security Center n’est pas en mesure de collecter des données de sécurité sur la machine virtuelle, et certaines recommandations de sécurité et alertes ne sont pas disponibles. En cas de refus, installez Microsoft Monitoring Agent manuellement. Consultez les [étapes recommandées en cas de refus](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quelles sont les étapes recommandées en cas de refus du provisionnement automatique ?

Installez manuellement l’extension Microsoft Monitoring Agent pour que Security Center puisse collecter des données de sécurité sur vos machines virtuelles et fournir des suggestions et des alertes. Consultez [Installation de l’agent pour les machines virtuelles Windows](../virtual-machines/extensions/oms-windows.md) ou [Installation de l’agent pour les machines virtuelles Linux](../virtual-machines/extensions/oms-linux.md) pour obtenir des instructions sur l’installation.

Vous pouvez connecter l’agent à n’importe quel espace de travail personnalisé existant ou à l’espace de travail créé par Security Center. Si les solutions « Security » ou « SecurityCenterFree » ne sont pas activées pour un espace de travail personnalisé, vous devez appliquer une solution. Pour ce faire, sélectionnez l’abonnement ou l’espace de travail personnalisé, puis appliquez un niveau tarifaire via la page **Stratégie de sécurité – Niveau tarifaire**.

   ![Niveau tarifaire][1]

Security Center active la solution appropriée sur l’espace de travail en fonction du niveau tarifaire sélectionné.

### Comment supprimer des extensions OMS installées par Security Center ?<a name="remove-oms"></a>
Vous pouvez supprimer manuellement l’agent Microsoft Monitoring Agent. Ce n’est pas recommandé car cela limite les recommandations de Security Center et les alertes.

> [!NOTE]
> Si la collecte de données est activée, Security Center réinstalle l’agent après l’avoir supprimé.  Vous devez désactiver la collecte de données avant de supprimer manuellement l’agent. Consultez Comment empêcher l’installation automatique de l’agent et la création de l’espace de travail ? pour obtenir des instructions sur la désactivation de la collecte de données.
>
>

Pour supprimer manuellement l’agent :

1.  Dans le portail, ouvrez **Log Analytics**.
2.  Sur la page Log Analytics, sélectionnez un espace de travail :
3.  Sélectionnez les machines virtuelles que vous ne souhaitez pas surveiller, puis choisissez **Déconnecter**.

   ![Supprimer l’agent][3]

> [!NOTE]
> Si une machine virtuelle Linux possède déjà un agent OM qui n’est pas un agent d’extension, la suppression de l’extension a pour effet de supprimer également l’agent. Le client doit alors le réinstaller.
>
>
### <a name="how-do-i-disable-data-collection"></a>Comment désactiver la collecte des données ?
L’approvisionnement automatique est désactivé par défaut. Vous pouvez désactiver l’approvisionnement automatique à partir des ressources à tout moment en désactivant ce paramètre dans la stratégie de sécurité. L’approvisionnement automatique est fortement recommandé si vous souhaitez obtenir des alertes de sécurité et des recommandations sur les mises à jour système, les vulnérabilités du système d’exploitation et la protection du point de terminaison.

Pour désactiver la collecte des données, [connectez-vous au Portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, **Centre de sécurité**, puis **Sélectionner une stratégie**. Sélectionnez l’abonnement pour lequel vous souhaitez désactiver l’approvisionnement automatique. Lorsque vous sélectionnez un abonnement, **Stratégie de sécurité - Collecte de données** s’ouvre. Sous **Auto provisioning** (Approvisionnement automatique), sélectionnez **Off** (Désactivé).

### <a name="how-do-i-enable-data-collection"></a>Comment activer la collecte des données ?
Vous pouvez activer la collecte des données pour votre abonnement Azure dans la stratégie de sécurité. Pour activer la collecte de données. [Connectez-vous au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, **Centre de sécurité**, puis **Stratégie de sécurité**. Sélectionnez l’abonnement pour lequel vous souhaitez activer l’approvisionnement automatique. Lorsque vous sélectionnez un abonnement, **Stratégie de sécurité - Collecte de données** s’ouvre. Sous **Auto provisioning** (Approvisionnement automatique), sélectionnez **On** (Activé).

### <a name="what-happens-when-data-collection-is-enabled"></a>Que se passe-t-il quand la collecte des données est activée ?
Lorsque l’approvisionnement automatique est activé, Security Center approvisionne Microsoft Monitoring Agent pour toutes les machines virtuelles Azure prises en charge et toutes celles nouvellement créées. L’approvisionnement automatique est recommandé. Toutefois, l’installation manuelle de l’agent est également disponible. [Découvrez comment installer l’extension Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) 

L’agent active l’événement de création de processus 4688 et le champ *CommandLine* à l’intérieur de l’événement 4688. Les nouveaux processus créés sur la machine virtuelle sont enregistrés par le journal des événements et analysés par les services de détection Security Center. Pour plus d’informations sur les détails enregistrés pour chaque nouveau processus, voir [Description des champs 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L’agent collecte également les événements 4688 créés sur la machine virtuelle et les stocke dans la recherche.

L’agent permet également d’activer la collecte de données pour les [Contrôles d’application adaptatifs](security-center-adaptive-application.md), Security Center configure une stratégie AppLocker locale en mode Audit pour autoriser toutes les applications. Cette stratégie amène AppLocker à générer des événements qui sont ensuite recueillis et exploités par Security Center. Il est important de noter que cette stratégie ne sera configurée sur aucun ordinateur sur lequel une stratégie AppLocker est déjà configurée. 

Lorsque Security Center détecte une activité suspecte sur la machine virtuelle, le client est averti par e-mail si [des informations de contact de sécurité](security-center-provide-security-contact-details.md) ont été fournies. Une alerte est également visible dans le tableau de bord d’alertes de sécurité de Security Center.

### <a name="will-security-center-work-using-an-oms-gateway"></a>Security Center fonctionne-t-il avec une passerelle OMS ?
Oui. Azure Security Center utilise Azure Monitor pour collecter des données à partir de machines virtuelles et de serveurs Azure, à l’aide de Microsoft Monitoring Agent.
Pour collecter les données, chaque machine virtuelle et chaque serveur doivent se connecter à Internet à l’aide du protocole HTTPS. Il peut s’agir d’une connexion directe, d’une connexion obtenue par un proxy ou bien établie via la [passerelle OMS](../azure-monitor/platform/gateway.md).

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Est-ce que l’agent d’analyse a un impact sur les performances de mes serveurs ?
L’agent utilise une quantité minime de ressources système et n’a donc qu’un faible impact sur les performances. Pour en savoir plus sur l’impact sur les performances, l’agent et l’extension, consultez le [Guide de planification et de fonctionnement](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Où sont stockées mes données ?
Les données collectées à partir de cet agent sont stockées dans un espace de travail Log Analytics existant associé à votre abonnement Azure ou dans un nouvel espace de travail. Pour plus d’informations, consultez [Sécurité des données](security-center-data-security.md).

## Clients Journaux Azure Monitor existants<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Est-ce que Security Center peut écraser les connexions existantes entre les machines virtuelles et les espaces de travail ?
Si l’agent Microsoft Monitoring Agent est déjà installé sur une machine virtuelle comme une extension Azure, Security Center n’écrase pas la connexion à l’espace de travail existante. En revanche, Security Center utilise l’espace de travail existant. La machine virtuelle est protégée si la solution « Security » ou « SecurityCenterFree » est installée sur l’espace de travail auquel elle rend compte. 

Une solution de Security Center est installée sur l’espace de travail sélectionné dans l’écran Collection de données, si elle ne l’était pas déjà. La solution est appliquée uniquement aux machines virtuelles appropriées. Lorsque vous ajoutez une solution, elle est déployée automatiquement par défaut sur tous les agents Windows et Linux connectés à votre espace de travail Log Analytics. Le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) permet d’appliquer une étendue à vos solutions.

Si Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas en tant qu’extension Azure), Security Center n’installe pas Microsoft Monitoring Agent et la surveillance de la sécurité est limitée.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Est-ce que Security Center installe des solutions sur mes espaces de travail Log Analytics existants ? Quelles sont les conséquences sur la facturation ?
Lorsque Security Center détecte qu’une machine virtuelle est déjà connectée à un espace de travail que vous avez créé, il active des solutions sur cet espace de travail en fonction de votre niveau tarifaire. Les solutions sont appliquées uniquement aux machines virtuelles Azure pertinentes, via le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md), de sorte que la facturation reste la même.

- **Niveau Gratuit** : Security Center installe la solution « SecurityCenterFree » sur l’espace de travail. Vous n’êtes pas facturé pour le niveau Gratuit.
- **Niveau Standard** : Security Center installe la solution « Security » sur l’espace de travail.

   ![Solutions sur l’espace de travail par défaut][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Je dispose déjà d’espaces de travail dans mon environnement. Puis-je les utiliser pour collecter des données de sécurité ?
Si l’agent Microsoft Monitoring Agent est déjà installé comme une extension Azure sur une machine virtuelle, Security Center utilise l’espace de travail connecté existant. Une solution de Security Center est installée sur l’espace de travail si elle ne l’était pas déjà. La solution est appliquée uniquement aux machines virtuelles appropriées via le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md).

Lorsque Security Center installe l’agent Microsoft Monitoring Agent sur des machines virtuelles, il utilise les espaces de travail par défaut créés par Security Center.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Je dispose déjà de la solution de sécurité sur mes espaces de travail. Quelles sont les conséquences sur la facturation ?
La solution Security & Audit permet d’activer les fonctionnalités Security Center de niveau Standard pour les machines virtuelles Azure. Si la solution Security & Audit est déjà installée sur un espace de travail, Security Center utilise la solution existante. Il n’y aura aucune modification de la facturation.

## <a name="using-azure-security-center"></a>Utilisation du Centre de sécurité Azure
### <a name="what-is-a-security-policy"></a>Qu’est-ce qu’une stratégie de sécurité ?
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement spécifique. Dans Azure Security Center, vous définissez les stratégies de vos abonnements Azure en fonction des exigences de sécurité de votre entreprise et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Les stratégies de sécurité activées dans Azure Security Center déterminent les recommandations et la surveillance liées à la sécurité. Pour plus d’informations sur les stratégies de sécurité, consultez [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Qui peut modifier une stratégie de sécurité ?
Pour modifier une stratégie de sécurité, vous devez avoir le rôle d’administrateur de la sécurité, de propriétaire ou de collaborateur pour l’abonnement concerné.

Pour savoir comment configurer une stratégie de sécurité, consultez [Définition de stratégies de sécurité dans le Centre de sécurité Azure](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>Qu’est-ce qu’une recommandation de sécurité ?
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Quand des failles de sécurité potentielles sont identifiées, des recommandations sont créées. Les recommandations vous guident tout au long du processus de configuration du contrôle. Voici quelques exemples :

* Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
* [Groupes de sécurité réseau](../virtual-network/security-overview.md) et règles pour contrôler le trafic vers les machines virtuelles
* Approvisionnement d’un pare-feu d’applications web pour se défendre des attaques ciblant vos applications web
* Déploiement de mises à jour système manquantes
* Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

Seules les recommandations qui sont activées dans les stratégies de sécurité sont affichées ici.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Comment connaître l’état de sécurité actuel de mes ressources Azure ?
La page **Vue d’ensemble de Security Center** affiche les mesures de sécurité globales de votre environnement pour les ressources suivantes : Compute, Mise en réseau, Stockage et données, et Applications. Chaque type de ressource possède un indicateur montrant la présence éventuelle de failles de sécurité. Cliquez sur chaque vignette pour afficher une liste des problèmes de sécurité identifiés par Security Center, ainsi qu’un inventaire des ressources dans votre abonnement.

### <a name="what-triggers-a-security-alert"></a>Qu’est-ce qui déclenche une alerte de sécurité ?
Azure Security Center collecte, analyse et fusionne automatiquement les données du journal à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. Quand des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détections :

* Des machines virtuelles compromises qui communiquent avec des adresses IP connues comme étant malveillantes
* Des programmes malveillants avancés qui sont détectés à l’aide du rapport d’erreurs Windows
* Des attaques par force brute contre des machines virtuelles
* Des alertes de sécurité émises par des solutions de sécurité partenaires intégrées, telles que des logiciels anti-programme malveillant ou des pare-feu d’applications web

### Pourquoi les valeurs des scores de sécurité ont-elles été modifiées ? <a name="secure-score-faq"></a>
Depuis février 2019, Security Center a ajusté le score de quelques recommandations, afin de mieux adapter leur niveau de gravité. À la suite de cet ajustement, l’ensemble des valeurs des scores de sécurité sont susceptibles d’avoir été modifiées.  Pour plus d’informations sur le score de sécurité, consultez [Calcul des scores de sécurité](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Quelle est la différence entre les menaces détectées et faisant l’objet d’une alerte par Microsoft Security Response Center et par Azure Security Center ?
Microsoft Security Response Center (MSRC) effectue certaines analyses de sécurité sur l'infrastructure et le réseau Azure et reçoit des informations sur les menaces et des plaintes pour mauvaise utilisation provenant de tiers. Lorsque MSRC constate que les données client ont été utilisées par un tiers illégal ou non autorisé ou que l'utilisation d’Azure par le client ne respecte pas les conditions de bon usage, un gestionnaire des incidents de sécurité informe le client. La notification correspond généralement à un courrier électronique envoyé aux contacts de sécurité spécifiés dans Azure Security Center ou au propriétaire de l’abonnement Azure si aucun contact de sécurité n’est spécifié.

Security Center est un service Azure qui surveille en continu l'environnement Azure du client et applique des analyses de façon à détecter automatiquement un large éventail d'activités potentiellement malveillantes. Ces détections sont signalées en tant qu'alertes de sécurité dans le tableau de bord du Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quelles ressources Azure sont surveillées par Azure Security Center ?
Azure Security Center surveille les ressources Azure suivantes :

* Machines virtuelles (VM) (y compris [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Groupes identiques de machines virtuelles
* Réseaux virtuels Azure
* Service SQL Azure
* un compte Azure Storage.
* Azure Web Apps (dans [App Service Environment](../app-service/environment/intro.md))
* Solutions de partenaires intégrées à votre abonnement Azure, par exemple pare-feu d’applications web sur les machines virtuelles et sur App Service Environment

En outre, les ordinateurs non Azure (y compris en local) peuvent également être analysés par Azure Security Center (les [ordinateurs Windows](./quick-onboard-windows-computer.md) et [Linux](./quick-onboard-linux-computer.md) sont pris en charge)

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Quels sont les types de machines virtuelles pris en charge ?
Le monitoring et des recommandations sont disponibles pour les machines virtuelles créées à l’aide des [modèles de déploiement Classic et Resource Manager](../azure-classic-rm.md).

Consultez [Plateformes prises en charge dans Azure Security Center](security-center-os-coverage.md) pour obtenir la liste des plateformes prises en charge.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Pourquoi Azure Security Center ne reconnaît-il pas la solution anti-programme malveillant de ma machine virtuelle Azure ?
Azure Security Center bénéficie d’une visibilité sur les logiciels anti-programme malveillant installés par le biais des extensions Azure. Par exemple, Security Center n’est pas en mesure de détecter les logiciels anti-programme malveillant préinstallés sur une image que vous avez fournie ou installés sur vos machines virtuelles à l’aide de votre propre processus (notamment les systèmes de gestion de la configuration).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Pourquoi reçois-je le message « Données d’analyse manquantes » pour ma machine virtuelle ?
Ce message s’affiche lorsqu’il n’existe aucune donnée d’analyse pour une machine virtuelle. Le remplissage des données d’analyse peut prendre un certain temps (inférieur à une heure) une fois la collecte de données activée dans Azure Security Center. Après le remplissage initial des données d’analyse, vous pouvez recevoir ce message s’il n’existe aucune donnée d’analyse ou s’il n’existe pas de données d’analyse récentes. Le remplissage des analyses n’est pas effectué pour une machine virtuelle à l’état Arrêté. Ce message peut également s’afficher si les données d’analyse n’ont pas été renseignées récemment (conformément à la stratégie de rétention de l’agent Windows, dont la valeur par défaut est 30 jours).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>À quelle fréquence Security Center recherche-t-il les vulnérabilités du système d’exploitation, les mises à jour du système et les problèmes de protection du point de terminaison ?
Les temps de latence des analyses de Security Center en lien avec les vulnérabilités, les mises à jour et les problèmes figurent ci-dessous :

- Configuration de la sécurité du système d’exploitation ; les données sont mises à jour dans les 48 heures
- Mises à jour du système ; les données sont mises à jour dans les 24 heures
- Problèmes de protection du point de terminaison ; les données sont mises à jour dans les 8 heures

Généralement, Security Center analyse les nouvelles données toutes les heures et actualise les suggestions en conséquence. 

> [!NOTE]
> Security Center utilise Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Migration de plateforme Azure Security Center](security-center-platform-migration.md).
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Pourquoi reçois-je le message « Agent de machine virtuelle manquant » ?
L’agent de machine virtuelle doit être installé sur les machines virtuelles pour activer la collecte des données. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. Pour plus d’informations sur l’installation de l’agent de machine virtuelle sur d’autres machines virtuelles, consultez l’article de blog [Agent de machine virtuelle et extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
