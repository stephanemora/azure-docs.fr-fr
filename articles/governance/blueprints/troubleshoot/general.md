---
title: Résoudre les erreurs à l’aide de blueprints Azure
description: Découvrez comment résoudre les problèmes de création et d’affectation des blueprints.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982288"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Résoudre les erreurs à l’aide de blueprints Azure

Vous pouvez rencontrer des erreurs lors de la création ou de l’affectation des blueprints. Cet article décrit différentes erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="finding-error-details"></a>Recherche des détails de l’erreur

De nombreuses erreurs sont dues à l’affectation d’un blueprint à une étendue. Quand une affectation échoue, le blueprint fournit des détails sur le déploiement ayant échoué. Ces informations précisent le problème afin que vous puissiez le résoudre et que les déploiements suivants réussissent.

1. Lancez le service Azure Blueprint dans le portail Azure. Pour cela, cliquez sur **Tous les services**, puis recherchez et sélectionnez **Stratégie** dans le volet gauche. Dans la page **Stratégie**, cliquez sur **Blueprints**.

1. Sélectionnez **Blueprints affectés** dans la page de gauche et utilisez la zone de recherche pour filtrer les affectations de blueprints afin d’identifier celle qui a échoué. Vous pouvez également trier la table des affectations d’après la colonne **État de l’approvisionnement** pour regrouper toutes les affectations ayant échoué.

1. Cliquez sur le blueprint ayant l’état _Échec_ ou cliquez avec le bouton droit et sélectionnez **Voir les détails de l’affectation**.

1. En haut de la page d’affectation de blueprint se trouve une bannière rouge signalant que l’affectation a échoué. Cliquez n’importe où sur la bannière pour obtenir plus de détails.

Il est courant que l’erreur soit due à un artefact inclus dans le blueprint, et non au blueprint lui-même. Par exemple, si le blueprint contient un artefact pour créer un coffre de clés, mais que la création de coffre de clés est bloquée par la stratégie Azure, l’affectation entière échoue.

## <a name="general-errors"></a>Erreurs générales

### <a name="policy-violation"></a>Scénario : Violation de stratégie

#### <a name="issue"></a>Problème

Le déploiement du modèle a échoué à cause de la violation de stratégie.

#### <a name="cause"></a>Cause

Une stratégie peut entrer en conflit avec le déploiement pour plusieurs raisons :

- La ressource en cours de création est limitée par la stratégie (généralement des restrictions de référence SKU ou d’emplacement)
- Le déploiement définit des champs qui sont configurés par la stratégie (courant avec les étiquettes)

#### <a name="resolution"></a>Résolution

Ajustez le blueprint pour qu’il ne soit pas en conflit avec les stratégies listées dans les informations sur l’erreur. Si ce n’est pas possible, une autre option consiste à changer l’étendue de l’affectation de stratégie de sorte que le blueprint ne soit plus en conflit avec la stratégie.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

- Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
- Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
- Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.