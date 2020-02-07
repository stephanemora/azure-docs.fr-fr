---
title: Azure VMware Solutions (AVS) – Allouer des adresses IP publiques
description: Explique comment allouer des adresses IP publiques à des machines virtuelles dans un environnement de cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024294"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Allouer des adresses IP publiques pour un environnement de cloud privé AVS

Ouvrez l’onglet Adresses IP publiques de la page Réseau pour allouer des adresses IP publiques à des machines virtuelles de votre environnement de cloud privé AVS.

1. [Accédez au portail AVS](access-cloudsimple-portal.md), puis sélectionnez **Réseau** dans le menu latéral.
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
