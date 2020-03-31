---
title: Codage la haute disponibilité d’Azure Media Services
description: Découvrez comment basculer vers un compte Media Services secondaire en cas de panne ou de défaillance d’un centre de données régional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934192"
---
# <a name="media-services-high-availability-encoding"></a>Encodage de la haute disponibilité dans Media Services 

Le service d’encodage Azure Media Services est une plateforme régionale de traitement par lots qui n’est actuellement pas conçue pour la haute disponibilité au sein d’une seule région. Actuellement, le service d’encodage n’assure pas de basculement instantané du service en cas de panne ou d’échec de l’un des centres de données régionaux du composant sous-jacent ou des services dépendants (tels que le stockage et SQL). Cet article explique comment déployer Media Services afin de conserver une architecture à haute disponibilité avec basculement et de garantir une disponibilité optimale pour vos applications.

Les instructions et les bonnes pratiques décrites dans l’article vous permettront de réduire le risque d’erreurs d’encodage et de retards, et de minimiser le temps de récupération si une panne se produit dans une seule région.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Comment créer un système d’encodage interrégion

* [Créez](create-account-cli-how-to.md) au moins deux comptes Azure Media Services.

    Les deux comptes doivent se trouver dans des régions différentes. Pour plus d’informations, voir [Régions dans lesquelles la plateforme Azure Media Services est déployée](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Chargez votre média dans la région à partir de laquelle vous envisagez d’envoyer le travail. Pour plus d’informations sur la façon de démarrer l’encodage, voir [Créer une entrée de travail à partir d’une URL HTTPS](job-input-from-http-how-to.md) ou [Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).

    Si vous devez ensuite renvoyer le [travail](transforms-jobs-concept.md) à une autre région, vous pouvez utiliser JobInputHttp ou [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) pour copier les données du conteneur de ressources source dans un conteneur de ressources situé dans l’autre région.
* Abonnez-vous aux messages JobStateChange dans chaque compte via Azure Event Grid. Pour plus d'informations, consultez les pages suivantes :

    * [Exemple d’analyse audio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) montrant comment surveiller un travail avec Azure Event Grid, incluant l’ajout d’un secours en cas de retard des messages Azure Event Grid pour une raison quelconque.
    * [Schémas Azure Event Grid pour les événements Media Services](media-services-event-schemas.md)
    * [S’inscrire à des événements par le biais du portail Azure ou de l’interface CLI](reacting-to-media-services-events.md) (vous pouvez également le faire avec le SDK de gestion EventGrid)
    * [SDK Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (qui prend en charge les événements Media Services en mode natif)

    Vous pouvez également consommer des événements Event Grid via Azure Functions.
* Quand vous créez un [travail](transforms-jobs-concept.md) :

    * Sélectionnez un compte de manière aléatoire dans la liste des comptes actuellement utilisés (cette liste contiendra normalement les deux comptes mais, si des problèmes sont détectés, il est possible qu’elle n’en contienne qu’un seul). Si la liste est vide, déclenchez une alerte pour qu’un opérateur puisse intervenir.
    * En règle générale, vous avez besoin d’une [unité réservée Multimédia](media-reserved-units-cli-how-to.md) par [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (sauf si vous utilisez [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) où 3 unités réservées Multimédia par JobOutput sont recommandées).
    * Obtenez le nombre d’unités réservées Multimédia (MRU) pour le compte choisi. Si le nombre d’**unités réservées Multimédia** actuel n’est pas déjà à la valeur maximale, ajoutez le nombre de MRU nécessaires au travail et mettez à jour le service. Si votre taux d’envoi de travaux est élevé et que vous interrogez fréquemment la valeur de MRU pour constater que vous avez atteint le maximum, utilisez un cache distribué pour la valeur avec un délai d’expiration raisonnable.
    * Tenez le compte du nombre de travaux en cours.

* Quand votre gestionnaire JobStateChange reçoit une notification indiquant qu’un travail a atteint l’état planifié, enregistrez le moment où il entre en état de planification et la région/le compte utilisés.
* Quand votre gestionnaire JobStateChange reçoit une notification indiquant qu’un travail a atteint l’état de traitement, marquez l’enregistrement du travail comme étant en cours de traitement.
* Quand votre gestionnaire JobStateChange reçoit une notification indiquant qu’un travail a atteint l’état Terminé/Erreur/Annulé, marquez l’enregistrement pour le travail comme final et décrémentez le nombre de travaux en cours. Obtenez le nombre d’unités réservées Multimédia pour le compte choisi et comparez le nombre de MRU actuel au nombre de travaux en cours. Si le nombre de travaux en cours est inférieur au nombre de MRU, décrémentez celui-ci et mettez à jour le service.
* Faites en sorte d’avoir un processus distinct qui examine périodiquement les enregistrements des travaux
    
    * Si vous avez des travaux à l’état planifié qui ne sont pas passés à l’état de traitement dans un laps de temps raisonnable pour une région donnée, supprimez cette région de la liste des comptes actuellement utilisés.  En fonction de vos exigences métier, vous pouvez décider d’annuler ces travaux immédiatement et de les renvoyer à l’autre région. Ou bien vous pouvez leur accorder un peu plus de temps pour passer à l’état suivant.
    * Selon le nombre d’unités réservées Multimédia configurées sur le compte et le taux d’envoi, il peut également y avoir des travaux en file d’attente que le système n’a pas encore été récupérés pour traitement.  Si la liste des tâches en file d’attente dépasse une limite acceptable dans une région, ces travaux peuvent être annulés et envoyés à l’autre région.  Toutefois, il peut s’agir d’un symptôme indiquant qu’il n’y a pas suffisamment d’unités réservées Multimédia configurées sur le compte pour la charge actuelle.  Si nécessaire, vous pouvez demander un quota plus important d’unités réservées Multimédia via le Support Azure.
    * Si une région a été supprimée de la liste des comptes, analysez-la en vue de sa récupération avant de la rajouter à la liste.  Vous pouvez surveiller l’intégrité régionale au travers des travaux existants dans la région (en vérifiant s’ils n’ont pas été annulés et renvoyés), en rajoutant le compte à la liste après un certain temps, et via des opérateurs surveillant les communications Azure en lien avec des pannes susceptibles d’affecter Azure Media Services.
    
Si vous constatez que le nombre de MRU augmente et diminue beaucoup, déplacez la logique de décrémentation vers la tâche périodique. Vérifiez que la logique d’envoi de prétravail compare le nombre de travaux en cours au nombre de MRU actuel pour déterminer si elle doit mettre à jour le nombre de MRU.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une diffusion en continu inter-région de vidéo à la demande](media-services-high-availability-streaming.md)
* Consulter les [exemples de code](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
