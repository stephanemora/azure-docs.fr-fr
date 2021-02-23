---
title: Concepts - Identité et accès
description: Découvrir les concepts d’identité et d’accès de Azure VMware Solution
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 68f4ce9136cca1cf9bf0824395e31704d8ed1a17
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364883"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concepts d’identité d’Azure VMware Solution

Les clouds privés Azure VMware Solution sont approvisionnés avec un serveur VMware vCenter et NSX-T Manager. Vous utilisez vCenter pour gérer les charges de travail des machines virtuelles. Vous utilisez NSX-T Manager pour étendre le cloud privé.

La gestion des accès et des identités utilise des privilèges de groupe CloudAdmin pour vCenter et des droits d’administrateur restreints pour NSX-T Manager. Il garantit que votre plateforme de cloud privé est mise à niveau automatiquement avec les fonctionnalités et correctifs les plus récents.  Pour plus d’informations, consultez l’[article sur les concepts relatifs aux mises à niveau de cloud privé][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>Identité et accès vCenter

Le groupe CloudAdmin fournit les privilèges dans vCenter. Vous gérez le groupe localement dans vCenter. Une autre option consiste à utiliser l’intégration de l’authentification unique LDAP vCenter avec Azure Active Directory. Vous activez cette intégration après avoir déployé votre cloud privé. 

Le tableau suivant répertorie les privilèges **CloudAdmin** et **CloudGlobalAdmin**.

|  Ensemble de privilèges           | CloudAdmin | CloudGlobalAdmin | Commentaire |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmes                  | Un utilisateur CloudAdmin dispose de tous les privilèges d’alarmes pour les alarmes du pool Compute-ResourcePool et des machines virtuelles.     |          --        |  -- |
|  Déploiement automatique             |  --  |        --        |  Microsoft effectue la gestion des hôtes.  |
|  Certificats            |  --  |        --       |  Microsoft effectue la gestion des certificats.  |
|  Bibliothèque de contenu         | Un utilisateur CloudAdmin dispose des privilèges nécessaires pour créer et utiliser des fichiers dans une bibliothèque de contenu.    |         Activé avec SSO.         |  Microsoft distribue les fichiers de la bibliothèque de contenu aux hôtes ESXi.  |
|  Centre de données              |  --  |        --          |  Microsoft effectue toutes les opérations du centre de données.  |
|  Magasin de données               | Datastore.AllocateSpace, Datastore.Browse, Datastore.Config, Datastore.DeleteFile, Datastore.FileManagement, Datastore.UpdateVirtualMachineMetadata     |    --    |   -- |
|  Agent Manager ESX       |  --  |         --       |  Microsoft effectue toutes les opérations.  |
|  Dossier                  |  Un utilisateur CloudAdmin dispose de tous les privilèges de dossier.     |  --  |  --  |
|  Global                  |  Global.CancelTask, Global.GlobalTag, Global.Health, Global.LogEvent, Global.ManageCustomFields, Global.ServiceManagers, Global.SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host.Hbr.HbrManagement      |        --          |  Microsoft effectue toutes les autres opérations d’hôte.  |
|  InventoryService        |  InventoryService.Tagging      |        --          |  --  |
|  Réseau                 |  Network.Assign    |                  |  Microsoft effectue toutes les autres opérations de réseau.  |
|  Autorisations             |  --  |        --       |  Microsoft effectue toutes les opérations d’autorisation.  |
|  Stockage basé sur les profils  |  --  |        --       |  Microsoft effectue toutes les opérations de profil.  |
|  Ressource                |  Un utilisateur CloudAdmin dispose de tous les privilèges de ressource.        |      --       | --   |
|  Tâche planifiée          |  Un utilisateur CloudAdmin dispose de tous les privilèges de ScheduleTask.   |   --   | -- |
|  Sessions                |  Sessions.GlobalMessage, Sessions.ValidateSession      |   --   |  Microsoft effectue toutes les autres opérations de session.  |
|  Affichage de stockage           |  StorageViews.View   |        --          |  Microsoft effectue toutes les autres opérations d’affichage de stockage (Configurer le service).  |
|  Tâches                   |  --  |  --   |  Microsoft administre les extensions qui gèrent les tâches.  |
|  vApp                    |  Un utilisateur CloudAdmin dispose de tous les privilèges de vApp.  |  --  |  --  |
|  Machine virtuelle         |  Un utilisateur CloudAdmin dispose de tous les privilèges de VirtualMachine.  |  --  |  --  |
|  vService                |  Un utilisateur CloudAdmin dispose de tous les privilèges de vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Identité et accès du gestionnaire NSX-T

Utilisez le compte *administrateur* pour accéder à NSX-T Manager. Ce compte dispose de privilèges complets et vous permet de créer et de gérer des passerelles de niveau 1 (T1), des segments (commutateurs logiques) et tous les services. Les privilèges vous permettent d’accéder à la passerelle de niveau 0 (T0) de NSX-T. Une modification apportée à la passerelle T0 peut entraîner une dégradation des performances réseau ou une perte de l’accès à un cloud privé. Ouvrez une demande de support dans le portail Azure pour demander toute modification de votre passerelle NSX-T T0.
  
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les concepts d’accès et d’identité d’Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Concepts de mise à niveau du cloud privé](concepts-upgrades.md).
- [Contrôle d’accès en fonction du rôle vSphere pour Azure VMware Solution](concepts-role-based-access-control.md).
- [Comment activer la ressource Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md