---
title: Surveiller Key Vault avec Azure Monitor pour Key Vault (préversion) | Microsoft Docs
description: Cet article décrit la solution Azure Monitor pour coffres de clés.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: c669946ab76fcaeaaa6fd681f521408643c5a63c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531257"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Analyse de votre service de coffre de clés avec Azure Monitor pour Key Vault (préversion)
Azure Monitor pour Key Vault (préversion) offre une analyse complète de vos coffres de clés en fournissant une vue unifiée des requêtes, des performances, des échecs et de la latence de Key Vault.
Cet article vous aidera à comprendre comment intégrer et personnaliser l’expérience d’Azure Monitor pour Key Vault (préversion).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Présentation d’Azure Monitor pour Key Vault (préversion)

Avant de vous lancer dans cette expérience, vous devez comprendre comment cette fonctionnalité présente et affiche les informations.
-    **Perspective à grande échelle** présentant un instantané des performances en fonction des demandes, de la décomposition des échecs et d’une vue d’ensemble des opérations et de la latence.
-   **Analyse approfondie** d’un coffre de clés particulier pour effectuer une analyse détaillée.
-    **Personnalisable** en vous permettant de modifier les métriques que vous souhaitez voir, modifier ou définir des seuils qui s’alignent sur vos limites et enregistrer votre propre classeur. Les graphiques du classeur peuvent être épinglés aux tableaux de bord Azure.

Azure Monitor pour Key Vault combine les journaux d’activité et les métriques pour fournir une solution de supervision globale. Tous les utilisateurs peuvent accéder aux données de surveillance basées sur les métriques. Toutefois, l’inclusion de visualisations basées sur les journaux peut obliger les utilisateurs à [activer la journalisation de leur coffre de clés Azure](../../key-vault/general/logging.md).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Configuration de vos coffres de clés pour l’analyse

> [!NOTE]
> L’activation des journaux est un service payant qui fournit des capacités d’analyse supplémentaires.

1. L’onglet Opérations et latence vous aide à déterminer combien et quels coffres de clés sont activés. Pour commencer la collecte, sélectionnez le bouton **Activer**, qui vous redirige vers un classeur distinct qui répertorie les coffres de clés qui nécessitent l’activation des journaux de diagnostic.

    ![Capture d’écran de l’onglet Opérations et latence avec le bouton d’activation bleu affiché](./media/key-vaults-insights-overview/enable-logging.png)

2. Pour activer les journaux de diagnostic, cliquez sur le lien **Activer** sous la colonne des actions, puis créez un nouveau paramètre de diagnostic qui envoie les journaux à un espace de travail Log Analytics. Il est recommandé d’envoyer tous les journaux vers le même espace de travail.

3. Une fois les paramètres de diagnostic enregistrés, vous serez en mesure de consulter tous les graphiques et visualisations basés sur les journaux sous la rubrique Insights Key Vault. Veuillez noter que cela peut prendre de quelques minutes à quelques heures pour commencer à alimenter les journaux.

