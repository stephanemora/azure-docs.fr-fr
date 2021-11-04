---
title: Déplacer un cluster Azure Service Fabric vers une nouvelle région
description: Découvrez comment migrer un cluster Azure Service Fabric et des applications vers une autre région.
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: micraft
ms.openlocfilehash: 3cbd2e21508296b6174a2322559973fb98728e2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083476"
---
# <a name="move-an-azure-service-fabric-cluster-to-a-new-region"></a>Déplacer un cluster Azure Service Fabric vers une nouvelle région

Les ressources de cluster Service Fabric sont par nature étendues à une région. Cette étendue signifie que le « déplacement dans la région » se fait en créant d’abord un nouveau cluster dans la région de destination, en migrant les charges de travail existantes, puis en dirigeant le trafic vers cette nouvelle région de destination. Dans ce document, nous décrivons les étapes nécessaires à cette migration. Plusieurs points de décision peuvent rendre la migration plus ou moins complexe. Cela comprend la façon dont vous avez configuré et configuré votre cluster et vos applications, la façon dont la communication fonctionne dans votre cluster, et si vos charges de travail sont sans état, avec état ou une combinaison des deux.  


## <a name="steps-to-follow-for-a-region-migration"></a>Procédure de migration vers une nouvelle région

Avant de procéder à une migration régionale, nous vous recommandons de créer un banc de test afin de mettre en pratique cette procédure. 

