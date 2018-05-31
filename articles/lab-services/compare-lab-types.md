---
title: Comparer différents types de laboratoires dans Azure Lab Services | Microsoft Docs
description: Explique et compare les différents types de laboratoires qu’Azure Lab Services (anciennement Dev/Test Labs) permet de créer.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 22a1c90dd1a1ca305431d91a801e5293a6d08703
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361180"
---
# <a name="compare-managed-and-devtest-labs-in-azure-lab-services"></a>Comparer les laboratoires gérés et DevTest dans Azure Lab Services
Il existe deux types de laboratoires, les laboratoires **gérés** avec Azure Lab Services, et les **laboratoires personnalisés** avec Azure Dev/Test Labs. Si vous souhaitez simplement ajouter ce dont vous avez besoin à un laboratoire et laisser le service configurer et gérer l’infrastructure requise, choisissez un **laboratoire géré**. Actuellement, le **laboratoire de classe** est le seul type de laboratoire géré qu’Azure Lab Services permet de créer. Si vous souhaitez gérer votre propre infrastructure, créez un **laboratoire personnalisé** avec Azure Dev/Test Labs.

Les sections suivantes décrivent plus en détail ces laboratoires. 

## <a name="managed-labs"></a>Laboratoires gérés
Les laboratoires gérés se déclinent en différents types adaptés à différents besoins. Actuellement, Azure Lab Services prend uniquement en charge les **laboratoires de classe** dans cette catégorie. Les laboratoires gérés permettent de commencer sans délai, avec une procédure d’installation minimale. Le service gère lui-même toute l’infrastructure du laboratoire, du lancement des machines virtuelles à la gestion des erreurs, en passant par la mise à l’échelle de cette infrastructure. Pour créer un laboratoire géré, commencez par créer un compte Lab pour votre organisation. Il sert de compte central, dans lequel sont gérés tous les laboratoires de l’entreprise. 

Lorsque vous créez et utilisez des ressources Azure dans ces laboratoires gérés, le service crée et gère les ressources dans des abonnements Microsoft internes, et non dans votre propre abonnement Azure. Le service effectue le suivi de l’utilisation de ces ressources dans des abonnements Microsoft internes, utilisation qui est refacturée à votre abonnement Azure associé au compte Lab.   

Voici quelques **cas d’usage des laboratoires gérés** : 

- Proposer aux étudiants un laboratoire de machines virtuelles configurées précisément pour répondre aux besoins de la classe ; Donner à chacun un temps limité d’utilisation des machines virtuelles pour le travail à la maison ou les projets personnels.
- Configurer un pool de machines virtuelles offrant des performances élevées en calcul pour effectuer des recherches nécessitant beaucoup de ressources système ou de graphismes. Exécutez les machines virtuelles suivant vos besoins, et nettoyez-les une fois que vous avez terminé. 
- Déplacer vers le cloud l’ordinateur physique qui servait de laboratoire à votre établissement. Redimensionner automatiquement et exclusivement le nombre de machines virtuelles à la limite maximale d’utilisation et au seuil de coûts définis sur le laboratoire.  
- Configurer rapidement un laboratoire de machines virtuelles pour héberger un hackathon. Supprimez le laboratoire d’un simple clic une fois que vous avez terminé. 


## <a name="devtest-labs"></a>Laboratoires DevTest
Certains cas de figure impliquent de gérer soi-même toute l’infrastructure et toute la configuration dans son propre abonnement. Pour cela, vous pouvez créer un laboratoire personnalisé avec Azure Dev/Test Labs sur le Portail Azure. Il n’est pas nécessaire dans ce cas de créer un compte Lab. Ces laboratoires ne s’affichent pas dans le compte Lab (qui sert aux laboratoires gérés).  

Voici quelques **cas d’usage des laboratoires DevTest** : 

- Configurer rapidement un laboratoire de machines virtuelles pour héberger un hackathon ou une session pratique lors d’une conférence. Supprimez le laboratoire d’un simple clic une fois que vous avez terminé. 
- Créer un pool de machines virtuelles configurées avec une application et offrir à l’équipe un accès à une machine virtuelle pour le dépistage des bogues.  
- Fournir aux développeurs des machines virtuelles configurées avec tous les outils nécessaires. Planifiez le démarrage et l’arrêt automatiques pour réduire les coûts. 
- Créer à plusieurs reprises un laboratoire de machines de test dans le cadre d’un déploiement. Effectuez les derniers tests et nettoyez les machines de test une fois que vous avez terminé. 
- Configurer différemment des machines virtuelles et plusieurs agents de test à des fins de tests de performances et de mise à l’échelle. 
- Proposer des sessions de formation aux clients à l’aide d’un laboratoire configuré avec la dernière version du produit. Donnez à chaque client un temps limité d’utilisation dans le laboratoire. 


## <a name="managed-labs-vs-devtest-labs"></a>Comparaison des laboratoires gérés et des Laboratoires DevTest
Le tableau suivant compare les deux types de laboratoires pris en charge par Azure Lab Services : 

| Caractéristiques | Laboratoires gérés | Laboratoires DevTest |
| -------- | ----------------  | ---------- |
| Gestion de l’infrastructure Azure dans le laboratoire |  Gérée automatiquement par le service. | Gérée par vos soins.  |
| Résilience intégrée en cas de problèmes d’infrastructure | Gérée automatiquement par le service. | Gérée par vos soins.  |
| Gestion des abonnements | Le service gère l’allocation des ressources au sein des abonnements Microsoft sur lesquels il repose. La mise à l’échelle est gérée automatiquement par le service. | Gérée par vos soins dans votre propre abonnement Azure. Aucune mise à l’échelle automatique des abonnements. |
| Déploiement Azure Resource Manager au sein du laboratoire | Non disponible | Disponible |

## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire à l’aide d’Azure Lab Services :

- [Configurer un laboratoire de classe](tutorial-setup-classroom-lab.md)
- [Configurer un laboratoire personnalisé](tutorial-create-custom-lab.md)
