---
title: Connecter Azure Sentinel à des services Azure, Windows et Microsoft
description: Découvrez comment connecter Azure Sentinel aux services cloud Azure et Microsoft 365, ainsi qu’aux journaux des événements Windows Server.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/18/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6f8a4d8c7d049241354a5cdeaf1613edfbe71b19
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131047570"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Connecter Azure Sentinel aux services Azure, Windows, Microsoft et Amazon

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel utilise la fondation Azure afin de fournir une prise en charge intégrée de service à service pour l’ingestion de données à partir de nombreux services Azure et Microsoft 365, d’Amazon Web Services et de divers services Windows Server. Plusieurs méthodes permettent d’établir ces connexions et cet article explique comment procéder.

Cet article décrit les types de connecteurs suivants :

- Connexions **basées sur une API**
- Connexions de **paramètres de diagnostic**, dont certaines sont gérées par Azure Policy
- Connexions basées sur un **agent Log Analytics**

Cet article présente des informations communes aux groupes de connecteurs. Pour obtenir des informations propres à chaque connecteur, telles que les conditions préalables pour les licences et les tables Log Analytics pour le stockage des données, consultez la page de [référence du connecteur de données](data-connectors-reference.md) associée.

Les intégrations suivantes sont à la fois plus uniques et plus populaires, et sont traitées individuellement avec leurs propres articles :

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Azure Defender](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Événements de sécurité Windows](connect-windows-security-events.md)
- [Amazon Web Services (AWS) CloudTrail](connect-aws.md)


## <a name="api-based-connections"></a>Connexions basées sur une API

### <a name="prerequisites"></a>Configuration requise

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Log Analytics.
- Vous devez disposer du rôle Administrateur général ou Administrateur de la sécurité sur le locataire de votre espace de travail Azure Sentinel.

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre service dans la galerie de connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Sélectionnez **Connecter** pour démarrer la diffusion en continu d’événements ou d’alertes à partir de votre service dans Azure Sentinel.

1. Si la page du connecteur contient une section intitulée **Créer des incidents (recommandé)** , sélectionnez **Activer** si vous souhaitez créer automatiquement des incidents à partir d’alertes.

Vous pouvez rechercher et interroger les données pour chaque service à l’aide des noms de table qui s’affichent dans la section du connecteur du service dans la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="diagnostic-settings-based-connections"></a>Connexions basées sur des paramètres de diagnostic

La configuration de certains connecteurs de ce type est gérée par Azure Policy. Sélectionnez l’onglet **Azure Policy** ci-dessous pour obtenir des instructions. Pour les autres connecteurs de ce type, sélectionnez l’onglet **Autonome**.

# <a name="standalone"></a>[Autonome](#tab/SA)

### <a name="prerequisites"></a>Prérequis

Pour ingérer des données dans Azure Sentinel :

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre type de ressource dans la galerie de connecteurs de données, puis choisissez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, sélectionnez le lien pour ouvrir la page de configuration des ressources.

    Si une liste de ressources du type souhaité s’affiche, sélectionnez le lien d’une ressource dont vous souhaitez ingérer les journaux.

1. Dans le menu de navigation de la ressource, sélectionnez **Paramètres de diagnostic**.

1. Sélectionnez **+ Ajouter un paramètre de diagnostic** en bas de la liste.

1. Dans l’écran **Paramètres de diagnostic**, entrez un nom dans le champ **Nom des paramètres de diagnostic**.

    Activez la case à cocher **Envoyer à Log Analytics**. Deux nouveaux champs s’affichent sous celle-ci. Choisissez l’**Abonnement** approprié et l’**Espace de travail Log Analytics** (où se trouve Azure Sentinel).

1. Activez les cases à cocher des types de journaux et de métriques que vous souhaitez collecter. Consultez nos choix recommandés pour chaque type de ressource dans la section pour le connecteur de la ressource dans la page [Référence des connecteurs de données](data-connectors-reference.md).

1. En haut de l’écran, sélectionnez **Enregistrer**.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>Prérequis

Pour ingérer des données dans Azure Sentinel :

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Pour utiliser Azure Policy afin d’appliquer une stratégie de streaming de journaux à vos ressources, vous devez avoir le rôle Propriétaire pour l’étendue d’attribution de stratégie.

### <a name="instructions"></a>Instructions

