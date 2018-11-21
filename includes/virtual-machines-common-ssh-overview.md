---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506310"
---
## <a name="overview-of-ssh-and-keys"></a>Vue d’ensemble de SSH et des clés

SSH est un protocole de connexion chiffré qui permet d’ouvrir des sessions en toute sécurité à travers des connexions non sécurisées. SSH est le protocole de connexion par défaut pour les machines virtuelles Linux hébergées dans Azure. Bien que le protocole SSH lui-même offre une connexion chiffrée, l’utilisation de mots de passe avec des connexions SSH laisse néanmoins la machine virtuelle vulnérable aux attaques en force brute ou au piratage des mots de passe. Une méthode plus sûre et recommandée pour se connecter à une machine virtuelle à l’aide de SSH consiste à utiliser une paire de clés publique et privée, également appelées *clés SSH*. 

* La *clé publique* est placée sur votre machine virtuelle Linux, ou tout autre service que vous souhaitez utiliser avec le chiffrement à clé publique.

* Le *clé privée* de votre système local est utilisée par un client SSH pour vérifier votre identité lorsque vous vous connectez à votre machine virtuelle Linux. Protégez cette clé privée. Ne la partagez pas.

Selon les stratégies de sécurité de votre organisation, vous pouvez réutiliser une paire de clés publique-privée unique pour accéder à plusieurs services et machines virtuelles Azure. Vous n’avez pas besoin d’une paire de clés distincte pour chaque machine virtuelle ou service auxquels vous souhaitez accéder. 

Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) devez connaître votre clé privée.