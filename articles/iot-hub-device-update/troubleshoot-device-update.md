---
title: Résoudre les problèmes courants de Device Update pour Azure IoT Hub | Microsoft Docs
description: Ce document fournit une liste de conseils et d’astuces pour vous aider à résoudre de nombreux problèmes que vous pouvez rencontrer avec Device Update pour IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: f5ea8cfe1df2ae89bb67675c9bf235d62dca4bf5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110082069"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Guide de dépannage de Device Update pour IoT Hub

Ce document liste quelques questions et problèmes courants que les utilisateurs Device Update ont signalés. Au fil de la progression de Device Update dans sa préversion publique, ce guide de dépannage sera régulièrement mis à jour avec de nouvelles questions et solutions. Si vous rencontrez un problème qui n’apparaît pas dans ce guide de dépannage, reportez-vous à la section [Contacter le support technique de Microsoft](#contact) pour documenter votre situation.

## <a name="importing-updates"></a><a name="import"></a>Importation des mises à jour

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>Q : Je ne parviens pas à connecter mon instance Device Update à mon instance IoT Hub.
_Vérifiez que vos routes de messages IoT Hub sont configurées correctement, conformément à la documentation [Ressources Device Update](./device-update-resources.md)._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>Q : Je rencontre une erreur liée au rôle (message d’erreur dans le portail Azure ou une erreur d’API 403).
_Il est possible que les autorisations d’accès ne soient pas configurées correctement. Vérifiez que vous avez configuré les autorisations d’accès correctement [Contrôle d’accès](./device-update-control-access.md)._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>Q : Je rencontre une erreur de type 500 lors de l’importation de contenu vers le service Device Update.
_Un code d’erreur dans la plage des 500 peut indiquer un problème avec le service Device Update. Attendez 5 minutes, puis réessayez. Si la même erreur persiste, suivez les instructions de la section [Contacter le support technique de Microsoft](#contact) pour créer une demande de support auprès de Microsoft._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>Q : Je rencontre un code d’erreur lors de l’importation de contenu et je voudrais l’analyser.
_Pour plus d’informations sur l’analyse des codes d’erreur, consultez la documentation [Codes d’erreur de Device Update](./device-update-error-codes.md)._

## <a name="device-failures"></a><a name="device-failure"></a> Échecs de l’appareil

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>Q : Comment puis-je vérifier que mon appareil est connecté à Device Update pour IoT Hub ?
_Vous pouvez vérifier que votre appareil est connecté à Device Update en regardant s’il apparaît sous la section des appareils « Non groupés » dans la vue Conformité du portail Azure._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>Q : La mise à jour d’un ou plusieurs de mes appareils échoue.
_Il y a de nombreuses causes racines possibles de l’échec de la mise à jour des appareils. Vérifiez que l’appareil est 1) connecté à votre instance d’IoT Hub et 2) connecté à votre instance Device Update, et que 3) le service Optimisation de la distribution est en cours d’exécution. Si ces trois conditions sont satisfaites pour votre appareil, suivez les instructions de la section [Contacter le support technique de Microsoft](#contact) pour créer une demande de support auprès de Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Déploiement d’une mise à jour

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>Q : J’ai déployé une mise à jour sur mes appareils, mais l’état de conformité n’indique pas cette dernière mise à jour. Que dois-je faire ?
_L’actualisation de l’état de conformité de l’appareil peut prendre jusqu’à 5 minutes. Attendez, puis revérifiez._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>Q : L’état du déploiement de mon appareil indique une incompatibilité. Que dois-je faire ?
_Les propriétés du fabricant et du modèle d’un appareil ciblé peuvent avoir été modifiées après la connexion de l’appareil à IoT Hub, ce qui fait que l’appareil est maintenant considéré comme incompatible avec le contenu de la mise à jour du déploiement actuel._

_Vérifiez l’[interface ADU Core](./device-update-plug-and-play.md) pour voir le fabricant et le modèle indiqués par votre appareil au service Device Update, et vérifiez s’ils correspondent au fabricant et au modèle que vous avez spécifiés dans le [manifeste d’importation](./import-concepts.md) du contenu de la mise à jour à déployer. Vous pouvez modifier ces propriétés pour un appareil donné en utilisant le [fichier de configuration de Device Update](./device-update-configuration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>Q : Je vois que mon déploiement est dans la phase « Active », mais la mise à jour n’est « En cours » sur aucun de mes appareils. Que dois-je faire ?
_Vérifiez que la date de début de votre déploiement n’est pas définie sur un moment dans le futur. Quand vous créez un déploiement, à titre de protection, la date de début du déploiement est par défaut le jour qui suit, sauf si vous la changez explicitement. Vous pouvez attendre que la date de début de votre déploiement arrive, ou bien annuler le déploiement en cours et créer un nouveau déploiement avec la date de début souhaitée._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>Q : J’essaie de regrouper mes appareils, mais je ne vois pas l’étiquette dans la liste déroulante lors de la création d’un groupe.
_Vérifiez que vous avez configuré correctement les routes de messages dans votre hub IoT conformément à la documentation [Ressources Device Update](./device-update-resources.md). Vous devez réétiqueter votre appareil après avoir configuré la route._

_Une autre cause racine peut être que vous avez appliqué l’étiquette avant de connecter votre appareil à Device Update pour IoT Hub. Vérifiez que votre appareil est déjà connecté à Device Update. Vous pouvez vérifier que votre appareil est connecté à Device Update pour IoT Hub en regardant s’il apparaît sous la section des appareils « Non groupés » dans la vue Conformité du portail Azure. Ajoutez temporairement une étiquette d’une valeur différente, puis ajoutez à nouveau votre étiquette souhaitée une fois que l’appareil est connecté._

_Si vous utilisez le service Device Provisioning, veillez à étiqueter vos appareils une fois qu’ils sont provisionnés et non pas pendant le processus de création des appareils. Si vous avez déjà étiqueté votre appareil lors de l’étape de création des appareils, vous devez étiqueter temporairement votre appareil avec une valeur différente une fois qu’il est provisionné, puis ajouter à nouveau l’étiquette que vous aviez prévue._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>Q : Mon déploiement s’est terminé avec succès, mais certains appareils n’ont pas pu être mis à jour.
_Ceci peut avoir été provoqué par une erreur côté client sur les appareils dont la mise à jour a échoué. Consultez la section Défaillances des appareils de ce guide de dépannage._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>Q : J’ai rencontré une erreur dans l’expérience utilisateur en tentant de lancer un déploiement.
_Ceci peut avoir été provoqué par un bogue du service ou de l’expérience utilisateur, ou par un problème des autorisations de l’API. Suivez les instructions de la section [Contacter le support technique de Microsoft](#contact) pour créer une demande de support auprès de Microsoft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>Q : J’ai démarré un déploiement, mais il n’atteint pas un état de finalisation.
_Ceci peut avoir été provoqué par un problème de performance du service, un bogue du service ou un bogue du client. Réessayez votre déploiement après 10 minutes. Si vous rencontrez le même problème, extrayez les journaux de vos appareils et reportez-vous à la section Défaillances des appareils de ce guide de dépannage. Si le même problème persiste, suivez les instructions de la section [Contacter le support technique de Microsoft](#contact) pour créer une demande de support auprès de Microsoft._

### <a name="q-i-migrated-from-a-device-level-agent-to-adding-the-agent-as-a-module-identity-on-the-device-and-my-update-shows-as-in-progress-even-though-it-has-been-applied-to-the-device"></a>Q : J’ai migré de l’agent au niveau de l’appareil vers l’agent ajouté comme identité de module sur l’appareil. Ma mise à jour apparaît comme « en cours » alors qu’elle a été appliquée à l’appareil.
La raison peut être que vous n’avez pas supprimé l’ancien agent qui était en communication sur le jumeau d’appareil. Lorsque vous provisionnez l’agent Device Update en tant que module (cf. [procédure](device-update-agent-provisioning.md)), toutes les communications qui ont lieu entre l’appareil et le service Device Update se produisent sur le jumeau de module. N’oubliez pas d’étiqueter le jumeau de module de l’appareil lorsque vous créez des [groupes](device-update-groups.md) et que toutes les [communications](device-update-plug-and-play.md) doivent avoir lieu sur le jumeau de module.

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Téléchargement des mises à jour sur les appareils

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>Q : Comment faire reprendre un téléchargement quand un appareil se reconnecte après une période de déconnexion ?
_Le téléchargement va reprendre de lui-même une fois la connectivité restaurée dans un délai de 24 heures. Au-delà de 24 heures, le téléchargement devra être relancé par l’utilisateur._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Cache connecté Microsoft (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>Q : Je rencontre un problème lors de la tentative de déploiement du module MCC sur mon appareil IoT Edge.
_Reportez-vous à la [documentation IoT Edge]() pour déployer des modules Edge sur des appareils IoT Edge. Vous pouvez vérifier que le module MCC fonctionne correctement sur votre appareil IoT Edge en accédant à http://localhost:5100/Summary._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>Q : Un de mes appareils IoT tente de télécharger une mise à jour via MCC, mais échoue.
_Plusieurs problèmes peuvent provoquer l’échec de la connexion d’un appareil IoT à MCC. Pour diagnostiquer le problème, récupérez les journaux du client DO et de Nginx auprès de l’appareil en échec (consultez la section [Contacter le support technique de Microsoft](#contact) pour des instructions sur la récupération des journaux des clients)._

_Votre appareil peut échouer à extraire du contenu depuis Internet pour le passer à son module MCC car l’URL qu’il utilise n’est pas autorisée. Pour déterminer si c’est le cas, vous devez vérifier vos variables d’environnement IoT Edge dans le portail Azure._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Contacter le support technique de Microsoft

Si vous rencontrez des problèmes qui ne peuvent pas être résolus à l’aide des questions fréquentes ci-dessus, vous pouvez faire une demande de support auprès du support technique de Microsoft via l’interface du portail Azure. Selon la catégorie que vous indiquez pour votre problème, vous pouvez être invité à rassembler et à partager des données supplémentaires pour aider le support technique de Microsoft à examiner votre problème. 

Voici des instructions sur la façon de collecter chaque type de données. Vous pouvez utiliser [getDevices]() pour rechercher des informations supplémentaires dans la réponse de la charge utile de l’API.

En outre, les informations suivantes peuvent être utiles pour délimiter la cause racine de votre problème :
* Le type d’appareil que vous tentez de mettre à jour (Azure Percept, passerelle IoT Edge, etc.)
* Le type de client Device Update que vous utilisez (basé sur une image, basé sur un package, simulateur)
* Le système d’exploitation exécuté par votre appareil
* Des détails concernant l’architecture de votre appareil
* Si vous avez réussi à utiliser Device Update pour mettre à jour un appareil

Si vous disposez d’une ou plusieurs des informations ci-dessus, incluez-la dans votre description du problème.

### <a name="collecting-client-logs"></a>Collecte des journaux du client

* Sur l’appareil Raspberry Pi, deux ensembles de journaux se trouvent ici :

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Pour le client empaqueté, les journaux se trouvent ici :

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Pour le simulateur, les journaux se trouvent ici :

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Codes d’erreur
Vous pouvez être invité à fournir les codes d’erreur quand vous signalez d’un problème lié à l’importation d’une mise à jour, à une défaillance d’appareil ou au déploiement d’une mise à jour.

Les codes d’erreur peuvent être obtenus en examinant l’interface [ADUCoreInterface](./device-update-plug-and-play.md). Pour plus d’informations sur l’analyse des codes d’erreur pour l’autodiagnostic et le dépannage, reportez-vous à la documentation sur les [codes d’erreur de Device Update](./device-update-error-codes.md).

### <a name="trace-id"></a>ID de trace
Vous pouvez être invité à fournir un ID de trace quand vous signalez un problème lié à l’importation ou au déploiement d’une mise à jour.

Vous pouvez trouver l’ID de trace pour une action utilisateur donnée dans la réponse de l’API ou dans la section Historique de l’importation de l’interface utilisateur du portail Azure. 

Actuellement, les ID de trace pour les actions de déploiement sont accessibles seulement via la réponse de l’API.

### <a name="deployment-id"></a>ID de déploiement
Vous pouvez être invité à fournir un ID de déploiement quand vous signalez un problème lié au déploiement d’une mise à jour.

L’ID de déploiement est créé par l’utilisateur lors de l’appel de l’API pour lancer un déploiement.

Actuellement, les ID de déploiement pour les déploiements lancés à partir de l’interface utilisateur du portail Azure sont générés automatiquement et ne sont pas exposés à l’utilisateur.

### <a name="iot-hub-instance-name"></a>Nom de l’instance IoT Hub
Vous pouvez être invité à fournir le nom de votre instance IoT Hub quand vous signalez un problème lié aux défaillances des appareils ou au déploiement d’une mise à jour.

Le nom du hub IoT est choisi par l’utilisateur lors du provisionnement initial.

### <a name="device-update-account-name"></a>Nom du compte Device Update
Vous pouvez être invité à fournir le nom de votre compte Device Update quand vous signalez un problème lié à l’importation d’une mise à jour, à des défaillances d’appareils ou au déploiement d’une mise à jour.

Le nom du compte Device Update est choisi par l’utilisateur lors de la première inscription au service. Vous trouverez plus d’informations dans la documentation [Ressources Device Update](./device-update-resources.md).

### <a name="device-update-instance-name"></a>Nom de l’instance Device Update
Vous pouvez être invité à fournir le nom de votre instance Device Update quand vous signalez un problème lié à l’importation d’une mise à jour, aux défaillances des appareils ou au déploiement d’une mise à jour.

Le nom de l’instance Device Update est choisi par l’utilisateur lors du provisionnement initial. Vous trouverez plus d’informations dans la documentation [Ressources Device Update](./device-update-resources.md).

### <a name="device-id"></a>ID de périphérique
Vous pouvez être invité à fournir un ID d’appareil quand vous signalez un problème lié aux défaillances des appareils ou au déploiement d’une mise à jour.

L’ID d’appareil est défini par le client lors du provisionnement initial de l’appareil. Il peut également être récupéré auprès du jumeau d’appareil de l’appareil.

### <a name="update-id"></a>ID de mise à jour
Vous pouvez être invité à fournir un ID de mise à jour quand vous signalez un problème lié au déploiement d’une mise à jour.

L’ID de mise à jour est défini par le client lors du lancement d’un déploiement.

### <a name="nginx-logs"></a>Journaux Nginx
Vous pouvez être invité à fournir des journaux Nginx quand vous signalez un problème lié au cache connecté Microsoft.

### <a name="adu-conftxt"></a>ADU-conf.txt
Vous pouvez être invité à fournir le fichier de configuration de Device Update (« adu-conf.txt ») quand vous signalez un problème lié au déploiement d’une mise à jour.

Le fichier de configuration est facultatif et il est créé par l’utilisateur en suivant les instructions de la documentation de [Configuration de Device Update](./device-update-configuration-file.md).

### <a name="import-manifest"></a>Manifeste d’importation
Vous pouvez être invité à fournir votre manifeste d’importation quand vous signalez un problème lié à l’importation ou au déploiement d’une mise à jour.

Le manifeste d’importation est un fichier créé par le client lors de l’importation de contenu de mise à jour vers le service Device Update.

**[Étape suivante : En savoir plus sur les codes d’erreur de Device Update](.\device-update-error-codes.md)**
