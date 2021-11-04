---
title: Configurer des règles (Fusion) de détection avancée des attaques en plusieurs étapes dans Azure Sentinel
description: Créez et configurez des règles de détection des attaques basées sur la technologie Fusion dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: bf84d969b8ca60c56ec172dbd9befeaaacd8a5dc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097858"
---
# <a name="configure-multistage-attack-detection-fusion-rules-in-azure-sentinel"></a>Configurer des règles (Fusion) de détection avancée des attaques en plusieurs étapes dans Azure Sentinel

> [!IMPORTANT]
> La nouvelle version de la règle d’analyse Fusion est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel utilise Fusion, un moteur de corrélation basé sur des algorithmes évolutifs d’apprentissage automatique pour détecter automatiquement des attaques en plusieurs étapes en identifiant des combinaisons de comportements anormaux et d’activités suspectes observés à divers stades de la chaîne de destruction. Sur la base de ces découvertes, Azure Sentinel génère des incidents qui seraient autrement difficiles à intercepter. Ces incidents comprennent au moins deux alertes ou activités. Le système est conçu de façon à ce que ces incidents soient peu volumineux, soient détectés avec une haute fidélité, et présentent un niveau de gravité élevé.

Personnalisée pour votre environnement, cette technologie de détection, en plus de réduire la fréquence de [faux positifs](false-positives.md), peut détecter des attaques même si les informations sont limitées ou si certaines informations sont manquantes.

## <a name="configure-fusion-rules"></a>Configurer des règles Fusion

Cette détection est activée par défaut dans Azure Sentinel. Pour vérifier ou modifier son état, suivez les instructions ci-dessous :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et entrez **Azure Sentinel**.

1. Dans le menu de navigation d'Azure Sentinel, sélectionnez **Analytique**.

1. Sélectionnez l’onglet **Règles actives**, puis, dans la colonne **Nom**, cherchez **Détection avancée des attaques multiphases** en filtrant la liste pour le type de règle **Fusion**. Consultez la colonne **État** pour vérifier si cette détection est activée ou désactivée.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="Capture d’écran de la règle d’analyse Fusion." lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. Pour changer l’état, sélectionnez cette entrée, puis, dans le volet de visualisation **Détection avancée des attaques multiphases**, sélectionnez **Modifier**.

1. Sous l’onglet **Général** de l’**Assistant Règle d’analyse**, notez l’état (Activé/Désactivé) ou modifiez-le si vous le souhaitez.

    Si vous avez modifié l’état et n’avez pas d’autres modifications à apporter, sélectionnez l’onglet **Vérifier et mettre à jour**, puis choisissez **Enregistrer**. 
    Pour configurer la règle de détection Fusion, cliquez sur **Suivant : Configurer Fusion**.

    :::image type="content" source="media/configure-fusion-rules/configure-fusion-rule.png" alt-text="Capture d’écran de la configuration de règle Fusion." lightbox="media/configure-fusion-rules/configure-fusion-rule.png":::

1. **Configurez des signaux source pour la détection Fusion** : pour un résultat optimal, nous vous recommandons d’inclure tous les signaux sources répertoriés, avec tous les niveaux de gravité. Par défaut, ils sont déjà inclus, mais vous avez la possibilité d’apporter des modifications en procédant comme suit :

    > [!NOTE]
    > Si vous excluez un signal source ou un niveau de gravité d’alerte, les détections Fusion qui reposent sur les signaux de cette source ou sur les alertes correspondant à ce niveau de gravité ne seront pas déclenchées. 
    
    - **Excluez des signaux des détections Fusion**, dont des anomalies, des alertes provenant de divers fournisseurs et des journaux bruts.
     
        *Cas d’usage :* si vous testez une source de signal spécifique connue pour produire des alertes bruyantes, vous pouvez désactiver temporairement les signaux de cette source de signal pour les détections Fusion.

    - **Configurer une gravité d’alerte pour chaque fournisseur** : en vertu de sa conception, le modèle ML Fusion met en corrélation des signaux de faible fidélité dans un seul incident de gravité élevée basé sur des signaux anormaux dans la chaîne de destruction de plusieurs sources de données. Les alertes incluses dans Fusion sont généralement de moindre gravité (moyenne, faible, informative), mais des alertes de gravité élevée sont parfois incluses.
     
        *Cas d’usage :* si vous avez un processus distinct pour le triage et l’examen des alertes de gravité élevée et préférez ne pas inclure ces alertes dans Fusion, vous pouvez configurer les signaux sources pour exclure les alertes de gravité élevée des détections Fusion. 



    - **Excluez des modèles de détection spécifiques de la détection Fusion**. Certaines détections Fusion ne sont pas applicables à votre environnement, ou peuvent occasionner la génération de faux positifs. Si vous souhaitez exclure un modèle de détection Fusion spécifique, suivez les instructions ci-dessous :

        1. Recherchez et ouvrez un incident Fusion du type que vous souhaitez exclure.

        1. Dans la section **Description**, sélectionnez **Afficher plus**.

        1. Sous **Exclure ce modèle de détection spécifique**, sélectionnez le **lien d’exclusion** pour être redirigé vers l’onglet **Configurer Fusion** dans l’Assistant Règle d’analyse.

            :::image type="content" source="media/configure-fusion-rules/exclude-fusion-incident.png" alt-text="Capture d’écran de l’incident Fusion. Sélectionnez le lien d’exclusion.":::

        Sous l’onglet **Configurer Fusion**, vous verrez le modèle de détection (une combinaison d’alertes et d’anomalies dans un incident Fusion) ajouté à la liste d’exclusion, ainsi que l’heure de l’ajout du modèle de détection.

        Vous pouvez supprimer un modèle de détection exclu à tout moment en sélectionnant l’icône de corbeille sur ce modèle de détection.

        :::image type="content" source="media/configure-fusion-rules/exclusion-patterns-list.png" alt-text="Capture d’écran de la liste des modèles de détection exclus.":::

        Les incidents qui correspondent aux modèles de détection exclus sont toujours déclenchés, mais ils **ne figurent pas dans votre file d’attente d’incidents actifs**. Ils seront automatiquement renseignés avec les valeurs suivantes :

        - **État** : « Closed »
        
        - **Classification de fermeture** : « Undetermined »

        - **Commentaire** : « Auto closed, excluded Fusion detection pattern »

        - **Étiquette** : « ExcludedFusionDetectionPattern ». Vous pouvez effectuer une requête sur cette étiquette pour afficher tous les incidents correspondant à ce modèle de détection.

            :::image type="content" source="media/configure-fusion-rules/auto-closed-incident.png" alt-text="Capture d’écran de l’incident Fusion exclu fermé automatiquement.":::



