---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168642"
---
## <a name="overview-of-ssh-and-keys"></a>Vue d’ensemble de SSH et des clés

[SSH](https://www.ssh.com/ssh/) est un protocole de connexion chiffré qui permet d’ouvrir des sessions sécurisées via des connexions non sécurisées. SSH est le protocole de connexion par défaut pour les machines virtuelles Linux hébergées dans Azure. Bien que le protocole SSH lui-même offre une connexion chiffrée, l’utilisation de mots de passe avec des connexions SSH laisse néanmoins la machine virtuelle vulnérable aux attaques en force brute ou au piratage des mots de passe. Une méthode plus sûre et recommandée pour se connecter à une machine virtuelle à l’aide de SSH consiste à utiliser une paire de clés publique et privée, également appelées *clés SSH*. 

* La *clé publique* est placée sur votre machine virtuelle Linux, ou tout autre service que vous souhaitez utiliser avec le chiffrement à clé publique.

* La *clé privée* reste sur votre système local. Protégez cette clé privée. Ne la partagez pas.

Quand vous utilisez un client SSH pour vous connecter à votre machine virtuelle Linux (qui a la clé publique), la machine virtuelle distante teste le client pour vérifier qu’il a la clé privée. Si le client a la clé privée, il est autorisé à accéder à la machine virtuelle. 

Selon les stratégies de sécurité de votre organisation, vous pouvez réutiliser une paire de clés publique-privée unique pour accéder à plusieurs services et machines virtuelles Azure. Vous n’avez pas besoin d’une paire de clés distincte pour chaque machine virtuelle ou service auxquels vous souhaitez accéder. 

Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) devez connaître votre clé privée.