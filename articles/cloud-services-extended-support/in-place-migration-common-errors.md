---
title: Erreurs courantes et problèmes connus lors de la migration vers Azure Cloud Services (support étendu)
description: Vue d’ensemble des erreurs courantes lors de la migration de Cloud Services (classique) vers Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286757"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Erreurs courantes et problèmes connus lors de la migration vers Azure Cloud Services (support étendu)

Cet article traite des problèmes connus et des erreurs courantes que vous pouvez rencontrer lors de Cloud Services (classique) vers Cloud Services (support étendu). 

## <a name="known-issues"></a>Problèmes connus
Les problèmes suivants sont connus et traités.

| Problèmes connus | Limitation des risques | 
|---|---|
| Les instances de rôle redémarrent UD par UD après une validation réussie. | L’opération de redémarrage suit la même méthode que les déploiements de système d’exploitation invité mensuels. Ne validez pas la migration des services cloud avec une seule instance de rôle ou affectés par le redémarrage.| 
| Le portail Azure ne peut pas lire l’état de la migration après l’actualisation du navigateur. | Réexécutez l’opération de validation et de préparation pour revenir à l’état de migration d’origine. | 
| Certificat affiché en tant que ressource secrète dans le coffre de clés. | Après la migration, rechargez le certificat en tant que ressource de certificat pour simplifier l’opération de mise à jour sur Cloud Services (support étendu). | 
| Les étiquettes de déploiement ne sont pas enregistrées en tant que balises dans le cadre de la migration. | Créez manuellement les balises après la migration pour conserver ces informations.
| Le nom du groupe de ressources est en majuscules. | Sans impact. La solution n’est pas encore disponible. |
| Le nom du verrou sur le verrou Cloud Services (support étendu) est incorrect. | Sans impact. La solution n’est pas encore disponible. | 
| Le nom de l’adresse IP est incorrect dans le panneau du portail Cloud Services (support étendu). | Sans impact. La solution n’est pas encore disponible. | 
| Un nom DNS non valide s’affiche pour l’adresse IP virtuelle après l’opération de mise à jour sur un service cloud migré. | Sans impact. La solution n’est pas encore disponible. | 
| Une fois la préparation réussie, la liaison d’un nouveau déploiement Cloud Services (support étendu) comme échangeable n’est pas autorisée. | Ne liez pas un nouveau service cloud comme étant échangeable à un service cloud préparé. | 
| Les messages d’erreur doivent être mis à jour. | Sans impact. | 

## <a name="common-migration-errors"></a>Erreurs courantes de migration
Erreurs courantes de migration et mesures d’atténuation. 

