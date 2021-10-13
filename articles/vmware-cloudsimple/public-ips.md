---
title: Azure VMware Solution by CloudSimple - Allouer des adresses IP publiques
description: Explique comment allouer des adresses IP publiques à des machines virtuelles dans un environnement de cloud privé
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 121baac8c8a5e881c63f16376f947fce315bd238
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616148"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Allouer des adresses IP publiques pour un environnement de cloud privé

Ouvrez l’onglet Public IPs de la page Network (Réseau) pour allouer des adresses IP publiques aux machines virtuelles de votre environnement de cloud privé.

1. [Accédez au portail CloudSimple](access-cloudsimple-portal.md), puis sélectionnez **Network** dans le menu latéral.
2. Sélectionnez **Public IPs**.
3. Cliquez sur **New Public IP**.

    ![Page d’IP publiques](media/public-ips-page.png)

4. Entrez un nom pour identifier l’entrée de l’adresse IP.
5. Conservez l’emplacement par défaut.
6. Utilisez le curseur pour changer le délai d’inactivité, si nécessaire.
7. Entrez l'adresse IP locale à laquelle vous voulez attribuer une adresse IP publique.
8. Entrez un nom DNS associé.
9. Cliquez sur **Envoyer**.

![Allouer des adresses IP publiques](media/network-public-ip-allocate.png)

La tâche d'attribution de l'adresse IP publique commence. Vous pouvez vérifier l'état de la tâche sur la page **Activity > Tasks** (Activité > Tâches). Une fois l'attribution terminée, la nouvelle entrée s’affiche sur la page des adresses IP publiques.