4. Pour obtenir une aide supplémentaire sur l’activation des journaux de diagnostic pour votre service Key Vault, consultez le [guide complet](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Voir à partir d’Azure Monitor

À partir d’Azure Monitor, vous pouvez afficher les détails des requêtes, de la latence et des échecs de plusieurs coffres de clés de votre abonnement et identifier les problèmes de performances et les scénarios de limitation.

Pour consulter l’utilisation et les opérations de vos coffres de clés dans tous vos abonnements, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Surveillance** dans le volet gauche du portail Azure, puis dans la section Insights, sélectionnez **Coffres de clés (préversion)** .

![Capture d’écran de l’expérience de vue d’ensemble avec plusieurs graphiques](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Classeur de présentation

Dans le classeur Vue d’ensemble de l’abonnement sélectionné, le tableau affiche les métriques interactives des coffres de clés regroupés dans l’abonnement. Vous pouvez filtrer les résultats en fonction des options que vous sélectionnez dans les listes déroulantes suivantes :

* Abonnements : seuls les abonnements qui ont des coffres de clés sont répertoriés.

* Coffres de clés : par défaut, seuls cinq coffres de clés maximum sont présélectionnés. Si vous sélectionnez tous les coffres de clés ou plusieurs d’entre eux dans le sélecteur d’étendue, jusqu’à 200 coffres de clés seront retournés. Par exemple, si vous avez un total de 573 coffres de clés sur 3 abonnements que vous avez sélectionnés, seuls 200 coffres sont affichés.

* Intervalle de temps : par défaut, affiche les 24 dernières heures d’informations en fonction des sélections correspondantes effectuées.

La vignette de compteur, sous la liste déroulante, cumule le nombre total de coffres de clés dans les abonnements sélectionnés et indique combien sont sélectionnés. Il existe des cartes thermiques conditionnelles à code couleur pour les colonnes du classeur qui rapportent les métriques relatives aux requêtes, aux échecs et à la latence. La couleur la plus profonde a la valeur la plus élevée et la couleur la plus claire désigne les valeurs les plus basses.

## <a name="failures-workbook"></a>Classeur Échecs

Sélectionnez **Échecs** en haut de la page et l’onglet Échecs s’ouvre. Il vous montre les correspondances d’API, la fréquence dans le temps, ainsi que la quantité de certains codes de réponse.

![Capture d’écran du classeur Échecs](./media/key-vaults-insights-overview/failures.png)

Il existe des codes de couleur conditionnels ou des cartes thermiques pour les colonnes du classeur qui rapportent les métriques relatives aux correspondances d’API avec une valeur bleue. La couleur la plus profonde a la valeur la plus élevée et la couleur la plus claire désigne les valeurs les plus basses.

Le classeur affiche les réussites (codes d’état 2xx), les erreurs d’authentification (codes d’état 401/403), la limitation (codes d’état 429) et d’autres échecs (codes d’état 4xx).

Pour mieux comprendre ce que chaque code d’état représente, nous vous recommandons de lire la documentation relative aux[codes d’état et de réponse d’Azure Key Vault](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="operations--latency-workbook"></a>Classeur Opérations et latence

Sélectionnez **Opérations et latence** en haut de la page, et l’onglet **Opérations et latence** s’ouvre. Cet onglet vous permet d’intégrer vos coffres de clés à des fins d’analyse. Pour obtenir des instructions plus détaillées, consultez la section [Configuration de vos coffres de clés pour l’analyse](#configuring-your-key-vaults-for-monitoring).

Vous pouvez voir combien de vos coffres de clés sont activés pour la journalisation. Si au moins un coffre a été configuré correctement, vous pouvez voir des tableaux qui affichent les opérations et les codes d’état pour chacun de vos coffres de clés. Vous pouvez cliquer dans la section des détails d’une ligne pour obtenir des informations supplémentaires sur l’opération.

![Capture d’écran des graphiques Opérations et latence](./media/key-vaults-insights-overview/logs.png)

Si vous ne voyez aucune donnée pour cette section, reportez-vous à la section située pus haut sur l’activation des journaux pour Azure Key Vault ou consultez la section de résolution des problèmes ci-dessous.

## <a name="view-from-a-key-vault-resource"></a>Vue à partir d’une ressource Key Vault

Pour accéder à Azure Monitor pour Key Vault directement à partir d’un coffre de clés :

1. Dans le portail Azure, sélectionnez Coffres de clés.

2. Dans la liste, choisissez un coffre de clés. Dans la section Supervision, choisissez Insights (préversion).

Vous pouvez également accéder à ces vues en sélectionnant le nom de ressource d’un coffre de clés à partir du classeur au niveau d’Azure Monitor.

![Capture d’écran de la vue à partir d’une ressource de coffre de clés](./media/key-vaults-insights-overview/key-vault-resource-view.png)

Dans le classeur **Vue d’ensemble** du coffre de clés, plusieurs métriques de performances s’affichent pour vous aider à évaluer rapidement :

- Les graphiques interactifs de performances présentant les détails les plus importants relatifs aux transactions, à la latence et à la disponibilité des coffres de clés.

- Les vignettes Métriques et État mettent en évidence la disponibilité du service, le nombre total de transactions pour la ressource de coffre de clés et la latence globale.

La sélection de l’un des autres onglets pour **Échecs** ou **Opérations** ouvre les classeurs respectifs.

![Capture d’écran de la vue Échecs](./media/key-vaults-insights-overview/resource-failures.png)

Le classeur des échecs décompose les résultats de toutes les requêtes de coffre de clés dans le laps de temps sélectionné et fournit la catégorisation des réussites (2xx), des erreurs d’authentification (401/403), de la limitation (429) et d’autres échecs.

![Capture d’écran de la vue Opérations](./media/key-vaults-insights-overview/operations.png)

Le classeur Operations permet aux utilisateurs d’approfondir les détails de toutes les transactions, lesquelles peuvent être filtrées par état du résultat à l’aide des vignettes en haut de l’écran.

![Capture d’écran de la vue Opérations](./media/key-vaults-insights-overview/info.png)

Les utilisateurs peuvent également définir des vues basées sur des types spécifiques de transactions dans le tableau du haut, ce qui met à jour dynamiquement le tableau du bas, où les utilisateurs peuvent voir les détails complets des opérations dans un volet contextuel.

>[!NOTE]
> Notez que les paramètres de diagnostic des utilisateurs doivent être activés pour afficher ce classeur. Pour en savoir plus sur l’activation des paramètres de diagnostic, lisez la section relative à la [journalisation d’Azure Key Vault](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Épingler et exporter

Vous pouvez épingler une des sections métriques à un tableau de bord Azure en sélectionnant l’icône de punaise en haut à droite de la section.

Les classeurs Vue d’ensemble et Échecs des coffres de clés et des abonnements multiples prennent en charge l’exportation des résultats au format Excel en sélectionnant l’icône de téléchargement à gauche de l’icône de punaise.

![Capture d’écran de l’icône de punaise sélectionnée](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Personnaliser Azure Monitor pour Key Vault (préversion)

Cette section présente les scénarios courants de modification du classeur pour le personnaliser en fonction de vos besoins d’analyse de données :
*  Régler l’étendue du classeur pour qu’il sélectionne toujours un abonnement ou des coffres de clés particuliers
* Modifier les métriques dans la grille
* Modifier le seuil des requêtes
* Modifier le rendu des couleurs

Pour commencer les personnalisations, vous pouvez activez le mode d’édition en sélectionnant le bouton **Personnaliser** dans la barre d’outils supérieure.

![Capture d’écran du bouton Personnaliser](./media/key-vaults-insights-overview/customize.png)

Les personnalisations sont enregistrées dans un classeur personnalisé pour éviter de remplacer la configuration par défaut dans notre classeur publié. Les classeurs sont enregistrés au sein d’un groupe de ressources, soit dans la section Mes rapports qui est privée, soit dans la section Rapports partagés accessible à tous les utilisateurs ayant accès au groupe de ressources. Une fois que vous avez enregistré le classeur personnalisé, vous devez accéder à la galerie de classeurs pour le lancer.

![Capture d’écran de la galerie de classeurs](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Spécification d’un abonnement ou d’un coffre de clés

Vous pouvez configurer les classeurs Vue d’ensemble et Échecs des coffres de clés et des abonnements multiples pour les étendre à un ou plusieurs abonnements ou coffres de clés à chaque exécution. Pour cela, procédez comme suit :

1. Sélectionnez **Surveillance** à partir du portail, puis sélectionnez **Coffres de clés (préversion)** dans le volet gauche.
2. Dans le classeur **Vue d’ensemble**, dans la barre de commandes, sélectionnez **Modifier**.
3. Dans la liste déroulante **Abonnements**, sélectionnez un ou plusieurs abonnements à utiliser par défaut. N’oubliez pas que le classeur prend en charge la sélection de jusqu’à 10 abonnements au total.
4. Dans la liste déroulante **Coffres de clés**, sélectionnez un ou plusieurs comptes à utiliser par défaut. N’oubliez pas que le classeur prend en charge la sélection d’un total de jusqu’à 200 comptes de stockage.
5. Sélectionnez **Enregistrer sous** dans la barre de commandes pour enregistrer une copie du classeur avec vos personnalisations, puis cliquez sur **Modifications terminées** pour revenir au mode lecture.

## <a name="troubleshooting"></a>Dépannage

Pour obtenir des conseils généraux sur la résolution des problèmes, consultez l’[article de résolution des problèmes](troubleshoot-workbooks.md) pour les insights basés sur des classeurs.

Cette section est destinée à vous aider à diagnostiquer et résoudre certains des problèmes communs que vous êtes susceptible de rencontrer lors de l’utilisation d’Azure Monitor pour Key Vault (préversion). La liste ci-dessous permet d’identifier les informations pertinentes pour un problème spécifique.

### <a name="resolving-performance-issues-or-failures"></a>Résolution des problèmes de performances ou des échecs

Pour résoudre les problèmes liés aux coffres de clés que vous identifiez auprès d’Azure Monitor pour Key Vault (préversion), consultez le [documentation d’Azure Key Vault](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Pourquoi je ne vois pas plus de 200 coffres de clés ?

Un maximum de 200 coffres de clés peuvent être sélectionnés et affichés. Quel que soit le nombre d’abonnements sélectionnés, le nombre de coffres de clés sélectionnés est limité à 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Pourquoi je ne vois pas tous mes abonnements dans le sélecteur d’abonnement ?

Nous affichons uniquement les abonnements qui contiennent des coffres de clés, choisis dans le filtre d’abonnement sélectionné, et qui sont sélectionnés dans la rubrique « Répertoire + abonnement » dans l’en-tête du portail Azure.

![Capture d’écran du filtre d’abonnement](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>J’obtiens un message d’erreur indiquant que « la requête dépasse le nombre maximal d’espaces de travail/de régions autorisés », que faire maintenant ?

Actuellement, il existe une limite de 25 régions et de 200 espaces de travail. Pour afficher vos données, vous devrez réduire le nombre d’abonnements ou de groupes de ressources.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Je souhaite apporter des modifications ou ajouter des visualisations supplémentaires aux insights Key Vault, comment faire ?

Pour apporter des modifications, sélectionnez « Mode d’édition » pour modifier le classeur. Vous pouvez ensuite enregistrer votre travail sous un nouveau classeur lié à un abonnement et à un groupe de ressources désignés.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Quel est le fragment de temps après avoir épinglé une partie des classeurs ?

Nous utilisons le fragment de temps « Auto » ; par conséquent, il dépend de l’intervalle de temps sélectionné.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Quel est l’intervalle de temps quand une partie du classeur est épinglée ?

L’intervalle de temps dépendra des paramètres du tableau de bord.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Pourquoi je ne vois pas de données pour mon coffre de clés dans la section Opérations et latence ?

Pour afficher vos données basées sur les journaux, vous devez activer les journaux pour chacun des coffres de clés que vous souhaitez analyser. Vous pouvez effectuer cette opération dans les paramètres de diagnostic pour chaque coffre de clés. Vous devrez envoyer vos données à un espace de travail Log Analytics désigné.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>J’ai déjà activé les journaux pour mon coffre de clés, pourquoi je ne vois toujours pas mes données sous Opérations et latence ?

Actuellement, les journaux de diagnostic ne fonctionnent pas de manière rétroactive. Par conséquent, les données ne commenceront à apparaître que lorsque des actions seront effectuées sur vos coffres de clés. Par conséquent, cette opération peut prendre un certain temps, allant de quelques heures à une journée, selon le degré d’activité de votre coffre de clés.

En outre, si vous avez sélectionné un grand nombre de coffres de clés et d’abonnements, vous ne pourrez peut-être pas consulter vos données en raison des limitations de requête. Pour voir vos données, vous devrez peut-être réduire le nombre d’abonnements ou de coffres sélectionnés. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Que se passe-t-il si je souhaite voir d’autres données ou créer mes propres visualisations ? Comment puis-je apporter des modifications aux insights Key Vault ?

Vous pouvez modifier le classeur existant, en utilisant le mode d’édition, puis enregistrer votre travail sous un nouveau classeur qui contiendra toutes vos nouvelles modifications.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les scénarios que les classeurs sont conçus pour prendre en charge, comment créer et personnaliser des rapports existants, et bien plus encore en consultant la rubrique [Créer des rapports interactifs avec les classeurs Azure Monitor](../platform/workbooks-overview.md).
