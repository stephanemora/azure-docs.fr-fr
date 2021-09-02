---
title: Outil de diagnostic pour le runtime d’intégration auto-hébergé
description: Outil de diagnostic pour le runtime d’intégration auto-hébergé
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b8aa070759ea29c9c2853d0c8a6af184bc169569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532512"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>Outil de diagnostic pour le runtime d’intégration auto-hébergé
Le runtime d’intégration auto-hébergé représente l’infrastructure de calcul utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. L’installation d’un runtime d’intégration auto-hébergé nécessite une machine locale ou une machine virtuelle à l’intérieur d’un réseau privé. Parfois, il est difficile d’examiner les problèmes sur les ordinateurs locaux, tels que les problèmes liés au réseau, au pare-feu, à la dépendance ou au système d’exploitation. Cet article décrit un nouvel outil de diagnostic pour résoudre les problèmes dans les environnements locaux.

L’outil exécute une série de scénarios de test sur l’ordinateur du runtime d’intégration auto-hébergé. Chaque scénario a des cas de contrôle d’intégrité typiques pour les problèmes courants. Les clients peuvent déclencher la fonctionnalité « résoudre les problèmes » s’ils rencontrent des problèmes. L’outil collecte les informations sur l’environnement du client et exécute les cas de contrôle de l’intégrité. 

## <a name="get-started"></a>Bien démarrer 
Il existe deux façons d’exécuter l’outil de diagnostic pour détecter les problèmes éventuels :

- Lorsque vous installez un runtime d’intégration auto-hébergé sur un ordinateur local, vous pouvez accéder à la fonctionnalité de résolution des problèmes à partir du Configuration Manager. Si vous rencontrez un problème, sélectionnez **Résoudre les problèmes** pour exécuter l’outil de diagnostic. Vous pouvez également sélectionner cette même option dans l’onglet **Diagnostics** après l’installation.

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="Capture d’écran montrant comment résoudre les problèmes à l’aide du Configuration Manager d’exécution.":::
   
- Vous pouvez également démarrer l’outil de diagnostic à partir de la ligne de commande :

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>Résultat des diagnostics
Le résultat de l’exécution et les messages détaillés du journal sont générés sous forme de rapport HTML. Vous pouvez consulter l’erreur et obtenir les méthodes d’atténuation suggérées ou l’URL des documents publics à partir du rapport.

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="Capture d’écran montrant le processus de diagnostic.":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="Capture d’écran montrant le rapport du résultat des diagnostics.":::

## <a name="next-steps"></a>Étapes suivantes

- Étudiez les [concepts de runtime d’intégration dans Azure Data Factory](./concepts-integration-runtime.md).

- Apprenez à [créer un runtime d’intégration auto-hébergé sur le portail Azure](./create-self-hosted-integration-runtime.md).
