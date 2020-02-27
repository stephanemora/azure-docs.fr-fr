---
title: Durcissement de la sécurité des hôtes de machines virtuelles AKS
description: Découvrir le durcissement de la sécurité du système d’exploitation hôte des machines virtuelles AKS
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594378"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Durcissement de la sécurité des hôtes de machines virtuelles AKS 

Azure Kubernetes Service (AKS) est un service sécurisé conforme aux normes SOC, ISO, PCI DSS et HIPAA. Cet article traite du durcissement de la sécurité appliqué aux hôtes de machines virtuelles AKS. Pour plus d’informations sur la sécurité AKS, consultez [Concepts de sécurité pour les applications et les clusters dans AKS (Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/concepts-security).

Les clusters AKS sont déployés sur des machines virtuelles hôtes, qui exécutent un système d’exploitation doté d’une sécurité optimisée. Ce système d’exploitation hôte est actuellement basé sur une image Ubuntu 16.04.LTS avec un ensemble d’étapes de durcissement de sécurité supplémentaires appliquées (voir les détails du durcissement de la sécurité).   

L’objectif du système d’exploitation hôte avec une sécurité durcie est de réduire la surface d’attaque et de permettre le déploiement de conteneurs de manière sécurisée. 

> [!Important]
> Le système d’exploitation avec une sécurité durcie n’a PAS été testé selon les critères CIS. Même s’il y a des critères communs avec les bancs d’essai CIS, l’objectif n’est pas d’être conforme à CIS. L’objectif du durcissement du système d’exploitation hôte consiste à converger vers un niveau de sécurité cohérent avec les propres normes de sécurité d’hôte interne de Microsoft. 

## <a name="security-hardening-features"></a>Caractéristiques du durcissement de la sécurité 

* AKS fournit un système d’exploitation hôte à sécurité optimisée par défaut. Il n’existe actuellement aucune option pour sélectionner un autre système d’exploitation. 

* Azure applique des correctifs quotidiens (y compris des correctifs de sécurité) aux hôtes de machines virtuelles AKS. Certains de ces correctifs nécessitent un redémarrage et d’autres non. Vous êtes responsable de la planification des redémarrages nécessaires des hôtes de machines virtuelles AKS. Pour obtenir des conseils sur l’automatisation des mises à jour correctives AKS, consultez l’[application de correctifs pour les nœuds AKS](https://docs.microsoft.com/azure/aks/node-updates-kured).

Vous trouverez ci-dessous un résumé du travail de durcissement de la sécurité d’image qui est implémenté dans AKS-Engine pour générer le système d’exploitation hôte à sécurité optimisée. Ce travail a été mis en œuvre [dans ce projet GitHub](https://github.com/Azure/aks-engine/projects/7).  

AKS-Engine ne promeut ni n’adhère à aucune norme de sécurité spécifique pour l’instant, mais les ID d’audit CIS (Center for Internet Security) sont fournis pour des raisons pratiques, le cas échéant. 

## <a name="whats-configured"></a>Quelle est la configuration ?

| CIS  | Description d’audit| 
|---|---|
| 1.1.1.1 |Vérifier que le montage des systèmes de fichiers cramfs est désactivé|
| 1.1.1.2 |Vérifier que le montage des systèmes de fichiers freevxfs est désactivé|
| 1.1.1.3 |Vérifier que le montage des systèmes de fichiers jffs2 est désactivé|
| 1.1.1.4 |Vérifier que le montage des systèmes de fichiers HFS est désactivé|
| 1.1.1.5 |Vérifier que le montage des systèmes de fichiers HFS Plus est désactivé|
|1.4.3 |Garantir l’authentification requise pour le mode mono-utilisateur |
|1.7.1.2 |Vérifier que la bannière d’avertissement de connexion locale est correctement configurée |
|1.7.1.3 |Vérifier que la bannière d’avertissement de connexion à distance est correctement configurée |
|1.7.1.5 |Vérifier que des autorisations sont configurées sur /etc/issue |
|1.7.1.6 |Vérifier que des autorisations sont configurées sur /etc/issue.net |
|2.1.5 |Vérifier que --streaming-connection-idle-timeout n’a pas la valeur 0 |
|3.1.2 |Vérifier que l’envoi de redirection de paquets est désactivé |
|3.2.1 |Vérifier que les packages routés sources ne sont pas acceptés |
|3.2.2 |Vérifier que les redirections ICMP ne sont pas acceptées |
|3.2.3 |Vérifier que les redirections ICMP sécurisées ne sont pas acceptées |
|3.2.4 |Vérifier que les paquets suspects sont consignés dans un journal |
|3.3.1 |Vérifier que les annonces de routeur IPv6 ne sont pas acceptées |
|3.5.1 |Vérifier que le protocole DCCP est désactivé |
|3.5.2 |Vérifier que le protocole SCTP est désactivé |
|3.5.3 |Vérifier que RDS est désactivé |
|3.5.4 |Vérifier que TIPC est désactivé |
|4.2.1.2 |Vérifier que la journalisation est configurée |
|5.1.2 |Vérifier que des autorisations sont configurées sur /etc/crontab |
|5.2.4 |Vérifier que le transfert SSH X11 est désactivé |
|5.2.5 |Vérifier que SSH MaxAuthTries est défini sur une valeur inférieure ou égale à 4 |
|5.2.8 |Vérifier que la connexion racine SSH est désactivée |
|5.2.10 |Vérifier que SSH PermitUserEnvironment est désactivé |
|5.2.11 |Vérifier que seuls les algorithmes MAX approuvés sont utilisés |
|5.2.12 |Vérifier que l’intervalle du délai d’inactivité SSH est configuré |
|5.2.13 |Vérifier que SSH LoginGraceTime est défini sur une minute ou moins |
|5.2.15 |Vérifier que la bannière d’avertissement SSH est configurée |
|5.3.1 |Vérifier que les critères de création de mot de passe sont configurés |
|5.4.1.1 |Vérifier que l’expiration du mot de passe est de 90 jours ou moins |
|5.4.1.4 |Vérifier que le verrouillage de mot de passe inactif est de 30 jours ou moins |
|5.4.4 |Vérifier que le masque umask d’utilisateur par défaut est 027 ou plus restrictif |
|5.6 |Vérifier que l’accès à la commande su est restreint|

## <a name="additional-notes"></a>Remarques supplémentaires
 
* Pour réduire encore la surface d’attaque, certains pilotes de module de noyau inutiles ont été désactivés dans le système d’exploitation. 

* Le système d’exploitation avec une sécurité durcie n’est PAS pris en charge en dehors de la plateforme AKS. 

## <a name="next-steps"></a>Étapes suivantes  

Pour plus d’informations sur la sécurité AKS, consultez les articles suivants : 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Concepts de sécurité AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Bonnes pratiques AKS](https://docs.microsoft.com/azure/aks/best-practices)
