---
title: Prise en main TmaxSoft OpenFrame sur des Machines virtuelles Azure
description: Ré-héberger votre IBM z/OS mainframe les charges de travail à l’aide de TmaxSoft OpenFrame environnement sur Machines virtuelles (VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896343"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Prise en main TmaxSoft OpenFrame sur Azure

Prendre de vos ressources de macroordinateur existantes et déplacez-les dans Microsoft Azure à l’aide TmaxSoft OpenFrame. Cette solution réhébergement populaires crée un environnement d’émulation sur Azure, ce qui vous permet de migrer rapidement des applications. Aucune remise en forme n’est nécessaire.

## <a name="openframe-rehosting-environment"></a>Environnement de réhébergement OpenFrame

Configurer un environnement de OpenFrame sur Azure pour le développement, de démonstrations, de test ou de charges de travail de production. Comme le montre l’illustration suivante, OpenFrame comprend plusieurs composants de l’environnement d’émulation mainframe sur Azure. Par exemple, services en ligne OpenFrame remplacement le macroordinateur intergiciel (middleware) tels que IBM CICS Customer Information contrôle système (). OpenFrame Batch, avec son composant TJES, remplace travail entrée sous-système (JES du macroordinateur IBM). 

![Processus de réhébergement OpenFrame](media/openframe-01.png)

> [!NOTE]
> Pour exécuter l’environnement OpenFrame sur Azure, vous devez disposer d’une licence de produit valide ou d’une licence d’essai à partir de TmaxSoft.

## <a name="openframe-components"></a>Composants OpenFrame

Les composants suivants font partie de l’environnement OpenFrame sur Azure :

- **Outils de migration** notamment OFMiner, une solution qui analyse les ressources de macroordinateurs et avant de les migre vers Azure.
- **Compilateurs**, y compris OFCOBOL, un compilateur qui interprète les programmes de COBOL du macroordinateur. OFPLI, qui interprète PL du macroordinateur / je programmes ; et OFASM, un compilateur qui interprète les programmes d’assembleur du macroordinateur.
- **Front-end** composants, y compris la Solution d’utilisateur de Java Enterprise (JEUS), un serveur d’applications web qui est certifié pour Java Enterprise Edition 6.OFGW et le composant passerelle OpenFrame qui fournit un écouteur 3270.
- **Application** environnement. Base de OpenFrame est l’intergiciel (middleware) qui gère l’ensemble du système. OpenFrame Server Type C (OSC) remplace le macroordinateur intergiciel (middleware) et IBM CICS.
- **Base de données relationnelle**, telles que Tibero (indiqué), base de données Oracle, Microsoft SQL Server, IBM Db2 ou MySQL. Les applications OpenFrame utilisent protocole de la connectivité ODBC (Open Database) pour communiquer avec la base de données.
- **Sécurité** via TACF, un module de service qui contrôle l’accès utilisateur aux systèmes et aux ressources. 
- **OFManager** est une solution qui fournit des fonctions de gestion et d’opération du OpenFrame dans l’environnement web.

![Architecture de OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Étapes suivantes

- [Installer TmaxSoft OpenFrame sur Azure](./install-openframe-azure.md)
