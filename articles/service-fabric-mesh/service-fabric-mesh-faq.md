---
title: Questions courantes sur Azure Service Fabric mesh | Microsoft Docs
description: Découvrez les questions et réponses fréquentes concernant Azure Service Fabric mesh.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d0ae7fbb22f6d98662f83968158182d447a75394
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501965"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Questions fréquemment posées sur Service Fabric mesh
Azure Service Fabric mesh est un service entièrement géré qui permet aux développeurs de déployer des applications de microservices sans gestion de machines virtuelles, de stockage ou de mise en réseau. Cet article contient des réponses aux questions fréquemment posées.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Comment faire pour signaler un problème ou poser une question ?

Posez des questions, obtenez des réponses d’ingénieurs Microsoft, et signalez des problèmes dans le [dépôt GitHub service-fabric-mesh-preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Quota et coût

**Quel est le coût de participation à la préversion ?**

Le déploiement d’applications ou de conteneurs sur la préversion de Mesh n’occasionne aucuns frais. En revanche, nous vous invitons à supprimer les ressources que vous déployez et à ne pas les laisser s’exécuter si vous ne les testez activement.

**Y a-t-il une limite de quota concernant le nombre de cœurs et la RAM ?**

Oui, les quotas pour chaque abonnement sont les suivants :

- Nombre d’applications : 5 
- Nombre de cœurs par application : 12 
- RAM totale par application : 48 Go 
- Nombre de points de terminaison réseau et d’entrée : 5  
- Nombre de volumes Azure que vous pouvez attacher : 10 
- Nombre de réplicas de service : 3 
- Le plus grand conteneur que vous puissiez déployer est limité à 4 cœurs, 16 Go de RAM.
- Vous pouvez allouer des cœurs partiels à vos conteneurs, par incréments de 0,5 cœurs, jusqu’à au maximum 6 cœurs.

**Puis-je laisser mon application s’exécuter pendant la nuit ?**

Oui, vous le pouvez. En revanche, nous vous invitons à supprimer les ressources que vous déployez et à ne pas les laisser s’exécuter si vous ne les testez activement. Cette stratégie pourrait changer à l’avenir, et les ressources pourraient être supprimées en cas d’utilisation à mauvais escient.

## <a name="supported-container-os-images"></a>Images de système d’exploitation du conteneur prises en charge
Lors du déploiement de services, vous pouvez utiliser les images de système d’exploitation du conteneur suivantes.

- Windows : windowsservercore et nanoserver
    - Windows Server 2016
    - Windows Server version 1709
- Linux
    - Aucune limitation connue

## <a name="features-gaps-and-known-issues"></a>Fonctionnalités manquantes et problèmes connus

**Après avoir déployé mon application, il semble que la ressource réseau qui y est associée n’a pas d’adresse IP**

Il existe actuellement un problème connu lié à l’apparition tardive de l’adresse IP après un certain délai. Vérifiez l’état de la ressource réseau dans quelques minutes pour voir l’adresse IP associée.

**Mon application ne parvient pas à accéder à la ressource réseau/de volume adéquate**

Dans votre modèle d’application, vous devez utiliser l’ID de ressource complet pour que les réseaux et volumes puissent accéder à la ressource associée. Voici à quoi il ressemble dans l’exemple de démarrage rapide :

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Je ne vois pas le modèle d’application actuel prenant en charge le chiffrement de mes secrets**

Oui, le chiffrement des secrets n’est pas pris en charge dans la préversion privée actuelle. 

**DNS ne fonctionne pas de la même façon dans mon cluster de développement Service Fabric et dans Mesh**

Ce problème est connu. Il se peut que vous deviez référencer les services différemment dans votre cluster de développement local et dans Azure Mesh. Dans votre cluster de développement local, utilisez {NomService}.{NomApplication}. Dans Azure Service Fabric mesh, utilisez {NomService}. Azure Mesh ne prend pas en charge actuellement la résolution DNS entre applications.

Pour d’autres problèmes de DNS connus en lien avec l’exécution d’un cluster de développement Service Fabric sur Windows 10, voir [Déboguer les conteneurs Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Lors de l’utilisation du module CLI je reçois l’erreur  ImportError: cannot import name 'sdk_no_wait'**

Si vous utilisez une version de CLI antérieure à la version 2.0.30, il se peut que vous obteniez cette erreur :

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**J’obtiens une erreur d’incompatibilité de nom distribution lorsque j’installe le package d’extension CLI**

Cela ne signifie pas que l’extension n’a pas été installée. Vous devriez toujours être en mesure d’utiliser les commandes de CLI sans aucun problème.

**Lorsque j’augmente la taille des instances, je vois que tous mes conteneurs sont affectés, y compris ceux qui sont en cours d’exécution**

Il s’agit d’un bogue qui devrait être corrigé dans la prochaine actualisation du runtime.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Service Fabric mesh, voir la [Vue d’ensemble](service-fabric-mesh-overview.md).