| Message d’erreur | Détails | 
|---|---|
| Le type de ressource est introuvable dans l’espace de noms `Microsoft.Compute` pour la version d’API ’2020-10-01-preview’. | [Inscrivez l’abonnement](in-place-migration-overview.md#setup-access-for-migration) pour l’indicateur de fonctionnalité CloudServices pour accéder à la préversion publique. | 
| Le serveur a rencontré une erreur interne. Relancez la requête. | Réessayez l’opération, utilisez [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou contactez le support technique. | 
| Le serveur a rencontré une erreur inattendue lors de la tentative d’allocation des ressources réseau pour le service cloud. Relancez la requête. | Réessayez l’opération, utilisez [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou contactez le support technique. | 
| Le déploiement nom-déploiement dans le service cloud nom-service-cloud doit se trouver dans un réseau virtuel à migrer. | Le déploiement ne se trouve pas dans un réseau virtuel. Consultez [ce](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) document pour plus d’informations. | 
| La migration du déploiement nom-déploiement dans le service cloud nom-service-cloud n’est pas prise en charge, car il se trouve dans la région nom-région. Régions autorisées : [liste des régions disponibles]. | La région n’est pas encore prise en charge pour la migration. | 
| Le déploiement nom-déploiement dans service le cloud nom-service-cloud ne peut pas être migré car aucun sous-réseau n’est associé au rôle nom-rôle. Associez tous les rôles à un sous-réseau, puis réessayez la migration du service cloud. | Mettez à jour le déploiement du service cloud (classique) en le plaçant dans un sous-réseau avant la migration. |  
| Le déploiement nom-déploiement dans le service cloud nom-service-cloud ne peut pas être migré car le déploiement nécessite au moins une fonctionnalité qui n’est pas inscrite sur l’abonnement dans Azure Resource Manager. Inscrivez toutes les fonctionnalités requises pour migrer ce déploiement. Fonctionnalité(s) manquante(s) : [liste des fonctionnalités manquantes]. | Contactez le support technique pour accéder aux indicateurs de fonctionnalités inscrits. | 
| Impossible de migrer le déploiement, car le service cloud du déploiement a deux emplacements occupés. La migration des services cloud est uniquement prise en charge pour les déploiements qui sont le seul déploiement dans leur service cloud. Supprimez l’autre déploiement dans le service cloud pour poursuivre la migration de ce déploiement. | Consultez la liste de [scénarios non pris en charge](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) pour plus de détails. | 
| Le déploiement nom-déploiement dans le service hébergé cloud nom-service-cloud est dans l’état intermédiaire : état. Migration non autorisée. | Le déploiement est en cours de création, de suppression ou de mise à jour. Attendez que l’opération se termine et réessayez. | 
| Le déploiement nom-déploiement dans le service hébergé cloud nom-service-cloud a une ou plusieurs adresses IP réservées, mais aucun nom d’adresse IP réservée. Pour résoudre ce problème, mettez à jour le nom d’adresse IP réservée ou contactez le service d’assistance Microsoft Azure. | Mettez à jour le déploiement du service cloud. | 
| Le déploiement nom-déploiement dans le service hébergé cloud nom-service-cloud a une ou plusieurs adresses IP réservées nom-IP-réservée, mais aucun point de terminaison sur l’adresse IP réservée. Pour résoudre ce problème, ajoutez au moins un point de terminaison à l’adresse IP réservée. | Ajoutez un point de terminaison à une adresse IP réservée. | 
| La migration du déploiement {0} dans le service hébergé {1} est en cours de validation et ne peut pas être modifiée jusqu’à ce qu’elle se termine correctement.  | Patientez ou recommencez l’opération. | 
| La migration du déploiement {0} dans le service hébergé {1} est en cours d’abandon et ne peut pas être modifiée jusqu’à ce qu’elle se termine correctement. | Patientez ou recommencez l’opération. |
| Une ou plusieurs machines virtuelles dans le déploiement {0} dans le HostedService {1} sont en cours d’opération de mise à jour. Vous ne pouvez effectuer la migration tant que l’opération précédente ne s’est pas déroulée correctement. Recommencez dans quelques instants. | Attendez que l’opération soit terminée. | 
| La migration n’est pas prise en charge pour le déploiement {0} dans le HostedService {1}, car celui-ci utilise les fonctionnalités suivantes qui ne sont pas encore prises en charge pour la migration : déploiement hors réseau virtuel.| Le déploiement ne se trouve pas dans un réseau virtuel. Consultez [ce](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) document pour plus d’informations. | 
| Le nom du réseau virtuel ne peut pas être nul ou vide. | Fournissez le nom du réseau virtuel dans le corps de la requête REST | 
| Le nom du sous-réseau ne peut pas être nul ou vide. | Fournissez le nom du sous-réseau dans le corps de la requête REST. | 
| DestinationVirtualNetwork doit être défini sur l’une des valeurs suivantes : Default, New ou Existing. | Indiquez la propriété DestinationVirtualNetwork dans le corps de la requête REST. | 
| L’option de destination de réseau virtuel par défaut n’est pas implémentée. | La valeur « Default » n’est pas prise en charge pour la propriété DestinationVirtualNetwork dans le corps de la requête REST. | 
| Le déploiement {0} ne peut pas être migré, car le CSPKG n’est pas disponible. | Mettez à niveau le déploiement et réessayez. | 
| Le sous-réseau avec l’ID « {0} » se trouve dans un emplacement différent du déploiement « {1} » dans le service hébergé « {2} ». L’emplacement du sous-réseau est « {3} » et l’emplacement du service hébergé est « {4} ».  Spécifiez un sous-réseau au même emplacement que le déploiement. | Mettez à jour le service cloud pour que le service cloud et le sous-réseau se trouvent au même emplacement avant la migration. | 
| La migration du déploiement {0} dans le service hébergé {1} est en cours d’abandon et ne peut pas être modifiée jusqu’à ce qu’elle se termine correctement. | Attendez la fin de l’abandon ou réessayez l’abandon. Utilisez [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou contactez le support technique sinon. | 
| Le déploiement {0} dans le HostedService {1} n’a pas été préparé pour la migration. | Exécutez prepare sur le service cloud avant d’exécuter l’opération de validation. | 
| UnknownExceptionInEndExecute : Contract.Assert a échoué : rgName est nul ou vide : l’exception reçue dans EndExecute n’est pas une RdfeException. |   Utilisez [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou contactez le support technique. | 
| UnknownExceptionInEndExecute : une tâche a été annulée : l’exception reçue dans EndExecute n’est pas une RdfeException. | Utilisez [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou contactez le support technique. | 
| XrpVirtualNetworkMigrationError : échec de la migration du réseau virtuel. | Utilisez [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) ou contactez le support technique. | 
| Le déploiement {0} dans le HostedService {1} appartient au réseau virtuel {2}. Migrez le réseau virtuel {2} pour migrer ce HostedService {1}. | Consultez [Migration de réseau virtuel](in-place-migration-technical-details.md#virtual-network-migration). | 
| Le quota actuel pour le nom de ressource dans Azure Resource Manager est insuffisant pour terminer la migration. Le quota actuel est {0}, supplément requis : {1}. Effectuez une demande de support pour augmenter le quota et recommencez la migration une fois que le quota a été augmenté.    | Suivez les canaux appropriés pour demander une augmentation du quota : <br>[Augmentation du quota pour les ressources réseau](../azure-portal/supportability/networking-quota-requests.md) <br>[Augmentation du quota pour les ressources de calcul](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la configuration requise pour la migration, consultez [Détails techniques de la migration vers Azure Cloud Services (support étendu)](in-place-migration-technical-details.md)
