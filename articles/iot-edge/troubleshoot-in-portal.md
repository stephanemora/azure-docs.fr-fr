---
title: Résoudre des problèmes à partir du portail Azure - Azure IoT Edge | Microsoft Docs
description: Utilisez la page de résolution des problèmes dans le portail Azure pour superviser les appareils et les modules IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e0761262463d69fe80f7dc15ed1a5944dcc235bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531559"
---
# <a name="troubleshoot-iot-edge-devices-from-the-azure-portal"></a>Résolution des problèmes liés aux appareils IoT Edge dans le portail Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge offre un moyen simple de superviser et de dépanner des modules dans le portail Azure. La page de résolution des problèmes est un wrapper pour les méthodes directes de l’agent IoT Edge, ce qui vous permet de récupérer facilement les journaux de modules déployés et de les redémarrer à distance.

## <a name="prerequisites"></a>Configuration requise

Pour être totalement opérationnelle, cette fonctionnalité de résolution des problèmes du portail nécessite IoT Edge version 1.1.3 ou ultérieure si vous êtes sur la branche de support à long terme, ou la version 1.2.1 ou ultérieure si vous êtes sur la dernière branche stable. Le composant hôte IoT Edge et le module edgeAgent doivent tous deux être sur ces versions.

## <a name="access-the-troubleshooting-page"></a>Accès à la page de résolution des problèmes

Vous pouvez accéder à la page de résolution des problèmes du portail par le biais de la page de détails de l’appareil IoT Edge ou de la page de détails du module IoT Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

1. Dans le volet gauche, dans le menu, sélectionnez **IoT Edge**.

1. Sélectionnez l’appareil IoT Edge que vous voulez superviser dans la liste des appareils.

1. Dans la page des détails de l’appareil, vous pouvez sélectionner **Résoudre les problèmes** dans le menu ou sélectionner l’état d’exécution d’un module particulier que vous voulez inspecter.

   ![Dans la page des détails de l’appareil, sélectionnez Résoudre les problèmes ou l’état d’exécution d’un module.](./media/troubleshoot-in-portal/troubleshoot-from-device-details.png)

1. Dans la page des détails de l’appareil, vous pouvez également sélectionner le nom d’un module pour ouvrir la page des détails le concernant. À partir de là, vous pouvez sélectionner **Résoudre les problèmes** dans le menu.

   ![Dans la page de détails du module, sélectionnez Résoudre les problèmes.](./media/troubleshoot-in-portal/troubleshoot-from-module-details.png)

## <a name="view-module-logs-in-the-portal"></a>Afficher les journaux de module dans le portail

Dans la page **Résolution des problèmes**, vous pouvez consulter et télécharger des journaux à partir de l’un des modules en cours d’exécution sur votre appareil IoT Edge.

Cette page a une limite maximale de 1 500 lignes de journal, et tous les journaux dépassant cette limite seront tronqués. Si les journaux sont trop volumineux, toute tentative d’obtention de journaux de module échouera. Dans ce cas, essayez de modifier le filtre d’intervalle de temps pour récupérer moins de données ou envisagez d’utiliser des méthodes directes pour [Récupérer les journaux des déploiements IoT Edge](how-to-retrieve-iot-edge-logs.md) afin de collecter des fichiers journaux plus volumineux.

Utilisez le menu déroulant pour choisir le module à inspecter.

![Choisir des modules dans le menu déroulant](./media/troubleshoot-in-portal/select-module.png)

Par défaut, cette page affiche les quinze dernières minutes de journalisation. Sélectionnez le filtre **Intervalle de temps** pour voir d’autres journaux. Utilisez le curseur pour sélectionner une fenêtre de temps dans les 60 dernières minutes, ou cochez la case **Entrer une heure à la place** pour choisir une fenêtre de date/heure spécifique.

![Sélectionner un intervalle de temps](./media/troubleshoot-in-portal/select-time-range.png)

Une fois que vous avez les journaux du module dont vous voulez résoudre les problèmes pendant l’intervalle de temps que vous voulez inspecter, vous pouvez utiliser le filtre **Rechercher** pour récupérer des lignes spécifiques des journaux. Vous pouvez filtrer les avertissements ou les erreurs, ou indiquer un terme ou une expression spécifique à rechercher. La fonctionnalité **Recherche** prend en charge les recherches de texte en clair ou les [expressions régulières .NET](/dotnet/standard/base-types/regular-expression-language-quick-reference) pour les recherches plus complexes.

Vous pouvez télécharger les journaux de module sous forme de fichier texte. Le fichier journal téléchargé reflète tous les filtres actifs que vous avez appliqués aux journaux.

>[!TIP]
>L’utilisation du processeur sur un appareil atteint temporairement un pic quand il collecte des journaux en réponse à une demande émanant du portail. Ce comportement est attendu et l’utilisation doit se stabiliser une fois la tâche terminée.

## <a name="restart-modules"></a>Redémarrer les modules

La page de **Résolution des problèmes** comprend une fonctionnalité permettant de redémarrer un module. La sélection de cette option permet d’envoyer une commande à l’agent IoT Edge pour redémarrer le module sélectionné. Le redémarrage d’un module n’affecte pas votre capacité à récupérer des journaux antérieurs au redémarrage.

![Redémarrer un module à partir de la page de résolution des problèmes](./media/troubleshoot-in-portal/restart-module.png)

## <a name="next-steps"></a>Étapes suivantes

Retrouvez d’autres conseils pour [résoudre les problèmes liés à votre appareil IoT Edge](troubleshoot.md) ou découvrez les [problèmes courants et les résolutions](troubleshoot-common-errors.md). 

Si vous avez d'autres questions, créez une [Support request](https://portal.azure.com/#create/Microsoft.Support) pour obtenir de l'aide.
