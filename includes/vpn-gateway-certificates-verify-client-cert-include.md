---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320023"
---
Si vous rencontrez des problèmes pour vous connecter, effectuez les vérifications suivantes :

- Si vous avez exporté un certificat client avec l'**Assistant Exportation de certificat**, vérifiez que vous l’avez exporté au format .pfx et sélectionné **Inclure tous les certificats dans le chemin d’accès de certification, si possible**. Lorsque vous l'exportez avec cette valeur, les informations de certificat racine sont également exportées. Après avoir installé le certificat sur l'ordinateur client, le certificat racine situé dans le fichier .pfx est également installé. Pour vérifier que le certificat racine est installé, ouvrez **Gérer les certificats utilisateur** et sélectionnez **Autorités de certification racines de confiance\Certificats**. Vérifiez que le certificat racine y est répertorié car il doit être présent pour permettre à l'authentification d'aboutir.

- Si vous avez utilisé un certificat qui émis par une autorité de certification d’entreprise et rencontrez des problèmes d’authentification, examinez l’ordre de l’authentification sur le certificat client. Vérifiez l’ordre de la liste d’authentification en double-cliquant sur le certificat client et en sélectionnant l'onglet **Détail**, puis **Utilisation améliorée de la clé**. Assurez-vous que le premier élément de la liste correspond à *Authentification client*. Si ce n’est pas le cas, émettez un certificat client basé sur le modèle Utilisateur disposant de l’*authentification client* comme premier élément dans la liste.

- Pour plus d’informations sur la résolution des problèmes liés à P2S, consultez [Résoudre les problèmes de connexions liés à P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