Les connecteurs de ce type utilisent Azure Policy pour appliquer une configuration de paramètres de diagnostic unique à une collection de ressources d’un type unique, définie en tant qu’étendue. Vous pouvez voir les types de journaux ingérés à partir d’un type de ressource donné sur le côté gauche de la page du connecteur pour cette ressource, sous **Types de données**.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre type de ressource dans la galerie de connecteurs de données, puis choisissez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, développez toutes les sections que vous voyez, puis sélectionnez le bouton **Lancer l’Assistant Attribution Azure Policy**.

    L’Assistant Attribution de stratégie s’ouvre, prêt à créer une stratégie, avec un nom de stratégie prérempli.

    1. Sous l’onglet **Informations de base**, sous **Étendue**, cliquez sur le bouton avec les points de suspension pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Dans l’onglet **Paramètres** :
       - Désactivez la case à cocher **Afficher uniquement les paramètres nécessitant une entrée**.
       - Si **Effet** figure dans des champs **Nom du paramètre**, laissez ceux-ci tels quels.
       - Choisissez votre espace de travail Azure Sentinel dans la liste déroulante **Espace de travail Log Analytics**.
       - Les champs de liste déroulante restants représentent les types de journaux de diagnostic disponibles. Conservez la valeur « True » pour tous les types de journaux que vous souhaitez ingérer.

    1. La stratégie sera appliquée aux ressources ajoutées à l’avenir. Pour appliquer la stratégie également à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

---

> [!NOTE]
>
> Avec ce type de connecteur de données, les indicateurs d’état de connectivité (une bande de couleur dans la galerie des connecteurs de données et des icônes de connexion à côté des noms de types de données) s’affichent comme *connectés* (en vert) uniquement si les données ont été ingérées au cours des 14 derniers jours. Après 14 jours passés sans ingestion de données, le connecteur sera déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

Vous pouvez rechercher et interroger les données pour chaque type de ressource à l’aide des noms de table qui s’affichent dans la section du connecteur de la ressource dans la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="log-analytics-agent-based-connections"></a>Connexions basées sur l’agent Log Analytics

### <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations de lecture et d’écriture sur l’espace de travail Log Analytics, ainsi que sur tout espace de travail contenant des machines dont vous souhaitez collecter les journaux.
- Vous devez être titulaire du rôle **Contributeur Log Analytics** sur la solution SecurityInsights (Azure Sentinel) de ces espaces de travail, en plus des rôles Azure Sentinel.

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre service (**DNS** ou **Pare-feu Windows**), puis choisissez **Ouvrir la page du connecteur**.

1. Installez et intégrez l’agent sur l’appareil qui génère les journaux.

    | Type de machine  | Instructions  |
    | --------- | --------- |
    | **Pour une machine virtuelle Azure Windows** | 1. Sous **Choisissez l’emplacement d’installation de l’agent**, développez **Installer l’agent sur une machine virtuelle Azure Windows**. <br><br>2. Sélectionnez le lien **Télécharger et installer l’agent pour les machines virtuelles Windows Azure >** . <br><br>3. Dans le panneau **Machines virtuelles**, sélectionnez une machine virtuelle sur laquelle installer l’agent, puis choisissez **Connecter**. Répétez cette étape pour chaque machine virtuelle à laquelle vous souhaitez vous connecter. |
    | **Pour toute autre machine Windows** | 1. Sous **Choisissez l’emplacement d’installation de l’agent**, développez **Installer l’agent sur un ordinateur non Azure Windows**. <br><br>2. Sélectionnez le lien **Télécharger et installer l’agent pour les machines Windows non-Azure >** .  <br><br>3. Dans le panneau **Agents de gestion**, sous l’onglet **Serveurs Windows**, sélectionnez le lien **Télécharger l’agent Windows** pour les systèmes 32 bits ou 64 bits, selon le cas.      |

1. Sélectionnez le bouton **Installer la solution** pour le DNS ou le Pare-feu Windows.

Vous pouvez rechercher et interroger les données pour le DNS et le Pare-feu Windows à l’aide des noms de table **DnsEvents**, **DnsInventory** et **WindowsFirewall**. Vous pouvez voir cela, ainsi que d’autres informations sur ces deux connecteurs de service, dans leurs sections respectives dans la page [Référence des connecteurs de données](data-connectors-reference.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter des services Azure, Microsoft et Windows, ainsi qu’Amazon Web Services, à Azure Sentinel. 
- Découvrez les [connecteurs de données Azure Sentinel](connect-data-sources.md) en général.
- [Recherchez votre connecteur de données Azure Sentinel](data-connectors-reference.md).
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
