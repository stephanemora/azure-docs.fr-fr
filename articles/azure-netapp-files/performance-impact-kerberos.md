---
title: Impact de Kerberos sur les performances sur les volumes Azure NetApp Files NFSv4.1 | Microsoft Docs
description: Décrit les options de sécurité disponibles, les vecteurs de performances testés et l’impact de Kerberos sur les performances attendues sur les volumes Azure NetApp Files NFSv4.1.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743284"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Impact de Kerberos sur les performances sur les volumes Azure NetApp Files NFSv4.1

Azure NetApp Files prend en charge le [chiffrement client NFS dans les modes Kerberos](configure-kerberos-encryption.md) (krb5, krb5i et krb5p) avec le chiffrement AES-256. Cet article décrit l’impact de Kerberos sur les performances sur les volumes NFSv4.1. 

## <a name="available-security-options"></a>Options de sécurité disponibles 

Les options de sécurité actuellement disponibles pour les volumes NFSv4.1 sont les suivantes : 

* **sec = sys** utilise des UID et GID UNIX locaux à l’aide d’AUTH_SYS pour authentifier les opérations NFS.
* **sec = krb5** utilise Kerberos V5 au lieu des UID et GID UNIX locaux pour authentifier les utilisateurs.
* **sec=krb5i** utilise Kerberos V5 pour authentifier les utilisateurs et effectuer des vérifications d’intégrité des opérations NFS qui utilisent des sommes de contrôle sécurisées pour prévenir la falsification de données.
* **s = krb5p** utilise Kerberos V5 pour authentifier les utilisateurs et effectuer des vérifications d’intégrité. Il chiffre le trafic NFS pour empêcher l’espionnage du trafic. Cette option est le paramètre le plus sécurisé, mais elle implique également la surcharge de performance la plus importante.

## <a name="performance-vectors-tested"></a>Vecteurs de performances testés

Cette section décrit l’impact des différentes options de `sec=*` sur les performances côté client uniquement.

* L’impact sur les performances a été testé à deux niveaux : faible concurrence (faible charge) et concurrence élevée (limites supérieures d’E/S et débit).  
* Trois types de charges de travail ont été testés :  
    * Lecture/écriture aléatoire de petites opérations (à l’aide de FIO)
    * Lecture/écriture aléatoire de grandes opérations (à l’aide de FIO)
    * Charge de travail importante de métadonnées telle que générée par les applications, par exemple, git

## <a name="expected-performance-impact"></a>Impact des performances attendues 

Il existe deux zones principales : la charge faible et la limite supérieure. Les listes suivantes décrivent l’impact sur les performances de paramètre de sécurité à paramètre de sécurité et de scénario à scénario. Toutes les comparaisons sont effectuées par rapport au paramètre de sécurité `sec=sys`. Le test a été effectué sur un seul volume, à l’aide d’un seul client. 

Impact de krb5 sur les performances :

* Concurrence faible (R/W) :
    * La latence séquentielle a augmenté de 0,3 ms.
    * La latence d’E/S aléatoire a augmenté de 0,2 ms.
    * La latence d’E/S de métadonnées a augmenté de 0,2 ms.
* Concurrence élevée (R/W) : 
    * Le débit séquentiel maximal n’a pas été affecté par krb5.
    * Le taux d’E/S maximal aléatoire a baissé de 30 % pour les charges de travail en lecture seule avec l’impact global dégringolant à zéro lorsque la charge de travail passe en écriture seule. 
    * La charge de travail de métadonnées maximale a baissé de 30 %.

Impact de krb5 sur les performances : 

* Concurrence faible (R/W) :
    * La latence séquentielle a augmenté de 0,5 ms.
    * La latence d’E/S aléatoire a augmenté de 0,2 ms.
    * La latence d’E/S de métadonnées a augmenté de 0,2 ms.
* Concurrence élevée (R/W) : 
    * Débit séquentiel maximal réduit de 70 % en général, quel que soit le mélange de la charge de travail.
    * Le taux d’E/S maximal aléatoire a baissé de 50 % pour les charges de travail en lecture seule avec l’impact global diminuant de 25 % lorsque la charge de travail passe en écriture seule. 
    * La charge de travail de métadonnées maximale a baissé de 30 %.

Impact de krb5 sur les performances :

* Concurrence faible (R/W) :
    * La latence séquentielle a augmenté de 0,8 ms.
    * La latence d’E/S aléatoire a augmenté de 0,2 ms.
    * La latence d’E/S de métadonnées a augmenté de 0,2 ms.
* Concurrence élevée (R/W) : 
    * Débit séquentiel maximal réduit de 85 % en général, quel que soit le mélange de la charge de travail. 
    * Le taux d’E/S maximal aléatoire a baissé de 65 % pour les charges de travail en lecture seule avec l’impact global diminuant de 43 % lorsque la charge de travail passe en écriture seule. 
    * La charge de travail de métadonnées maximale a baissé de 30 %.

## <a name="next-steps"></a>Étapes suivantes  

* [Configurer le chiffrement Kerberos NFSv4.1 pour Azure NetApp Files](configure-kerberos-encryption.md) 
