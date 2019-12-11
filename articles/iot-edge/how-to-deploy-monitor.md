---
title: Déployer des modules à grande échelle à partir du portail Azure – Azure IoT Edge
description: Utilisez le portail Azure afin de créer des déploiements automatiques pour les groupes d’appareils IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665846"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure

Créez un **déploiement automatique IoT Edge** dans le portail Azure pour gérer les déploiements en cours sur plusieurs appareils à la fois. Les déploiements automatiques IoT Edge font partie de la fonctionnalité [Gestion automatique des appareils](/azure/iot-hub/iot-hub-automatic-device-management) d’IoT Hub. Les déploiements sont des processus dynamiques qui vous permettent de déployer plusieurs modules sur plusieurs appareils, de suivre l’état et l’intégrité des modules, et d’apporter des modifications si nécessaire. 

Pour plus d’informations, consultez [Comprendre les déploiements automatiques IoT Edge pour un seul ou de nombreux appareils](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifier les appareils à l’aide de balises

Avant de pouvoir créer un déploiement, vous devez être en mesure de spécifier les appareils concernés. Azure IoT Edge identifie les appareils à l’aide de **balises** dans le jumeau d’appareil. Chaque appareil peut avoir plusieurs balises que vous pouvez définir comme bon vous semble pour votre solution. Par exemple, si vous gérez un campus de bâtiments intelligents, vous pouvez ajouter les balises suivantes à un appareil :

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Pour plus d’informations sur les étiquettes et les jumeaux d’appareils, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Créer un déploiement

1. Accédez à votre IoT Hub dans le [Portail Azure](https://portal.azure.com). 
1. Sélectionnez **IoT Edge**.
1. Sélectionnez **Ajouter le déploiement IoT Edge**.

La création d’un déploiement nécessite cinq étapes. Les sections suivantes les décrivent en détail. 

### <a name="step-1-name-and-label"></a>Étape 1 : Nom et étiquette

1. Donnez à votre déploiement un nom unique comportant au plus 128 lettres minuscules. Évitez les espaces et les caractères non valides suivants : `& ^ [ ] { } \ | " < > /`.
1. Vous pouvez ajouter des étiquettes sous forme de paires clé-valeur pour vous aider à suivre vos déploiements. Par exemple, **HostPlatform** et **Linux**, ou **Version** et **3.0.1**.
1. Sélectionnez **Suivant** pour passer à l’étape 2. 

### <a name="step-2-add-modules-optional"></a>Étape 2 : ajouter des modules (facultatif)

Vous pouvez ajouter jusqu’à 20 modules à un déploiement. 

Si vous créez un déploiement sans module, il supprime tous les modules actuels des appareils cibles. 

Pour ajouter un module à partir d’Azure Stream Analytics, effectuez les étapes suivantes :

1. Dans la section **Modules de déploiement** de la page, cliquez sur **Ajouter**.
1. Sélectionnez **Module Azure Stream Analytics**.
1. Choisissez votre **abonnement** dans le menu déroulant.
1. Choisissez votre **travail IoT Edge** dans le menu déroulant.
1. Sélectionnez **Enregistrer** pour ajouter votre module au déploiement. 

Pour ajouter du code personnalisé en tant que module, ou pour ajouter manuellement un module de service Azure, effectuez les étapes suivantes :

1. Dans la section **Paramètres du registre de conteneurs** de la page, fournissez les noms et informations d'identification des registres de conteneurs privés qui contiennent les images de module pour ce déploiement. L’agent IoT Edge signale l’erreur 500 s’il ne peut pas trouver les informations d’identification du registre de conteneurs pour une image Docker.
1. Dans la section **Modules de déploiement** de la page, cliquez sur **Ajouter**.
1. Sélectionnez **Module IoT Edge**.
1. Donnez un **Nom** à votre module.
1. Dans le champ **URI de l’image**, entrez l’image conteneur pour votre module. 
1. Spécifiez les **options de création de conteneur** qui doivent être passées au conteneur. Pour plus d’informations, consultez [docker create](https://docs.docker.com/engine/reference/commandline/create/).
1. Utilisez le menu déroulant pour sélectionner une **Stratégie de redémarrage**. Choisissez parmi les options suivantes : 
   * **Toujours** : le module redémarre toujours s’il s’arrête pour une raison quelconque.
   * **jamais** : le module ne redémarre jamais s’il s’arrête pour une raison quelconque.
   * **on-failure** (en cas d’échec) : le module redémarre s’il se bloque, mais pas s’il est fermé correctement. 
   * **on-unhealthy** (en cas d’état défectueux) : le module redémarre s’il se bloque ou s’il retourne un état défectueux. C’est à chaque module d’implémenter la fonction d’état d’intégrité. 
1. Utilisez le menu déroulant pour sélectionner **l’État souhaité** pour le module. Choisissez parmi les options suivantes :
   * **en cours d’exécution** : il s’agit de l’option par défaut. Le module commence à s’exécuter immédiatement après son déploiement.
   * **arrêté** : après son déploiement, le module reste inactif jusqu’à ce que vous-même ou un autre module demandiez son démarrage.
1. Sélectionnez **Définir les propriétés souhaitées du jumeau de module** si vous souhaitez ajouter des balises ou d'autres propriétés au jumeau de module.
1. Entrez les **variables d'environnement** correspondant à ce module. Les variables d’environnement fournissent des informations de configuration à un module.
1. Sélectionnez **Enregistrer** pour ajouter votre module au déploiement. 

Une fois que vous avez configuré tous les modules pour un déploiement, sélectionnez **Suivant** pour passer à l’étape 3.

### <a name="step-3-specify-routes-optional"></a>Étape 3 : spécifier des itinéraires (facultatif)

Les itinéraires définissent comment les modules communiquent les uns avec les autres dans un déploiement. Par défaut, l’Assistant vous donne un itinéraire nommé **route** et défini sous la forme **FROM /\* INTO $upstream**, ce qui signifie que tous les messages issus des modules sont envoyés à votre hub IoT.  

Ajoutez ou mettez à jour les itinéraires avec des informations issues de [Déclarer des itinéraires](module-composition.md#declare-routes), puis sélectionnez **Suivant** pour passer à la section de vérification.

### <a name="step-4-specify-metrics-optional"></a>Étape 4 : Spécifier les métriques (facultatif)

Les métriques fournissent des nombres récapitulatifs des différents états qu’un appareil peut signaler après l’application d’un contenu de configuration.

1. Entrez un nom pour **Nom de métrique**.

1. Entrez une requête pour **Critères de la métrique**. La requête est basée sur les [propriétés signalées](module-edgeagent-edgehub.md#edgehub-reported-properties) du jumeau de module du hub IoT Edge. La métrique représente le nombre de lignes retournées par la requête.

   Par exemple :

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Étape 5 : Appareils cibles

Utilisez la propriété tags à partir de vos appareils pour cibler des appareils spécifiques qui doivent recevoir ce déploiement. 

Étant donné que plusieurs déploiements peuvent cibler le même appareil, vous devez donner à chaque déploiement un numéro de priorité. En cas de conflit, le déploiement avec la priorité la plus élevée prévaut (plus la valeur est grande, plus la priorité est élevée). Si deux déploiements ont le même numéro de priorité, celui qui a été créé le plus récemment prévaut. 

1. Entrez un entier positif pour la **Priorité** du déploiement.
1. Entrez une **Condition cible** pour déterminer quels sont les appareils ciblés par ce déploiement. La condition est basée sur les balises de jumeau d’appareil ou sur les propriétés signalées du jumeau d’appareil et doit correspondre au format de l’expression. Par exemple, `tags.environment='test'` ou `properties.reported.devicemodel='4000x'`. 
1. Sélectionnez **Suivant** pour passer à l’étape finale.

### <a name="step-6-review-deployment"></a>Étape 6 : Vérifier le déploiement

Passez en revue les informations de votre déploiement, puis sélectionnez **Envoyer**.

## <a name="deploy-modules-from-azure-marketplace"></a>Déployer des modules à partir de la Place de marché Azure

La Place de marché Azure est un marché d’applications et de services en ligne qui vous permet de naviguer au milieu d’un large éventail d’applications et de solutions d’entreprise certifiées et optimisées pour s’exécuter sur Azure, notamment les [modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Vous pouvez également accéder à la Place de marché Azure via le Portail Azure sous **Créer une ressource**.

Vous pouvez déployer un module IoT Edge à partir de la Place de marché Azure ou du portail Azure :

1. Recherchez un module et entamez le processus de déploiement.

   * Portail Azure : Recherchez un module et sélectionnez **Créer**.

   * Place de marché Azure :

     1. Recherchez un module et sélectionnez **Obtenir maintenant**.
     1. Acceptez les conditions d’utilisation et la politique de confidentialité du fournisseur en sélectionnant **Continuer**.

1. Choisissez votre abonnement et le hub IoT auquel l'appareil cible est joint.

1. Choisissez **Déployer à l'échelle**.

1. Choisissez d'ajouter le module à un nouveau déploiement ou à un clone d'un déploiement existant ; en cas de clonage, sélectionnez le déploiement existant dans la liste.

1. Sélectionnez **Créer** pour poursuivre le processus de création d'un déploiement à l'échelle. Vous pourrez spécifier les mêmes détails que pour n'importe quel déploiement.

## <a name="monitor-a-deployment"></a>Surveiller un déploiement

Pour afficher les détails d’un déploiement et surveiller les appareils qui l’exécutent, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub. 
1. Sélectionnez **IoT Edge**.
1. Sélectionnez **Déploiements IoT Edge**. 

   ![Afficher les déploiements IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Inspectez la liste des déploiements. Pour chaque déploiement, vous pouvez consulter les détails suivants :
   * **ID** : nom du déploiement.
   * **Target condition (Condition cible)** : balise utilisée pour définir les appareils ciblés.
   * **Priorité** : numéro de priorité du déploiement.
   * **System metrics** - **Targeted** (Métriques système - Ciblé) spécifie le nombre de jumeaux d’appareil dans IoT Hub qui correspondent à la condition de ciblage, et **Applied** (appliqué) spécifie le nombre d’appareils dont les jumeaux de module se sont vu appliquer le contenu de déploiement dans IoT Hub. 
   * **Device metrics** (Métriques de l’appareil) : nombre d’appareils IoT Edge dans le déploiement indiquant une réussite ou des erreurs de l’exécution du client IoT Edge.
   * **Custom metrics** (Métriques personnalisées) : nombre d’appareils IoT Edge figurant dans les données de rapport de déploiement pour toutes les métriques que vous avez définies dans le cadre du déploiement.
   * **Creation time (Heure de création)** : horodatage de création du déploiement. Cet horodatage sert à départager deux déploiements ayant la même priorité. 
1. Sélectionnez le déploiement que vous souhaitez surveiller.  
1. Inspectez les détails du déploiement. Vous pouvez utiliser les onglets pour passer en revue les détails du déploiement.

## <a name="modify-a-deployment"></a>Modifier un déploiement

Quand vous modifiez un déploiement, les modifications sont répliquées immédiatement sur tous les appareils ciblés. 

Si vous mettez à jour la condition cible, les mises à jour suivantes se produisent :

* Si un appareil ne remplissait pas l’ancienne condition cible, mais qu’il remplit la nouvelle condition cible et que ce déploiement a la priorité la plus élevée pour cet appareil, ce déploiement est appliqué à l’appareil. 
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible, il désinstalle ce déploiement et prend le déploiement suivant dans l’ordre de priorité. 
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible et ne remplit la condition cible d’aucun autre déploiement, aucun changement ne se produit sur l’appareil. L’appareil continue à exécuter ses modules actuels dans leur état actuel, mais il n’est plus géré dans le cadre de ce déploiement. Dès qu’il remplit la condition cible d’un autre déploiement, il désinstalle ce déploiement et prend le nouveau. 

Pour modifier un déploiement, effectuez les étapes suivantes : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub. 
1. Sélectionnez **IoT Edge**.
1. Sélectionnez **Déploiements IoT Edge**. 

   ![Afficher les déploiements IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Sélectionnez le déploiement à modifier. 
1. Mettez à jour les champs suivants : 
   * Condition cible
   * Métriques : vous pouvez modifier ou supprimer des métriques que vous avez définies, ou en ajouter de nouvelles.
   * Étiquettes
   * Priority
1. Sélectionnez **Enregistrer**.
1. Suivez les étapes fournies dans [Surveiller un déploiement](#monitor-a-deployment) pour observer le déploiement des modifications. 

## <a name="delete-a-deployment"></a>Supprimer un déploiement

Quand vous supprimez un déploiement, tous les appareils prennent leur déploiement suivant dans l’ordre de priorité. Si vos appareils ne remplissent la condition cible d’aucun autre déploiement, les modules ne sont pas supprimés lors de la suppression du déploiement. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub. 
1. Sélectionnez **IoT Edge**.
1. Sélectionnez **Déploiements IoT Edge**. 

   ![Afficher les déploiements IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Utilisez la case à cocher pour sélectionner le déploiement à supprimer. 
1. Sélectionnez **Supprimer**.
1. Un message vous informe que cette action va supprimer ce déploiement et restaurer tous les appareils à leur état précédent.  Cela signifie qu’un déploiement avec une priorité inférieure sera utilisé.  Si aucun autre déploiement n’est ciblé, aucun module n’est supprimé. Si vous souhaitez supprimer tous les modules de votre appareil, créez un déploiement sans aucun module et déployez-le sur l’appareil en question. Cliquez sur **Oui** pour continuer. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez le [déploiement des modules sur des appareils IoT Edge](module-deployment-monitoring.md).
