---
title: Concepts - Identité et accès
description: Découvrir les concepts d’identité et d’accès de Azure VMware Solution
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9d5b7b4c7c2e0d55cffc99a3f371494f40320a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88750593"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concepts d’identité d’Azure VMware Solution

Un serveur vCenter et un gestionnaire NSX-T sont provisionnés au cours du déploiement d’un cloud privé. Vous utilisez vCenter pour gérer les charges de travail des machines virtuelles, et le gestionnaire NSX-T pour étendre le réseau à définition logicielle du cloud privé.

La gestion des accès et des identités utilise des privilèges de groupe CloudAdmin pour vCenter, et des droits d’administrateur restreints pour le gestionnaire NSX-T. Cette stratégie vous assure que votre plateforme de cloud privé peut être mise à niveau automatiquement. Les fonctionnalités et correctifs les plus récents sont fournis régulièrement. Pour plus d’informations sur les mises à niveau de cloud privé, consultez [l’article sur les concepts des mises à niveau des clouds privés][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>Identité et accès vCenter

Les privilèges dans vCenter sont fournis par l’intermédiaire du groupe CloudAdmin. Ce groupe peut être géré localement dans vCenter, ou par le biais de l’intégration de l’authentification unique LDAP vCenter à Azure Active Directory. Vous êtes en mesure d’activer cette intégration après avoir déployé un cloud privé.

Les privilèges CloudAdmin et CloudGlobalAdmin sont présentés dans le tableau ci-dessous.

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

Vous accédez au gestionnaire NSX-T en utilisant le compte « administrateur ». Ce compte dispose de privilèges complets ; il vous permet de créer et de gérer des routeurs T1, des commutateurs logiques et tous les services. Les privilèges complets dans NSX-T vous permettent également d’accéder au routeur NSX-T T0. Une modification apportée au routeur T0 peut entraîner une dégradation des performances réseau ou une perte de l’accès à un cloud privé. Afin de vous conformer aux conditions de prise en charge, vous devez ouvrir une demande de support dans le portail Azure pour demander que soient apportées des modifications à votre routeur NSX-T T0.
  
## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à découvrir les [concepts des mises à niveau des clouds privés][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md