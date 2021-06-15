---
title: Configuration Ethernet de BareMetal pour Oracle
description: En savoir plus sur la configuration des interfaces Ethernet sur les instances BareMetal pour les charges de travail Oracle.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 04/14/2021
ms.openlocfilehash: b3183e660b8775e8f3c9137f27220e9db97deec6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578599"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Configuration Ethernet de BareMetal pour Oracle

Dans cet article, nous allons examiner la configuration des interfaces Ethernet sur les instances BareMetal pour les charges de travail Oracle.

Chaque instance BareMetal configurée pour Oracle est préconfigurée avec des ensembles d’interfaces Ethernet. Les interfaces Ethernet sont classées en quatre types :

- Utilisé pour ou par l’accès client.
- Utilisé pour la communication nœud à nœud. Cette interface est configurée sur tous les serveurs, quelle que soit la topologie demandée. Utilisé uniquement pour les scénarios de scale-out.
- Utilisé pour la connectivité de nœud à stockage.
- Utilisé pour la configuration de la récupération d’urgence et la connectivité à la portée mondiale pour la connectivité inter-régions.

## <a name="architecture"></a>Architecture

Le diagramme suivant illustre l’architecture des interfaces Ethernet préconfigurées de BareMetal Infrastructure. 

[![Diagramme montrant l’architecture des interfaces Ethernet préconfigurées pour les charges de travail Oracle.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

La configuration par défaut est fournie avec une interface IP cliente (eth1), qui se connecte à partir de votre réseau virtuel Azure (VNET) et vous permet d’utiliser Secure Shell (SSH) pour accéder à une instance BareMetal.

> [!NOTE]
> Pour une autre interface client (eth10) à partir d’un autre réseau virtuel Azure, contactez votre responsable CSA Microsoft pour envoyer une demande de service. Par exemple, si vous souhaitez des environnements de développement, de test et de production/récupération d’urgence.

| **Interface logique de la carte réseau** | **Nom avec le système d’exploitation RHEL** | **Cas d’usage** |
| --- | --- | --- |
| Un | net1.tenant | Client vers instance BareMetal |
| C | net2.tenant | Nœud à stockage : prend en charge la coordination et l’accès aux contrôleurs de stockage pour la gestion de l’environnement de stockage. |
| B | net3.tenant | Nœud à nœud (interconnexion privée) |
| C | net4.tenant | Réservé/iSCSI |
| C | net5.tenant | Réservé/Sauvegarde des fichiers journaux |
| C | net6.tenant | Nœud à storage/Sauvegarde de données (RMAN, instantané) |
| C | net7.tenant | Nœud à stockage/dNFS-Pri : fournit la connectivité avec le groupe de stockage NetApp. |
| C | net8.tenant | Nœud à stockage/dNFS-Sec : fournit la connectivité avec le groupe de stockage NetApp. |
| D | net9.tenant | Connectivité de la récupération d’urgence pour la configuration de la portée mondiale pour l’accès à BMI dans une autre région. |
| Un | \*net10.tenant | \* Client vers instance BareMetal
 |

Si nécessaire, vous pouvez définir d’autres cartes de contrôleur d’interface réseau (NIC) par vous-même. Toutefois, les configurations des cartes réseau existantes *ne peuvent pas* être modifiées.

## <a name="usage-rules"></a>Règles d’utilisation

Pour les instances BareMetal, la valeur par défaut aura neuf adresses IP affectées sur les quatre cartes réseau logiques. Les règles d’usage suivantes s’appliquent :

- L’adresse IP attribuée à « Ethernet A » doit se trouver hors de la plage d’adresses du pool d’adresses IP du serveur que vous avez envoyée à Microsoft. Cette adresse IP ne doit pas être conservée dans le répertoire /etc/hosts du système d’exploitation.
- « Ethernet B » doit être conservé exclusivement dans le répertoire etc/hosts pour la communication entre les différentes instances. Gérez ces adresses IP dans des configurations Oracle RAC (Real Application Clusters) avec scale-out en tant qu’adresses IP utilisées pour la configuration inter-nœuds.
- L’adresse IP attribuée à « Ethernet C » doit être utilisée pour la communication à NFS. Ce type d’adresse ne doit pas être conservé dans le répertoire etc/hosts.
- Ethernet « D » doit être utilisé exclusivement pour la configuration de la portée mondiale en vue d’accéder aux instances BareMetal dans votre région de récupération d’urgence.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur BareMetal Infrastructure pour l’architecture Oracle.

> [!div class="nextstepaction"]
> [Architecture de BareMetal Infrastructure pour Oracle](oracle-baremetal-architecture.md)
