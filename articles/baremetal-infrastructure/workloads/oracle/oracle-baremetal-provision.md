---
title: Provisionner BareMetal pour Oracle
description: Découvrez le provisionnement de BareMetal Infrastructure pour Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558595"
---
# <a name="provision-baremetal-for-oracle"></a>Provisionner BareMetal pour Oracle

Dans cet article, nous allons examiner comment provisionner vos instances BareMetal Infrastructure pour les charges de travail Oracle. 

La première étape pour provisionner vos instances BareMetal consiste à collaborer avec votre CSA Microsoft. Il vous fournira une assistance en fonction de vos besoins spécifiques en matière de charge de travail et de l’architecture que vous déployez, qu’il s’agisse d’une instance unique, de One Node RAC ou de RAC. Pour plus d’informations sur ces topologies, consultez [Architecture de BareMetal Infrastructure pour Oracle](oracle-baremetal-architecture.md).

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Un abonnement Azure actif
> * Contrat de support Premier Microsoft
> * Licences pour Red Hat Enterprise Linux 7.6
> * Contrat de support Oracle 
> * Licences et composants d’installation de logiciels pour Oracle
> * ExpressRoute connecté **localement à Azure** (**si vous le souhaitez**, configurez ExpressRoute Global Reach afin de bénéficier d’une connectivité directe de l’emplacement local vers la base de données Oracle)   
> * Réseau virtuel
> * Création de la passerelle
> * Machines virtuelles dans le réseau virtuel (jump boxes)

## <a name="information-to-provide-microsoft-operations"></a>Informations à fournir à Microsoft Operations

Vous devrez fournir les informations suivantes à votre CSA :

1. Espace d’adressage du réseau virtuel. Cette plage doit être un sous-réseau /24 ; par exemple, 10.11.0.0/24.
2. Plage P2P. Cette plage doit être un sous-réseau /29 ; par exemple, 10.12.0.0/29.
3. Pool d’adresses IP du serveur. La plage recommandée est /24 ; par exemple, 10.13.0.0/24.
4. Adresse IP du serveur. Choisissez une adresse IP parmi le pool d’adresses IP du serveur.

    > [!Note] 
    > Les trente premières adresses IP sont réservées à la configuration de l’infrastructure Microsoft. Ainsi, dans cet exemple, la première adresse IP disponible pour une lame serait 10.13.0.30.

5. Région Azure requise, par exemple West US2.
6. Référence SKU de BareMetal Infrastructure requise, par exemple S192 (deux machines).

## <a name="storage-requirements"></a>Exigences de stockage

Collaborez avec votre représentant CSA afin de déterminer vos besoins en matière de stockage pendant la demande de provisionnement, notamment les besoins de stockage attendus en fonction de la croissance future. Le stockage est ajouté par incréments de 1 To.

Pour les volumes, nous respectons la [norme OFA (Optimal Flexible Architecture)](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093) d’Oracle, avec un niveau De base et une configuration Entreprise. Si vous avez des besoins de stockage personnalisés autres que les dimensionnements standard, effectuez votre demande personnalisée par le biais de votre CSA.

| Configuration de base (POC/Test) | Description | Petite | Moyenne | grand |
| --- | --- | --- | --- | --- |
| /u01 | Fichiers binaires Oracle | 500 Go | 500 Go | 500 Go |
| /u02 | Lectures intensives/Administrateur | 500 Go | 1 To | 5 To |
| /u03 | Écritures intensives/Journaux | 500 Go | 1 To | 5 To |
| /u09 | Sauvegarde | 5 To | 10 To | 15 To |

| Configuration de l’entreprise | Description | Petite | Moyenne | grand | Très grande |
| --- | --- | --- | --- | --- | --- |
| /u01 | Fichiers binaires Oracle | 500 Go | 500 Go | 500 Go | 500 Go |
| /u02 | Admin | 100 Go | 100 Go | 100 Go | 100 Go |
| /u10 à /u59 | Lectures intensives | 500 Go | 5 To | 10 To | 20 To |
| /u60 à /u89 | Écritures intensives | 500 Go | 5 To | 10 To | 20 To |
| /u90 à /u91 | Journaux de phase de restauration par progression | 500 Go | 500 Go | 1 To | 1 To |
| /u95 | Archivage | 10 To | 10 To | 20 To | 20 To |
| /u98 | Sauvegarde | 25 To | 25 To | 50 To | 50 To |

## <a name="next-step"></a>Étape suivante

Apprenez-en davantage sur BareMetal Infrastructure pour Oracle.

> [!div class="nextstepaction"]
> [Qu’est-ce que BareMetal Infrastructure sur Azure ?](../../concepts-baremetal-infrastructure-overview.md)
