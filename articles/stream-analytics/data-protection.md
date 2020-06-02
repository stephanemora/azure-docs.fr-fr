---
title: Protection des données dans Azure Stream Analytics
description: Cet article explique comment chiffrer vos données privées utilisées par une tâche Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 8d201beb2ff4aba815749b12a506d2292779cb82
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857285"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Protection des données dans Azure Stream Analytics 

Azure Stream Analytics est une plateforme en tant que service entièrement gérée, qui vous permet de générer des pipelines d’analyse en temps réel. Le gros du travail, par exemple, l’approvisionnement du cluster, la mise à l’échelle des nœuds pour prendre en charge votre utilisation et la gestion des points de contrôle internes sont gérés en coulisses.

## <a name="encrypt-your-data"></a>Chiffrer vos données

Stream Analytics utilise automatiquement les normes de chiffrement les plus sophistiquées dans l’ensemble de son infrastructure pour chiffrer et sécuriser vos données. Vous pouvez tout simplement faire confiance à Stream Analytics pour stocker vos données en toute sécurité. Vous n’avez donc pas à vous soucier de la gestion de l’infrastructure.

Si vous souhaitez utiliser des clés gérées par le client (CMK) pour chiffrer vos données, vous pouvez utiliser votre propre compte de stockage (à usage général v1 ou v2) pour stocker toutes ressources de données privées requises par le runtime Stream Analytics. Votre compte de stockage peut être chiffré en fonction des besoins. Aucune de vos ressources de données privées n’est stockée de façon permanente par l’infrastructure Stream Analytics. 

Vous devez configurer ce paramètre au moment de la création de la tâche Stream Analytics, et ne pouvez pas le modifier pendant le cycle de vie de la tâche. Il n’est pas recommandé de modifier ou supprimer le stockage qu’utilise votre Stream Analytics. Si vous supprimez votre compte de stockage, vous supprimez définitivement toutes les ressources de données privées, ce qui entraînera l’échec de votre tâche. 

Une mise à jour ou une rotation de clés sur votre compte de stockage n’est pas possible à l’aide du portail Stream Analytics. Vous pouvez mettre à jour les clés à l’aide des API REST.


## <a name="configure-storage-account-for-private-data"></a>Configurer un compte de stockage pour des données privées 

Utilisez les étapes suivantes pour configurer votre compte de stockage pour des ressources de données privées. Cette configuration s’effectue à partir de votre tâche Stream Analytics, non à partir de votre compte de stockage.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure. 

1. Dans la liste des résultats, sélectionnez  **Analytics** > **Tâche Stream Analytics** . 

1. Renseignez la page de la tâche Stream Analytics avec les détails nécessaires, tels que le nom, la région et l’échelle. 

1. Activez la case à cocher *Sécuriser tous les actifs de données privés nécessaires à ce travail dans mon compte de stockage*.

1. Sélectionnez un compte de stockage dans votre abonnement. Notez qu’il n’est pas possible de modifier ce paramètre pendant le cycle de vie de la tâche. 

   ![Paramètres du compte de stockage de données privées](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Ressources de données privées stockées

Toutes les données privées requises que Stream Analytics doit conserver son stockées dans votre compte de stockage. Voici quelques exemples de ressources de données privées : 

* Requêtes que vous avez créées et configurations associées  

* Fonctions définies par l’utilisateur 

* Points de contrôle nécessaires au runtime Stream Analytics

* Captures instantanées de données de référence 

Les détails de connexion de vos ressources, que votre tâche Stream Analytics utilise, sont également stockés. Chiffrez votre compte de stockage pour sécuriser toutes vos données. 

Pour vous aider à répondre à vos obligations de conformité dans n’importe quel secteur ou environnement réglementé, apprenez-en davantage sur les [offres de conformité de Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="known-issues"></a>Problèmes connus
Il existe un problème connu où un travail qui utilise une clé gérée par le client connaît des échecs lors de l’utilisation d’une identité managée pour s’authentifier auprès de toutes les entrées ou sorties. Un correctif pour ce problème est en cours d’élaboration et sera bientôt disponible prochainement. 

## <a name="next-steps"></a>Étapes suivantes

* [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md)
* [Comprendre les entrées d’Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Concepts de point de contrôle et de relecture dans les tâches Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Utilisation de données de référence pour effectuer des recherches dans Stream Analytics](stream-analytics-use-reference-data.md)
