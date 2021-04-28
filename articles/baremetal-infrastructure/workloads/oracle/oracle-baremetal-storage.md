---
title: Stockage sur BareMetal pour les charges de travail Oracle
description: Découvrez le stockage proposé par BareMetal Infrastructure pour les charges de travail Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558593"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Stockage sur BareMetal pour les charges de travail Oracle

Cet article fournit une vue d’ensemble du stockage proposé par BareMetal Infrastructure pour les charges de travail Oracle.

BareMetal Infrastructure pour Oracle offre un stockage NFS (Network File System) NetApp. Le stockage NFS ne nécessite pas de certification Oracle Real Application Clusters (RAC). Pour plus d’informations, consultez la [matrice des technologies Oracle RAC pour les clusters Linux](https://www.oracle.com/database/technologies/tech-generic-linux-new.html).

Cette offre de stockage comprend le support de niveau 3 d’un partenaire OEM, à l’aide de contrôleurs de stockage A700s ou A800s.

Le stockage BareMetal Infrastructure offre les fonctionnalités de stockage Premium suivantes :

- Volumes de stockage pour les données/journaux/quorum/FSA proposés par le biais du protocole dNFS
- Redondance de disque (*protection contre deux défaillances de disque*)
- Scale-out des données sur plusieurs volumes, limité à 100 To par volume
- Scale-out vers plusieurs contrôleurs de stockage, jusqu’à 12 contrôleurs
- Aucune gestion au niveau du disque (*ajouter/supprimer des disques*) ; prise en charge automatique par Infra.
- Aucun temps d’arrêt pour redistribuer le contenu des fichiers vers différents volumes
- Capacité à augmenter/réduire les volumes
- Intégration de SnapCenter pour la sauvegarde à l’aide du clonage et de SnapVault
- Chiffrement des données au repos, avec prise en charge de FIPS (140-2)

## <a name="next-steps"></a>Étapes suivantes

Découvrez les considérations relatives à l’application des correctifs de BareMetal Infrastructure.

> [!div class="nextstepaction"]
> [Considérations relatives à l’application des correctifs pour BareMetal pour Oracle](oracle-baremetal-patching.md)

