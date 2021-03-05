---
title: Présentation de la collecte de journaux proactive sur un appareil Azure Stack Edge Pro
description: Décrit comment la collecte proactive de journaux est effectuée sur un appareil Azure Stack Edge Pro et comment la désactiver.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: 064af116112f0b530ac0cc9b5755dcec2cf0bd07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722062"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Collecte de journaux proactive sur votre appareil Azure Stack Edge

La collection proactive de journaux rassemble les indicateurs d’intégrité du système sur votre appareil Azure Stack Edge pour vous aider à résoudre les problèmes sur tous les appareils. La collecte proactive des journaux est activée par défaut. Cet article décrit ce qui est enregistré, comment Microsoft gère les données et comment désactiver ou activer la collecte proactive de journaux. 

Les informations de cet article s’appliquent aux appareils Azure Stack Edge Pro GPU, Azure Stack Edge Pro R et Azure Stack Edge Mini R.

## <a name="about-proactive-log-collection"></a>À propos de la collecte de journaux proactive

Les équipes d’ingénierie et de support technique de Microsoft utilisent les journaux système de votre appareil Azure Stack Edge pour identifier et résoudre efficacement les problèmes susceptibles de survenir lors de l’utilisation de celui-ci. La collecte proactive de journaux est une méthode qui avertit Microsoft qu’un problème/événement a été détecté par l’appliance Azure Stack Edge du client. Consultez [Ies indicateurs de collecte des journaux proactifs](#proactive-log-collection-indicators) pour les événements suivis. Les journaux de support relatifs au problème sont chargés automatiquement sur un compte de stockage Azure managé et contrôlé par Microsoft. Le support et les ingénieurs de Microsoft examinent ces journaux de support afin de déterminer la meilleure solution pour résoudre le problème avec le client.

> [!NOTE]
> Ces journaux sont utilisés uniquement à des fins de débogage et fournissent un support aux clients en cas de problème.


## <a name="enabling-proactive-log-collection"></a>Activation de la collecte de journaux proactive

La collecte proactive des journaux est activée par défaut. Vous pouvez activer la collecte proactive de journaux lorsque vous tentez d’activer l’appareil via l’interface utilisateur locale. 

1. Dans l’interface utilisateur web locale de l’appareil, accédez à la page **Bien démarrer**.

2. Dans la vignette **Activation**, sélectionnez **Activer**. 

    ![Page « Détails du cloud » 1 de l’interface utilisateur web locale](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. Dans le volet **Activer** :

   1. Entrez la **clé d’activation** que vous avez obtenue à l’étape [Obtenir la clé d’activation pour Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Une fois activée, la collecte proactive de journaux est activée par défaut, ce qui permet à Microsoft de collecter des journaux en fonction de l’état d’intégrité de l’appareil. Ils sont ensuite chargés sur un compte de stockage Azure. 

      Vous pouvez désactiver la collecte proactive des journaux pour empêcher Microsoft de collecter des journaux.

   1. Si vous souhaitez désactiver la collecte proactive des journaux sur l’appareil, sélectionnez **Désactiver**.

   1. Sélectionnez **Activer**.

   ![Page « Détails du cloud » de l’interface utilisateur web locale 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>Indicateurs de collecte de journaux proactive

Une fois la collecte de journaux proactive activée, les journaux sont chargés automatiquement quand un des événements suivants est détecté sur l’appareil :  


|Alerte/Erreur/condition  |Description  |
|---------|---------|
|AcsUnhealthyCondition     |Les services cohérents Azure ne sont pas sains.         |
|IOTEdgeAgentNotRunningCondition      |L’agent IoT Edge n’est pas en cours d’exécution.         |
|UpdateInstallFailedEvent | Impossible d’installer la mise à jour.        |

 
Microsoft va continuer à ajouter de nouveaux événements à la liste précédente. Aucun consentement supplémentaire n’est nécessaire pour les nouveaux événements. Pour en savoir plus sur les indicateurs de collecte de journaux proactive les plus récents, consultez cette page.    
 

## <a name="other-log-collection-methods"></a>Autres méthodes de collecte de journaux

Outre la collecte des journaux proactive, qui collecte des journaux relatifs à un problème spécifique détecté, d’autres collectes de journaux peuvent apporter une compréhension plus approfondie de l’intégrité et du comportement du système. En général, ces autres journaux peuvent être exécutés lors d’une demande de support ou déclenchées par Microsoft en fonction des données de télémétrie que l’appareil fournit.

## <a name="handling-data"></a>Gestion des données

Si un client active la collecte des journaux proactive, il consent à ce que Microsoft collecte les journaux de l’appareil Azure Stack Edge de la manière décrite dans le présent document. Le client consent également expressément au chargement et à la rétention de ces journaux dans un compte de stockage Azure managé et contrôlé par Microsoft.

Microsoft utilise les données uniquement pour résoudre des problèmes liés à l’intégrité du système. Les données ne sont pas utilisées à des fins de marketing, publicitaires ou commerciales sans le consentement du client. 

Microsoft traite les données collectées conformément à ses pratiques de confidentialité standard. Si un client décide de révoquer son consentement à la collecte des journaux proactive, cela n’affecte les données collectées avec son consentement avant la révocation de celui-ci.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [Collecter un package de support](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).