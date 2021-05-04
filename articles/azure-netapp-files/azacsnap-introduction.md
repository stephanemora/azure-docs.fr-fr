---
title: Présentation de l’outil Azure Application Consistent Snapshot pour Azure NetApp Files | Microsoft Docs
description: Fournit une présentation de l’outil Azure Application Consistent Snapshot avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 1cd22ad59da4961644c4e4d05150ba8565bf47a0
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929756"
---
# <a name="what-is-azure-application-consistent-snapshot-tool"></a>Qu’est-ce que l’outil Azure Application Consistent Snapshot Tool ?

L’outil Captures instantanées cohérentes Azure Application (AzAcSnap) est un outil de ligne de commande qui offre la protection des données pour les bases de données tierces et gérant toute l’orchestration requise pour les placer dans un état cohérent de l’application avant de prendre une capture instantanée et les renvoyées ensuite à un état opérationnel.

## <a name="supported-platforms-and-os"></a>Plateformes et systèmes d’exploitation pris en charge

- **Bases de données**
  - SAP HANA (reportez-vous à la [matrice de prise en charge](azacsnap-get-started.md#snapshot-support-matrix-from-sap) pour plus d’informations)

- **Systèmes d’exploitation**
  - SUSE Linux Enterprise Server 12+
  - Red Hat Enterprise Linux 7+

## <a name="benefits-of-using-azacsnap"></a>Avantages de l’utilisation d’AzAcSnap

AzAcSnap tire parti des fonctionnalités d’instantané de volume et de réplication dans Azure NetApp Files et les grandes instances Azure.  Elle vous permet de bénéficier des avantages suivants :

- **Protection des données cohérente avec l’application** : AzAcSnap est une solution centralisée pour la sauvegarde des fichiers de base de données critiques. L’outil garantit la cohérence de la base de données avant d’effectuer un instantané du volume de stockage. Par conséquent, il garantit que l’instantané du volume de stockage peut être utilisé pour la récupération de la base de données.
- **Gestion du catalogue de bases de données** : lorsque vous utilisez AzAcSnap avec une base de données dotée d’un catalogue de sauvegarde intégré, les enregistrements au sein du catalogue sont maintenus à jour avec les instantanés de stockage.  Cette fonctionnalité permet à un administrateur de base de données de voir l’activité de sauvegarde.
- **Protection de volume ad hoc** : cette fonctionnalité est utile pour les volumes autres que des bases de données qui n’ont pas besoin de mettre au repos l’application avant de prendre un instantané du stockage.  Les volumes de sauvegarde de journal SAP HANA ou les volumes SAPTRANS en sont des exemples.
- **Clonage des volumes de stockage** : cette fonctionnalité fournit des clones de volume de stockage économes en espace à des fins de développement et de test.
- **Prise en charge de la récupération d’urgence** : AzAcSnap utilise la réplication du volume de stockage pour fournir des options de récupération des instantanés cohérents par rapport à l’application répliqués sur un site distant.

AzAcSnap est un binaire unique.  Il n’a pas besoin d’agents ni de plug-ins supplémentaires pour interagir avec la base de données ou le stockage (Azure NetApp Files via Azure Resource Manager et Grande instance Azure via SSH).  AzAcSnap doit être installé sur un système qui dispose d’une connectivité à la base de données et au stockage.  Toutefois, la flexibilité de l’installation et de la configuration permet d’utiliser une seule installation centralisée ou une installation entièrement distribuée avec des copies installées sur chaque installation de base de données.

## <a name="architecture-overview"></a>Vue d’ensemble de l’architecture

AzAcSnap peut être installé sur le même hôte que la base de données (SAP HANA) ou sur un système centralisé.  Toutefois, il doit exister une connectivité réseau aux serveurs de base de données et au stockage principal (Azure Resource Manager pour Azure NetApp Files ou SSH pour Grande instance Azure).

AzAcSnap est une application légère qui est généralement exécutée à partir d’un planificateur externe.  Sur la plupart des systèmes Linux, cette opération est nommée `cron` et la documentation se concentrera dessus.  Toutefois, le planificateur peut être un outil alternatif, à condition qu’il puisse importer le profil d’interpréteur de commandes de l’utilisateur `azacsnap`.  L’importation des paramètres d’environnement de l’utilisateur garantit que les chemins d’accès et les autorisations de fichiers sont correctement initialisés.

## <a name="command-synopsis"></a>Synopsis de commande

Le format général de la commande est le suivant : `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]`.

## <a name="command-options"></a>Options de commande

Les options de commande sont les suivantes avec les commandes en tant que puces principales et les sous-commandes associées en tant que puces secondaires :

- **`-h`** fournit une aide étendue à la ligne de commande avec des exemples sur l’utilisation de AzAcSnap.
- La commande **`-c configure`** fournit une interface interactive de type questions/réponses pour créer ou modifier le fichier de configuration `azacsnap` (valeur par défaut = `azacsnap.json`).
  - **`--configuration new`** crée un fichier config.
  - **`--configuration edit`** modifie un fichier config existant.
  - Reportez-vous à la référence sur la [commande de configuration](azacsnap-cmd-ref-configure.md).
- **`-c test`** valide le fichier de configuration et teste la connectivité.
  - **`--test hana`** teste la connexion à l’instance SAP HANA.
  - **`--test storage`** teste la communication avec l’interface de stockage sous-jacente en créant un instantané de stockage temporaire sur tous les volumes `data` configurés, puis en les supprimant.
  - **`--test all`** effectuera à la fois les tests `hana` et `storage` dans cet ordre.
  - Reportez-vous à la [référence sur la commande de test](azacsnap-cmd-ref-test.md).
- **`-c backup`** est la commande principale permettant d’exécuter des instantanés de stockage cohérents avec la base de données pour des volumes de données (volumes de données SAP HANA) et d’autres volumes (par exemple, des sauvegardes partagées, des sauvegardes de journaux ou des volumes de démarrage).
  - **`--volume data`** pour effectuer un instantané de tous les volumes dans la strophe `dataVolume` du fichier de configuration.
  - **`--volume other`** pour effectuer un instantané de tous les volumes dans la strophe `otherVolume` du fichier de configuration.
  - Reportez-vous à la [référence sur la commande de sauvegarde](azacsnap-cmd-ref-backup.md).
- **`-c details`** fournit des informations sur les instantanés ou la réplication.
  - **`--details snapshots`** fournit une liste de détails de base sur les instantanés de chaque volume qui a été configuré.
  - **`--details replication`** fournit des informations de base sur l’état de la réplication depuis le site de production vers le site de récupération d’urgence.
  - Reportez-vous à la [référence sur la commande d’obtention des informations](azacsnap-cmd-ref-details.md).
- La commande **`-c delete`** supprime un instantané de stockage ou un ensemble d’instantanés. Vous pouvez utiliser l’ID de sauvegarde SAP HANA figurant dans HANA Studio ou le nom de l’instantané de stockage. L’ID de sauvegarde est uniquement lié aux instantanés `hana`, créés pour les volumes de données et les volumes partagés. Dans le cas contraire, si le nom de l’instantané est entré, il cherche tous les instantanés correspondant à ce nom.
  - Consultez la référence sur [la commande de suppression](azacsnap-cmd-ref-delete.md).
- **`-c restore`** fournit deux méthodes pour restaurer un instantané sur un volume, soit en créant un volume basé sur l’instantané, soit en restaurant un volume dans un état précédent.
  - **`--restore snaptovol`** crée un volume basé sur le dernier instantané sur le volume cible.
  - **`-c restore --restore revertvolume`** rétablit l’état antérieur du volume cible en fonction de l’instantané le plus récent.
  - Reportez-vous à la [référence sur la commande de restauration](azacsnap-cmd-ref-restore.md).
- Le paramètre de ligne de commande facultatif **`[--configfile <configfilename>]`** permet de fournir un nom de fichier de configuration JSON différent.  Cela s’avère particulièrement utile pour créer un fichier de configuration distinct par SID (par exemple `--configfile H80.json`).

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec l’outil Azure Application Consistent Snapshot](azacsnap-get-started.md)