> [!NOTE]
> Azure Sentinel utilise actuellement 30 jours de données historiques pour former les systèmes de Machine Learning. Ces données sont toujours chiffrées à l’aide des clés de Microsoft à mesure qu’elles passent par le pipeline de Machine Learning. Cependant, les données de formation ne sont pas chiffrées à l’aide de [clés gérées par le client (CMK)](customer-managed-keys.md) si vous avez activé CMK dans votre espace de travail Azure Sentinel. Pour désactiver Fusion, accédez à **Azure Sentinel** \> **Configuration** \> **Analyse \> Règles actives**, faites un clic droit sur la règle **Détection des attaques multiphases avancées** et sélectionnez **Désactiver**.

## <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Configurer des règles d’analyse planifiée pour les détections Fusion

> [!IMPORTANT]
>
> - La détection basée sur Fusion à l’aide d’alertes de règles analytiques est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

**Fusion** peut détecter des attaques en plusieurs étapes, ainsi que des menaces émergentes, basées sur un scénario en utilisant des alertes générées par des [règles d’analyse planifiée](detect-threats-custom.md). Nous vous recommandons de suivre les étapes ci-dessous pour configurer et activer ces règles afin de tirer le meilleur parti des fonctionnalités de fusion d’Azure Sentinel.

1. Fusion pour les menaces émergentes peut utiliser des alertes générées par toute règle d’analyse planifiée, tant [intégrée](detect-threats-built-in.md#scheduled) que [créée par vos analystes de sécurité](detect-threats-custom.md), contenant des informations sur la chaîne de destruction (tactique) et le mappage d’entité. Pour vous assurer que la sortie d’une règle d’analyse peut être utilisée par Fusion pour détecter des menaces émergentes :

    - Vérifiez le **mappage d’entités** pour ces règles planifiées. Utilisez la [section de configuration du mappage d'entités](map-data-fields-to-entities.md) pour mapper les paramètres de vos résultats de requête avec des entités reconnues par Azure Sentinel. Étant donné que Fusion met en corrélation des alertes basées sur des entités (par exemple, un *compte d’utilisateur* ou une *adresse IP*), ses algorithmes d’apprentissage automatique ne peuvent pas effectuer de mise en correspondance d’alerte sans les informations d’entité.

    - Passez en revue les **tactiques** dans les détails de votre règle analytique. L’algorithme d’apprentissage automatique Fusion utilise les informations de tactique [MITRE ATT&CK](https://attack.mitre.org/) pour détecter les attaques en plusieurs étapes, et les tactiques avec lesquelles vous étiquetez les règles d’analyse sont affichées dans les incidents obtenus. Les calculs Fusion peuvent être affectés si les alertes entrantes ne contiennent pas d’informations de tactique.

1. Fusion peut également détecter les menaces basées sur un scénario en utilisant des règles basées sur les **modèles de règles d’analyse planifiée** suivants, qui se trouvent sous l’onglet **Modèles de règles** dans le panneau **Analyse**. Pour activer ces détections, sélectionnez le nom de la règle dans la galerie de modèles, puis cliquez sur **créer une règle** dans le volet d’informations.

    - [Cisco - blocage du pare-feu mais connexion réussie à Azure AD](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml)
    - [Fortinet - Modèle de balise détecté](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml)
    - [Une adresse IP avec plusieurs échecs de connexion Azure AD a réussi à se connecter à Palo Alto VPN](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml)
    - [Plusieurs réinitialisations de mot de passe par l’utilisateur](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml)
    - [Consentement d’application rare](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml)
    - [SharePointFileOperation via des adresses IP auparavant invisibles](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml)
    - [Déploiement de ressources suspectes](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml)

    > [!NOTE]
    > Pour l’ensemble des règles analytiques planifiées utilisées par Fusion, l’algorithme ML effectue une correspondance approximative pour les requêtes KQL fournies dans les modèles. Le changement de nom des modèles n’aura aucun impact sur les détections Fusion.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [Détections Fusion dans Azure Sentinel](fusion.md).

Apprenez-en davantage sur les nombreuses [Détections Fusion basées sur un scénario](fusion-scenario-reference.md).

Maintenant que vous en savez plus sur la détection avancée des attaques multiphases, il peut vous être utile de suivre le guide de démarrage suivant pour savoir comment bénéficier d’une visibilité sur vos données et sur les menaces potentielles : [Bien démarrer avec Azure Sentinel](get-visibility.md).

Si vous êtes prêt à examiner les incidents créés pour vous, consultez le tutoriel suivant : [Examiner les incidents avec Azure Sentinel](investigate-cases.md).