## <a name="create-new-cluster"></a>Créer un cluster
1. [Configurez un cluster dans la nouvelle région](./service-fabric-cluster-creation-via-arm.md#use-your-own-custom-template) en réattribuant votre modèle ARM existant pour la topologie de votre cluster et de votre infrastructure. Si vous ne disposez de modèle ARM décrivant votre cluster, il vous est recommandé de récupérer le modèle ARM actuel depuis [Azure Resource Explorer](https://resources.azure.com/). Azure Resource Explorer peut vous aider à découvrir vos ressources déployées ainsi que les informations de configuration correspondantes pour créer un ou plusieurs modèles ARM qui vous permettent de déployer à plusieurs reprises des clones de votre environnement existant. Testez et vérifiez que vous disposez de modèles ARM fonctionnels susceptibles de déployer des clones de votre environnement existant avant de poursuivre. 

## <a name="move-applications-and-traffic"></a>Déplacer les applications et le trafic
1. [Déployez des applications et des services existants via ARM](service-fabric-application-arm-resource.md). Veillez à conserver toutes les personnalisations de paramètres d’application ou de configuration que vous avez effectuées. Par exemple, si votre application dispose d’un paramètre « count » avec une valeur par défaut de 5, mais que dans votre environnement source vous avez mis à niveau la valeur de ce paramètre sur 7, vous devez vous assurer que le déploiement de l’application dans la nouvelle région présente la valeur 7. Si vous n’utilisez pas ARM pour gérer vos instances de service et d’application, vous êtes responsable de l’identification de tous les services et applications exécutés dans la région actuelle, de leur configuration et de la duplication de ces services et applications dans la nouvelle région ou le nouveau cluster. 

2. Migrer vos services  
   -  Pour les charges de travail avec état : 
      * <p>Pour vous assurer que vos services avec état ont atteint un point stable, assurez-vous d’abord que vous avez arrêté le trafic entrant vers ces services. La façon de procéder dépend de la façon dont le trafic est remis à vos services. Par exemple, vous devrez peut-être couper le service à partir d’Event Hubs ou empêcher un service comme APIM ou l’équilibreur de charge réseau Azure de router le trafic vers le service en supprimant les règles de routage ou de transfert qui conviennent. Une fois que le trafic a cessé et que les services ont terminé le travail en arrière-plan lié à ces requêtes, vous pouvez continuer. </p>
      
      * Effectuez une sauvegarde à partir de n’importe quel service avec état à l’aide du [service de restauration de sauvegarde](service-fabric-reliable-services-backup-restore.md) et effectuez une [sauvegarde à la demande](service-fabric-backup-restore-service-ondemand-backup.md). Assurez-vous que cette opération est effectuée une fois le trafic arrêté et la tâche de traitement en arrière-plan terminée. Lorsque l’opération est terminée, vous pouvez [restaurer les données](service-fabric-backup-restore-service-trigger-restore.md) dans les services avec état de la nouvelle région ou du nouveau cluster. Le compte de stockage Azure utilisé pour effectuer la sauvegarde doit être accessible à partir de la nouvelle région.

   -  Pour des services sans état : 
      * <p>Aucune autre tâche, en dehors du déploiement des services dans le nouveau cluster, ne doit être nécessaire, idéalement dans le cadre du déploiement de l’application ARM effectué à l’étape 2, et en veillant à ce qu’ils soient configurés de la même façon que dans le cluster source.</p>

   -  Pour tous les services :  
      * <p>Assurez-vous que toutes les étapes de communication entre les clients et les services sont configurées de façon similaire au cluster source. Par exemple, cette validation peut consister à s’assurer que les intermédiaires comme Event Hubs, les équilibreurs de charge réseau, les passerelles d’application ou la gestion des API sont configurés avec les règles nécessaires pour permettre au trafic de circuler vers le cluster.</p>  

3. Redirigez le trafic de l’ancienne région vers la nouvelle région. Nous vous recommandons d’utiliser [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) pour la migration, car il propose différentes [méthodes de routage](../traffic-manager/traffic-manager-routing-methods.md). La façon dont vous mettez à jour vos règles de routage du trafic dépend de votre souhait de conserver ou de déprécier la région existante, de même que du flux de trafic dans votre application. Vous devrez peut-être vérifier si les adresses IP privées/publiques ou les noms DNS peuvent être déplacés entre différentes ressources Azure dans différentes régions. Service Fabric n’a pas connaissance de cette partie de votre système et vous devez donc l’examiner et, si nécessaire, faire appel aux équipes Azure impliquées dans votre flux de trafic, notamment s’il est plus complexe ou si votre charge de travail est critique en termes de latence. Des documents tels que [Configurer un domaine personnalisé](../api-management/configure-custom-domain.md), [Adresses IP publiques](../virtual-network/ip-services/public-ip-addresses.md) et [Zones DNS et enregistrements](../dns/dns-zones-records.md) peuvent vous être utiles, et sont des exemples d’informations dont vous aurez besoin selon vos flux de trafic et protocoles. Voici deux exemples de scénarios illustrant l’approche de mise à jour du routage du trafic :  
   * Si vous n’envisagez pas de conserver la région source existante et si un DNS/CNAME est associé à l’adresse IP publique d’un équilibreur de charge réseau distribuant les appels vers votre cluster source d’origine. Mettez à jour le DNS/CNAME à associer à une nouvelle adresse IP publique du nouvel équilibreur de charge réseau dans la nouvelle région. Si vous effectuez ce transfert, les clients utilisant le cluster existant basculeront vers le nouveau cluster. 
  
   * Si vous envisagez de conserver la région source existante et si vous disposez d’un DNS/CNAME associé à l’adresse IP publique d’un équilibreur de charge réseau qui distribuait les appels à votre cluster source d’origine. Configurez une instance d’Azure Traffic Manager, puis associez le nom DNS à cette instance Azure Traffic Manager. Azure Traffic Manager peut être configuré pour ensuite effectuer le routage vers les équilibreurs de charge réseau au sein de chaque région. 

4. Si vous envisagez de conserver les deux régions, vous disposerez généralement d’une sorte de « synchronisation d’arrière-plan », où la source de vérité est conservée dans un magasin distant, par exemple SQL, CosmosDB, Stockage Blob ou Fichier, qui est ensuite synchronisé entre les régions. Si cela s’applique à votre charge de travail, il vous est recommandé de vérifier que les données circulent entre les régions comme prévu.  

## <a name="final-validation"></a>Validation finale
1. En guise de validation finale, vérifiez que le trafic est transmis comme prévu et que les services de la nouvelle région (voire de l’ancienne région) fonctionnent normalement. 

2. Si vous n’envisagez pas de conserver la région source d’origine, à ce stade, les ressources de cette région peuvent être supprimées. Nous vous recommandons d’attendre un certain temps avant de supprimer les ressources et ce, en cas de problème nécessitant une restauration de la région source d’origine.  

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez déplacé votre cluster et vos applications vers une nouvelle région, vous devez vérifier que les sauvegardes sont configurées pour protéger les données requises.

> [!div class="nextstepaction"]
> [Configurer des sauvegardes après la migration](service-fabric-backuprestoreservice-quickstart-azurecluster.md)