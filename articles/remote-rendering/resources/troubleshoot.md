---
title: Dépanner
description: Informations sur la résolution des problèmes concernant Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678827"
---
# <a name="troubleshoot"></a>Dépanner

Cette page liste les problèmes courants qui interfèrent avec Azure Remote Rendering et les façons de les résoudre.

## <a name="client-cant-connect-to-server"></a>Le client ne peut pas se connecter au serveur

Vérifiez que vos pare-feu (sur l’appareil, dans les routeurs, etc.) ne bloquent pas les ports suivants :

* **50051 (TCP)**  : nécessaire pour la connexion initiale (négociation HTTP)
* **8266 (TCP+UDP)**  : nécessaire pour le transfert de données
* **5000 (TCP)** , **5433 (TCP)** , **8443 (TCP)**  : nécessaires pour [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Erreur 'Déconnecté : VideoFormatNotAvailable'

Vérifiez que votre GPU prend en charge le décodage vidéo matériel. Consultez [PC de développement](../overview/system-requirements.md#development-pc).

Si vous travaillez sur un ordinateur portable avec deux GPU, il est possible que le GPU que vous utilisez par défaut n’offre pas de fonctionnalité de décodage vidéo matériel. Si c’est le cas, essayez de forcer votre application à utiliser l’autre GPU. Cela est souvent possible dans les paramètres du pilote GPU.

## <a name="h265-codec-not-available"></a>Codec H265 non disponible

Le serveur peut refuser de se connecter avec une erreur **codec non disponible** pour deux raisons.

**Le codec H265 n’est pas installé :**

Tout d’abord, veillez à installer les **extensions vidéo HEVC** comme indiqué dans la section [Logiciels](../overview/system-requirements.md#software) de la configuration système requise.

Si les problèmes persistent, vérifiez que votre carte graphique prend en charge le codec H265 et que le pilote graphique le plus récent est installé. Pour connaître les informations spécifiques au fournisseur, consultez la section [PC de développement](../overview/system-requirements.md#development-pc) de la configuration système requise.

**Le codec est installé, mais ne peut pas être utilisé :**

La raison de ce problème est un paramètre de sécurité incorrect sur les DLL. Ce problème ne se manifeste pas quand vous tentez de regarder des vidéos encodées avec le codec H265. La réinstallation du codec ne résout pas non plus le problème. Effectuez plutôt les étapes suivantes :

1. Ouvrez **PowerShell** avec des droits d’administrateur, puis exécutez la commande suivante

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Cette commande doit générer la valeur `InstallLocation` du codec, similaire à ce qui suit :
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Ouvrez le dossier dans l’Explorateur Windows
1. Il doit y avoir un sous-dossier **x86** et un sous-dossier **x64**. Cliquez avec le bouton droit sur l’un des dossiers, puis choisissez **Propriétés**
    1. Sélectionnez l’onglet **Sécurité**, puis cliquez sur le bouton **Paramètres avancés**
    1. Cliquez sur **Modifier** pour le **Propriétaire**
    1. Tapez **Administrateurs** dans le champ de texte
    1. Cliquez sur **Vérifier les noms**, puis sur **OK**
1. Répétez les étapes ci-dessus pour l’autre dossier
1. Répétez également les étapes ci-dessus sur chaque fichier DLL présent dans les deux dossiers. Il doit y avoir quatre DLL en tout.

Pour vérifier que les paramètres sont désormais corrects, effectuez les étapes suivantes pour chacune des quatre DLL :

1. Sélectionnez **Propriétés > Sécurité > Modifier**
1. Parcourez la liste de tous les **Groupes/Utilisateurs**, puis vérifiez que le droit **Lire et exécuter** est défini pour chacun d’eux (la colonne **Autoriser** doit contenir une coche)

## <a name="low-video-quality"></a>Vidéo de mauvaise qualité

La qualité de la vidéo peut être compromise par la qualité du réseau ou par l’absence du codec vidéo H265.

* Consultez les étapes indiquées pour [identifier les problèmes réseau](#unstable-holograms).
* Consultez la [configuration système requise](../overview/system-requirements.md#development-pc) pour savoir comment installer le pilote graphique le plus récent.

## <a name="black-screen-after-successful-model-loading"></a>Écran noir après le chargement réussi d’un modèle

Si vous êtes connecté au runtime de rendu et que vous avez correctement chargé un modèle, mais que vous ne voyez ensuite qu’un écran noir, cela peut avoir plusieurs causes distinctes.

Nous vous recommandons de tester les opérations suivantes avant de procéder à une analyse plus approfondie :

* Le codec H265 est-il installé? Même s’il y existe un mécanisme de secours pour le codec H264, nous avons observé des cas où ce mécanisme de secours ne fonctionnait pas correctement. Consultez la [configuration système requise](../overview/system-requirements.md#development-pc) pour savoir comment installer le pilote graphique le plus récent.
* Quand vous utilisez un projet Unity, fermez Unity, supprimez les dossiers *library* et *obj* temporaires dans le répertoire du projet, puis chargez/générez à nouveau le projet. Dans certains cas, les données mises en cache entraînaient un mauvais fonctionnement de l’exemple sans aucune raison évidente.

Si ces deux étapes n’ont pas permis de résoudre le problème, il est nécessaire de déterminer si les trames vidéo sont reçues, ou non, par le client. Cela peut être déterminé par programmation, comme expliqué dans le chapitre [Requêtes de performances côté serveur](../overview/features/performance-queries.md). Le `FrameStatistics struct` a un membre qui indique le nombre de trames vidéo reçues. Si ce nombre est supérieur à 0 et qu’il augmente dans le temps, le client reçoit des trames vidéo réelles du serveur. Par conséquent, il doit s’agir d’un problème côté client.

### <a name="common-client-side-issues"></a>Problèmes courants côté client

**Le modèle ne se trouve pas dans la vue de frustum :**

Dans de nombreux cas, le modèle s’affiche correctement, mais il se trouve en dehors du frustum de l’appareil photo. Une raison courante à cela est que le modèle a été exporté avec un tableau croisé dynamique trop décentré, de sorte qu’il est coupé par le plan arrière de découpage de l’appareil photo. Cela permet d’interroger par programmation le cadre englobant du modèle et de visualiser la zone avec Unity sous la forme d’une zone de ligne ou d’imprimer ses valeurs dans le journal de débogage.

**Le pipeline de rendu Unity n’inclut pas les crochets de rendu :**

Azure Remote Rendering se raccroche au pipeline de rendu Unity pour créer la composition du cadre avec la vidéo et effectuer la reprojection. Pour vérifier que ces crochets existent, ouvrez le menu *Window > Analysis > Frame Debug* (Fenêtre > Analyse > Débogage de frames). Activez-le et vérifiez qu’il existe deux entrées pour `HolographicRemotingCallbackPass` dans le pipeline :

![Débogueur de frames Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Le code Unity utilisant l’API Remote Rendering ne se compile pas

Basculez le *type de build* de la solution Unity sur **Déboguer**. Lors du test de ARR dans l’éditeur Unity, le `UNITY_EDITOR` défini est disponible uniquement dans les builds « Debug ». Notez que cela n’est pas lié au type de build utilisé pour les [applications déployées](../quickstarts/deploy-to-hololens.md), dans lesquelles vous devez préférer les builds « Release ».

## <a name="unstable-holograms"></a>Hologrammes instables

Si les objets rendus semblent se déplacer avec les mouvements des têtes, vous rencontrez peut-être des problèmes avec la *Reprojection en phase tardive* (LSR). Pour obtenir des conseils sur la façon d’aborder une telle situation, consultez la section relative à la [reprojection en phase tardive](../overview/features/late-stage-reprojection.md).

Les hologrammes instables (hologrammes qui oscillent, déformés, qui vacillent ou qui sautent) peuvent également s’expliquer par une mauvaise connectivité réseau, en particulier une bande passante réseau insuffisante ou une latence trop élevée. La valeur de [statistiques de performances](../overview/features/performance-queries.md) `ARRServiceStats.VideoFramesReused` constitue un bon indicateur de la qualité de votre connexion réseau. Les trames réutilisées indiquent les situations où une ancienne trame vidéo devait être réutilisée côté client car aucune nouvelle trame vidéo n’était disponible, par exemple en raison d’une perte de paquets ou de variations de la latence du réseau. Si `ARRServiceStats.VideoFramesReused` est souvent supérieur à zéro, cela indique un problème réseau.

Une autre valeur à examiner est `ARRServiceStats.LatencyPoseToReceiveAvg`. Elle doit toujours être inférieure à 100 ms. Si vous voyez des valeurs plus élevées, cela indique que vous êtes connecté à un centre de données qui est trop éloigné.

Pour obtenir la liste des atténuations potentielles, consultez les [instructions pour la connectivité réseau](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Étapes suivantes

* [Configuration système requise](../overview/system-requirements.md)
* [Configuration requise pour le réseau](../reference/network-requirements.md)
