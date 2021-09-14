---
title: Nouveautés de l’agent des serveurs avec Azure Arc
description: Cet article contient des notes de publication pour l’agent des serveurs avec Azure Arc. Pour la plupart des problèmes résumés, des liens mènent à des informations supplémentaires.
ms.topic: overview
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: d4008a41629ac2e71e1abdb91e30f2d6b9350538
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431601"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Nouveautés de l’agent des serveurs avec Azure Arc

L’agent Connected Machine des serveurs avec Azure Arc reçoit des améliorations de manière continue. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[archive des nouveautés de l’agent des serveurs avec Azure Arc](agent-release-notes-archive.md).

## <a name="august-2021"></a>Août 2021

Version 1.10

### <a name="fixed"></a>Fixe

- L’agent de stratégie Guest Configuration peut désormais configurer et corriger les paramètres système. Les attributions de stratégies existantes sont toujours en mode Auditer uniquement. Découvrez-en plus sur les [options de correction de Guest Configuration](../../governance/policy/concepts/guest-configuration-policy-effects.md) d’Azure Policy.
- L’agent de stratégie Guest Configuration redémarre à présent toutes les 48 heures et non plus toutes les 6 heures.

## <a name="july-2021"></a>Juillet 2021

Version 1.9

## <a name="new-features"></a>Nouvelles fonctionnalités

Prise en charge de l’indonésien

### <a name="fixed"></a>Fixe

Correction d’un bogue empêchant la gestion des extensions dans la région USA Ouest 3

Version 1.8

### <a name="new-features"></a>Nouvelles fonctionnalités

- Amélioration de la fiabilité lors de l’installation de l’extension de l’agent Azure Monitor sur les systèmes Red Hat et CentOS
- Ajout de la mise en œuvre côté agent de la longueur maximale du nom de la ressource (54 caractères)
- Améliorations de la stratégie de configuration d’invité (Guest Configuration) :
  - Ajout de la prise en charge des stratégies de configuration d’invité basées sur PowerShell sur les systèmes d’exploitation Linux
  - Ajout de la prise en charge de plusieurs attributions de la même stratégie de configuration d’invité sur le même serveur
  - Mise à niveau de PowerShell Core vers la version 7.1 sur les systèmes d’exploitation Windows

### <a name="fixed"></a>Fixe

- L’agent continue de s’exécuter s’il ne parvient pas à écrire les événements de démarrage/d’arrêt du service dans le journal des événements de l’application Windows

## <a name="june-2021"></a>Juin 2021

Version 1.7

### <a name="new-features"></a>Nouvelles fonctionnalités

- Amélioration de la fiabilité lors de l’intégration :
  - Amélioration de la logique de nouvelle tentative quand HIMDS n’est pas disponible
  - Désormais, l’intégration se poursuit au lieu de s’arrêter si les informations du système d’exploitation ne peuvent pas être obtenues
- Amélioration de la fiabilité lors de l’installation de l’extension de l’agent OMS sur les systèmes Red Hat et CentOS

## <a name="may-2021"></a>Mai 2021

Version 1.6

### <a name="new-features"></a>Nouvelles fonctionnalités

- Ajout de la prise en charge de SUSE Enterprise Linux 12
- Mise à jour de l’agent de configuration invité vers la version 1.26.12.0 pour inclure les éléments suivants :

   - Les stratégies sont exécutées dans un processus distinct.
   - Ajout de la prise en charge de signature V2 pour la validation de l’extension.
   - Mise à jour mineure de la journalisation des données.

## <a name="april-2021"></a>Avril 2021

Version 1.5

### <a name="new-features"></a>Nouvelles fonctionnalités

- Ajout de la prise en charge de Red Hat Enterprise Linux 8 et CentOS Linux 8.
- Nouveau paramètre `-useStderr` pour diriger l’erreur et la sortie détaillée vers stderr.
- Nouveau paramètre `-json` pour diriger les résultats de sortie au format JSON (en cas d’utilisation avec -useStderr).
- Collecter d’autres métadonnées d’instance : fabricant, modèle et ID de la ressource de cluster (pour les nœuds Azure Stack HCI).
 
## <a name="next-steps"></a>Étapes suivantes

- Avant d’évaluer ou d’activer des serveurs avec Azure Arc sur plusieurs machines hybrides, consultez [Vue d’ensemble de l’agent Machine connectée](agent-overview.md) pour en savoir plus sur les exigences et les détails techniques relatifs à l’agent, ainsi que sur les méthodes de déploiement.

- Consultez le [Guide de planification et de déploiement](plan-at-scale-deployment.md) pour planifier le déploiement de serveurs avec Azure Arc à n’importe quelle échelle et implémenter la gestion et la supervision centralisées.
