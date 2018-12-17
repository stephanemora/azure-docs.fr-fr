---
title: Diagnostiquer et résoudre les problèmes de déconnexion avec Azure IoT Hub
description: Apprenez à diagnostiquer et résoudre les erreurs courantes en matière de connectivité des appareils pour Azure IoT Hub.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: b05a8bfd46800c5b0b0126adcf2acb4852bb6683
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339754"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Détecter et résoudre les problèmes de déconnexion avec Azure IoT Hub

Les problèmes de connectivité des appareils IoT sont parfois difficiles à résoudre, car il existe de nombreux points de défaillance possibles. La logique d’application côté appareil, les réseaux physiques, les protocoles, le matériel et Azure IoT Hub sont tous susceptibles d’occasionner des problèmes. Cet article fournit des suggestions sur la manière de détecter et résoudre les problèmes de connectivité d’appareil côté cloud (par opposition au côté appareil).

## <a name="get-alerts-and-error-logs"></a>Obtenir des alertes et des journaux d’erreurs

Utilisez Azure Monitor pour recevoir des alertes et créer des journaux en cas de connexions d’appareil défaillantes.

### <a name="turn-on-diagnostic-logs"></a>Activer les journaux de diagnostic

Pour enregistrer les événements et les erreurs de connexion d’appareil, activez les diagnostics pour IoT Hub.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre hub IoT.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Activer les diagnostics**.
1. Activez la collecte des journaux **Connexions**.
1. Pour faciliter l’analyse, activez l’option **Envoyer à Log Analytics** ([voir les tarifs](https://azure.microsoft.com/pricing/details/log-analytics/)). Consultez l’exemple sous [Résoudre les erreurs de connectivité](#Resolve-connectivity-errors).

   ![Paramètres recommandés][2]

Pour plus d’informations, consultez l’article [Surveiller l’intégrité d’Azure IoT Hub et diagnostiquer rapidement les problèmes](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Configurer des alertes pour la métrique du nombre d’_appareils connectés_

Pour obtenir des alertes quand des appareils se déconnectent, configurez les alertes sur la métrique **Appareils connectés**.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre hub IoT.
1. Sélectionnez **Alertes (classiques)**.
1. Sélectionnez **Ajouter une alerte métrique (classique)**.
1. Renseignez le formulaire, puis sélectionnez **OK**.

   ![Alerte de métrique recommandée][3]

Pour plus d’informations, consultez l’article [Que sont les alertes classiques dans Microsoft Azure ?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Résoudre les erreurs de connectivité

Lorsque vous activez les alertes et les journaux de diagnostic relatifs aux appareils connectés, vous recevez des alertes quand des erreurs se produisent. Cette section décrit comment résoudre les problèmes courants lorsque vous recevez une alerte. Pour exécuter la procédure ci-après, vous devez avoir configuré Azure Log Analytics pour vos journaux de diagnostic.

1. Accédez à votre espace de travail pour **Log Analytics** dans le portail Azure.
1. Sélectionnez **Recherche de journaux**.
1. Pour isoler les journaux d’erreurs de connectivité pour IoT Hub, entrez la requête ci-après, puis sélectionnez **Exécuter** :

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Si des résultats s’affichent, recherchez les éléments `OperationName`, `ResultType` (code d’erreur) et `ResultDescription` (message d’erreur) pour obtenir plus de détails sur l’erreur.

   ![Exemple de journal des erreurs][4]

1. Utilisez ce tableau pour comprendre et résoudre les erreurs courantes.

    | Error | Cause racine | Résolution : |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | La connexion a été fermée par l’appareil pour une raison inconnue d’IoT Hub. Cette erreur est généralement causée par une expiration du délai de connexion MQTT/AMQP ou par une perte de connectivité Internet. | Assurez-vous que l’appareil peut se connecter à IoT Hub en [testant la connexion](tutorial-connectivity.md). Si la connexion est correcte, mais que l’appareil se déconnecte par intermittence, veillez à implémenter la logique de persistance de connexion d’appareil appropriée pour le protocole que vous avez choisi (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub n’est pas parvenu à authentifier la connexion. | Assurez-vous que le jeton de signature d’accès partagé ou un autre jeton de sécurité que vous utilisez ne sont pas arrivés à expiration. Les [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md) génèrent automatiquement des jetons sans nécessiter de configuration spécifique. |
    | 409002 LinkCreationConflict | Un appareil a plusieurs connexions. Lorsqu’une nouvelle demande de connexion émane d’un appareil, IoT Hub ferme la connexion précédente en générant cette erreur. | Dans la plupart des cas, un appareil détecte une déconnexion et tente de rétablir la connexion, mais IoT Hub considère toujours l’appareil comme étant connecté. IoT Hub ferme la connexion précédente et journalise cette erreur. Cette erreur étant généralement l’effet secondaire d’un autre problème temporaire, recherchez d’autres erreurs dans les journaux pour résoudre le problème. Sinon, veillez à émettre une nouvelle demande de connexion uniquement en cas d’abandon de la connexion précédente. |
    | 500001 ServerError | IoT Hub a rencontré un problème côté serveur. Ce problème est très probablement temporaire. Bien que l’équipe IoT Hub s’efforce continuellement de respecter les termes du [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/iot-hub/), de petits sous-ensembles de nœuds IoT Hub rencontrent parfois des erreurs temporaires. Lorsque votre appareil tente de se connecter à un nœud présentant un problème, vous recevez cette erreur. | Pour résoudre l’erreur temporaire, émettez une nouvelle tentative à partir de l’appareil. Pour [gérer automatiquement les nouvelles tentatives](iot-hub-reliability-features-in-sdks.md#connection-and-retry), vérifiez que vous utilisez la dernière version des [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md).<br><br>Pour connaître les meilleures pratiques en matière de gestion des erreurs temporaires et de nouvelles tentatives, consultez l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).  <br><br>Si le problème persiste après plusieurs tentatives, consultez le service [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) et la page [Historique des états d’Azure](https://azure.microsoft.com/status/history/) pour déterminer si IoT Hub présente un problème connu. S’il n’existe aucun problème connu et que le problème persiste, [contactez le support](https://azure.microsoft.com/support/options/) pour un examen approfondi. |
    | 500008 GenericTimeout | IoT Hub n’est pas parvenu à effectuer la demande de connexion avant l’expiration du délai de connexion. Comme dans le cas de l’erreur 500001 ServerError, il s’agit probablement d’un problème temporaire. | Exécutez la procédure de résolution de l’erreur 500001 ServerError pour déterminer la cause racine et corriger ce problème.|

## <a name="other-steps-to-try"></a>Autres mesures de résolution possibles

Si les mesures ci-dessus n’ont pas résolu le problème, vous pouvez essayer ce qui suit :

* Si vous avez accès aux appareils problématiques, que ce soit physiquement ou à distance (comme SSH), consultez le [guide de résolution des problèmes côté appareil](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) pour poursuivre le dépannage.
* Vérifiez que vos appareils présentent l’état **Activé** en accédant au portail Azure > votre hub IoT > Appareils IoT.
* Obtenez de l’aide auprès du [forum Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), du forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) ou du [support Azure](https://azure.microsoft.com/support/options/).

Pour contribuer à améliorer la documentation à l’intention de tous les utilisateurs, laissez un commentaire dans la section des commentaires ci-après si ce guide ne vous a pas aidé.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la résolution des problèmes temporaires, consultez [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).
* Pour explorer davantage le SDK Azure IoT et la gestion des nouvelles tentatives, consultez [Guide pratique pour gérer la connectivité et la messagerie fiable à l’aide des kits Azure IoT Hub device SDK](iot-hub-reliability-features-in-sdks.md#connection-and-retry).

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
