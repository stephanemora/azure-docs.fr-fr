---
title: Résoudre les problèmes de votre appareil IoT Plug-and-Play
description: Guide des étapes de dépannage recommandées pour les partenaires qui certifient un appareil IoT Plug-and-Play.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e87051bb27e097f507435582cc919f05fd78aeb
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403056"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Résoudre les problèmes liés à votre projet de certification IoT Plug-and-Play

Pendant la phase Connexion et test de votre projet de certification IoT Plug-and-Play, vous pouvez rencontrer certains scénarios qui vous empêchent de réussir le test du service de certification Azure pour IoT (AICS).

## <a name="prerequisites"></a>Prérequis

- Vous devez être connecté et disposer d’un projet pour votre appareil sur le [portail Azure Certified Device](https://certify.azure.com). Pour plus d’informations, consultez ce [tutoriel](tutorial-01-creating-your-project.md).

## <a name="when-aics-tests-arent-passing"></a>Lorsque les tests AICS ne réussissent pas

Le test AICS peut ne pas réussir en raison de plusieurs causes. Procédez comme suit pour vérifier les problèmes courants et résoudre les problèmes liés à votre appareil.

1. Vérifiez que votre code d’appareil définit la charge utile de l’ID de modèle pendant la configuration de DPS. Il s’agit d’une condition requise pour qu’AICS valide votre appareil.
1. Vous pouvez afficher les journaux de télémétrie des séries de tests précédentes en appuyant sur le bouton `View Logs` pour identifier la cause de l’échec du test. La messagerie de test et les données brutes peuvent être consultées.  

    ![Examiner les données de test](./media/images/review-logs.png)

1. Dans certains cas où les journaux indiquent `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected`, essayez de redémarrer l’appareil et de relancer le processus de configuration de l’appareil.
1. Si les tests automatisés continuent d’échouer, vous pouvez `request a manual review` des résultats. Cette opération déclenche une demande de **validation manuelle** avec l’équipe Azure Certified Device.  

    ![Demander une révision manuelle](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Quand vous voyez « Réussi avec avertissements »

Lors de l’exécution des tests, si vous recevez un résultat `Passed with warnings`, cela signifie que certaines données de télémétrie n’ont pas été reçues pendant la période de test. Cela peut être dû à une dépendance des données de télémétrie sur des intervalles de temps plus longs ou des déclencheurs externes qui n’étaient pas disponibles. Vous pouvez procéder à l’envoi de votre appareil pour révision, au cours de laquelle l’équipe de révision déterminera si une **validation manuelle** est nécessaire à l’avenir.

## <a name="when-you-need-help-with-the-model-repository"></a>Si vous avez besoin d’aide avec le référentiel de modèles

Pour les problèmes IoT Plug-and-Play liés au référentiel de modèles, reportez-vous à [notre guide de documentation sur le référentiel de modèles d’appareil](../iot-develop/concepts-model-repository.md).

## <a name="next-steps"></a>Étapes suivantes

Nous espérons que ce guide vous aidera à poursuivre votre parcours de certification IoT Plug-and-Play ! Une fois que vous avez passé l’AICS, vous pouvez suivre nos didacticiels pour envoyer et publier votre appareil.

- [Tutoriel : Test de votre appareil](tutorial-03-testing-your-device.md)