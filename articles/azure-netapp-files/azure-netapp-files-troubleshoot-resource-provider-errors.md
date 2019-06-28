---
title: Corriger les erreurs du fournisseur de ressources Azure NetApp Files | Microsoft Docs
description: Cet article décrit les causes, solutions et contournements à employer pour résoudre les erreurs courantes du fournisseur de ressources Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769435"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Corriger les erreurs du fournisseur de ressources Azure NetApp Files
Cet article décrit les erreurs courantes du fournisseur de ressources Azure NetApp Files, ainsi que leurs causes. Il présente des solutions et divers contournements possibles pour résoudre ces erreurs. 

<a name="error_01"></a>***Azure Key Vault n’est pas configuré.***   
Azure Key Vault stocke les informations d’identification requises pour accéder à l’API sous-jacente. Cette erreur indique qu’Azure Key Vault n’a pas reçu les informations d’identification complètes pour accéder à l’API sous-jacente.

* Cause :  
Azure Key Vault n’a pas reçu les informations d’identification correctes, ou les informations d’identification ne sont pas complètes.  

* Solution   
Le service Azure NetApp Files utilise Azure Key Vault. Azure Key Vault s’authentifie à l’aide d’un jeton d’Azure Active Directory. Le propriétaire de l’application doit d’abord inscrire son application dans Azure Active Directory.

* Solution de contournement   
Aucune.  Azure Key Vault doit être correctement configuré pour l’utilisation d’Azure NetApp Files.  

<a name="error_02"></a>***Impossible de modifier le jeton de création.***   
Cette erreur se produit lorsque vous essayez de modifier le jeton de création après la création du volume.
Le jeton de création doit être défini lors de la création du volume. Il ne peut pas être modifié ultérieurement.

* Cause :   
Vous essayez de modifier le jeton de création après la création du volume. Cette opération n’est pas prise en charge.

* Solution   
Après la création du volume, supprimez le paramètre de la requête pour masquer ce message d’erreur.

* Solution de contournement   
Si vous devez modifier le jeton de création, vous pouvez créer un nouveau volume avec un nouveau jeton de création, puis migrer les données vers ce nouveau volume.


<a name="error_03"></a>***Le jeton de création doit contenir au moins 16 caractères.***   
Cette erreur se produit lorsque le jeton de création ne répond pas à la longueur requise. La longueur du jeton de création doit être au moins de 16 caractères.

* Cause :   
Le jeton de création ne respecte pas les exigences de longueur.  Lorsque vous créez un volume à l’aide de l’API, un jeton de création est requis. Si vous utilisez le portail, le jeton peut être généré automatiquement.

* Solution   
Augmentez la longueur du jeton de création. Par exemple, vous pouvez ajouter un autre mot au début ou à la fin du jeton de création.

* Solution de contournement   
La longueur minimale requise pour le longueur du jeton de création ne peut pas être contournée.  Vous pouvez utiliser un préfixe ou un suffixe pour augmenter la longueur du jeton de création.


<a name="error_04"></a>***Erreur lors de la suppression d’un volume introuvable sur Azure NetApp Files.***   
Cette erreur se produit en cas de désynchronisation du registre interne des ressources.

* Cause :   
Le volume peut rester affiché dans le portail pendant un certain temps après sa suppression. Si vous supprimez le volume à l’aide de l’API, il est possible que le volume n’a pas été spécifié correctement. L’erreur peut également être due à l’obsolescence du cache de votre navigateur.

* Solution   
Videz le cache de votre navigateur (si vous utilisez le portail). Il existe également un cache interne qui est actualisé toutes les 10 minutes.  Vous pouvez essayer de re-vider ce cache.  Si le problème persiste après 10 minutes, vous pouvez créer un ticket de support.

* Solution de contournement   
En attendant que le problème soit résolu, utilisez un autre volume.


<a name="error_05"></a>***Erreur lors de l’insertion d’un nouveau volume détecté sur Azure NetApp Files.***   
Cette erreur se produit en cas de désynchronisation du registre interne des ressources.

* Cause :   
Le volume peut rester affiché dans le portail pendant un certain temps après sa suppression. Si vous supprimez le volume à l’aide de l’API, il est possible que le volume n’a pas été spécifié correctement.

* Solution   
Si vous utilisez le portail, le volume a déjà été créé.  Le volume doit s’afficher automatiquement. Si le problème persiste, vous pouvez créer un ticket de support.

* Solution de contournement   
Vous pouvez créer un volume doté d’un autre nom et d’un autre jeton de création.


<a name="error_06"></a>***Le nom du chemin du fichier peut contenir seulement des lettres, des chiffres et des traits d’union (« - »).***   
Cette erreur se produit lorsque le chemin d’accès du fichier contient des caractères qui ne sont pas pris en charge. Il peut s’agir d’un point («. »), d’une virgule («, »), d’un trait de soulignement («\_»), ou du signe « dollar » (« $»).

* Cause :   
Le chemin d’accès du fichier contient des caractères qui ne sont pas pris en charge. Il peut s’agir d’un point («. »), d’une virgule («, »), d’un trait de soulignement («\_»), ou du signe « dollar » (« $»).

* Solution   
Supprimez du chemin d’accès du fichier les caractères qui ne sont pas des caractères alphabétiques, des nombres ou des traits d’union («- »).

* Solution de contournement   
Vous pouvez remplacer un trait de soulignement par un trait d’union ou utiliser des majuscules plutôt que des espaces pour indiquer le début de nouveaux mots (par exemple, « NouveauVolume » au lieu de « nouveau volume »).


<a name="error_07"></a>***Impossible de modifier l’ID du volume.***   
Cette erreur se produit lorsque vous essayez de modifier l’ID (identifiant) du volume.  La modification de l’ID du volume n’est pas une opération prise en charge.

* Cause :   
L’ID du système de fichiers est défini lors de la création du volume. Vous ne pouvez pas modifier l’ID du volume par la suite.

* Solution   
Aucune.

* Solution de contournement   
Aucune.  L’ID du volume est généré lors de la création du volume. Vous ne pouvez pas le modifier par la suite.


<a name="error_08"></a>***Une valeur '{0}' non valide a été reçue pour {1}.***   
Ce message indique une erreur dans les champs pour RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Cause :   
La requête de validation d’entrée a échoué pour au moins un des champs suivants : RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Solution   
Veillez à définir tous les paramètres obligatoires et non conflictuels sur la ligne de commande. Par exemple, vous ne pouvez pas définir les paramètres UnixReadOnly et UnixReadWrite en même temps.

* Solution de contournement   
Veuillez consulter la section Solution.  


<a name="error_09"></a>***Valeur manquante pour '{0}'.***   
Cette erreur indique qu’un attribut requis est manquant dans la requête pour au moins l’un des paramètres suivants : RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Cause :   
La requête de validation d’entrée a échoué pour au moins un des champs suivants : RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Solution   
Veillez à définir tous les paramètres obligatoires et non conflictuels sur la ligne de commande. Par exemple, vous ne pouvez pas définir les paramètres UnixReadOnly et UnixReadWrite en même temps.

* Solution de contournement   
Veuillez consulter la section Solution.  


<a name="error_10"></a> ***{0} déjà en cours d’utilisation.***   
Cette erreur indique que le nom de la ressource est déjà utilisé.

* Cause :   
Vous essayez de créer un volume avec un nom identique à celui d’un volume existant.

* Solution   
Utilisez un nom unique lors de la création d’un volume.

* Solution de contournement   
Si nécessaire, vous pouvez modifier le nom du volume existant pour pouvoir nommer votre nouveau volume comme vous le souhaitez.


<a name="error_11"></a> ***{0} est trop court.***   
Cette erreur indique que le nom du volume ne répond pas à la longueur minimale requise.

* Cause :   
Le nom de volume est trop court.

* Solution   
Augmentez la longueur du nom de volume.  

* Solution de contournement   
Vous pouvez ajouter un préfixe ou un suffixe courant au nom du volume.


<a name="error_12"></a>***API Azure NetApp Files inaccessible.***   
L’API Azure s’appuie sur l’API Azure NetApp Files pour gérer les volumes.  Cette erreur indique un problème avec la connexion d’API.

* Cause :   
L’API sous-jacente ne répond pas, ce qui se traduit par une erreur interne. Cette erreur est généralement temporaire.

* Solution   
Ce problème est généralement temporaire.  La requête devrait réussir après un certain temps.

* Solution de contournement   
Aucune. L’API sous-jacente est essentielle pour la gestion des volumes.  


<a name="error_13"></a>***Aucune information d’identification trouvée pour l’abonnement '{0}'.***   
Cette erreur indique que les informations d’identification fournies sont pas valides ou n’ont pas été définies correctement dans l’abonnement.

* Cause :   
Les informations d’identification non valides ou définie de façon incorrecte empêchent l’accès au service de gestion des volumes.

* Solution   
Assurez-vous que les informations d’identification sont correctement définies et entrées sur la ligne de commande.

* Solution de contournement   
Aucune.  Il est crucial de définir correctement les informations d’identification. Sans quoi vous ne pourrez pas utiliser Azure NetApp Files correctement.  


<a name="error_14"></a>***Aucun ID de résultat d’opération trouvé pour '{0}'.***   
Cette erreur indique qu’une erreur interne empêche l’opération de se terminer.

* Cause :   
Une erreur interne s’est produite et a empêché l’opération de se terminer.

* Solution   
Cette erreur est généralement temporaire.  Patientez quelques minutes, puis réessayez. Si le problème persiste, créez un ticket de support technique pour étudier le problème.

* Solution de contournement   
Patientez quelques minutes et vérifiez si le problème persiste.


<a name="error_15"></a>***Opération '{0}' non prise en charge.***   
Cette erreur indique que la commande n’est pas disponible pour l’abonnement ou la ressource que vous utilisez.

* Cause :   
L’opération n’est pas disponible pour l’abonnement ou la ressource.

* Solution   
Assurez-vous que la commande est correcte et disponible pour la ressource et l’abonnement que vous utilisez.

* Solution de contournement   
Veuillez consulter la section Solution.  


<a name="error_16"></a>***L’opération de correction n’est pas prise en charge pour ce type de ressource.***   
Cette erreur se produit lorsque vous essayez de modifier la cible de montage ou la capture instantanée.

* Cause :   
La cible de montage est définie lors de sa création. Vous ne pouvez pas la modifier par la suite.

* Solution   
Aucune.  Vous ne pouvez plus modifier la cible de montage après la création du volume.

* Solution de contournement   
Aucune.


<a name="error_17"></a>***À reçu une valeur pour la propriété en lecture seule '{0}'.***   
Cette erreur se produit lorsque vous définissez une valeur pour une propriété qui ne peut pas être modifiée. Par exemple, vous ne pouvez pas modifier l’ID du volume.

* Cause :   
Vous avez tenté de modifier un paramètre (par exemple, l’ID du volume) qui ne peut pas être modifié.

* Solution   
Aucune. Vous ne pouvez pas modifier le paramètre de l’ID du volume.

* Solution de contournement   
L’ID de volume ne doit pas nécessiter de modification.  Par conséquent, une solution de contournement n’est pas nécessaire.

<a name="error_18"></a>***La ressource {0} demandée est introuvable.***   
Cette erreur se produit lorsque vous tentez de référencer une ressource qui n’existe pas, par exemple, un volume ou un instantané. La ressource a peut-être été supprimée ou son nom n’est pas correctement saisi.

* Cause :   
Vous tentez de référencer une ressource qui n’existe pas (par exemple, un volume ou un instantané), qui a déjà été supprimée ou dont le nom n’a pas été correctement saisi.

* Solution   
Vérifiez si le nom de la ressource est correctement saisi dans la requête.

* Solution de contournement   
Veuillez consulter la section Solution.

<a name="error_19"></a>***Impossible d’obtenir les informations d’identification de l’abonnement '{0}'.***   
Cette erreur indique que les informations d’identification fournies sont pas valides ou qu’elles ne sont pas définies correctement dans l’abonnement.

* Cause :   
Les informations d’identification invalides ou incorrectement définies dans l’abonnement empêchent l’accès au service pour la gestion des volumes.

* Solution   
Assurez-vous que les informations d’identification sont correctement définies et entrées sur la ligne de commande.

* Solution de contournement   
Aucune.  Il est crucial de définir correctement les informations d’identification. Sans quoi vous ne pourrez pas utiliser Azure NetApp Files correctement.

<a name="error_20"></a>***Erreur d’instance Azure NetApp Files inconnue.***   
L’API Azure s’appuie sur l’API Azure NetApp Files pour gérer les volumes. Cette erreur indique un problème de communication avec l’API.

* Cause :   
L’API sous-jacente envoie une erreur inconnue.  Cette erreur est généralement temporaire.

* Solution   
Ce problème est généralement temporaire. La requête devrait réussir après un certain temps. Si le problème persiste, créez un ticket de support technique pour étudier le problème.

* Solution de contournement   
Aucune.  L’API sous-jacente est essentielle pour la gestion des volumes.

<a name="error_21"></a>***La valeur reçue '{0}' correspond à une propriété inconnue.***   
Cette erreur se produit lorsque des propriétés qui n’existent pas sont fournies pour une ressource, telles qu’un nom de volume, d’instantané ou de cible de montage.

* Cause :   
Une requête est constituée d’un ensemble de propriétés qui peuvent être utilisées avec chaque ressource.  Vous ne pouvez pas inclure dans une requête des propriétés qui n’existent pas.

* Solution   
Assurez-vous que tous les noms de propriété sont correctement orthographiés et que les propriétés sont disponibles dans l’abonnement et la ressource.

* Solution de contournement   
Réduisez le nombre de propriétés définies dans la requête pour éliminer la propriété ayant provoqué l’erreur.


<a name="error_22"></a>***L’opération de mise à jour n’est pas prise en charge pour ce type de ressource.***   
Seuls les volumes peuvent être mis à jour. Cette erreur se produit lorsque vous essayez d’effectuer une opération de mise à jour non prise en charge, par exemple, en mettant à jour un instantané.

* Cause :   
La ressource que vous voulez mettre à jour ne prend pas en charge l’opération de mise à jour.  Seules les propriétés des volumes peuvent être modifiées.

* Solution   
Aucune.  La ressource que vous essayez de mettre à jour ne prend pas en charge l’opération de mise à jour. Il n’est donc pas possible de la modifier.

* Solution de contournement   
Pour un volume : créez une ressource avec la mise à jour en place, puis migrez les données.


<a name="error_23"></a>***Le nombre d’éléments : {0} pour l’objet : {1} est situé hors des plages minimum et maximum.***   
Cette erreur se produit lorsque les règles de stratégie d’exportation ne répondent pas à la spécification d’une plage minimum ou maximum.  Si vous définissez la stratégie d’exportation, elle doit avoir au moins une règle de stratégie d’exportation et jusqu’à cinq règles de stratégie d’exportation au maximum.

* Cause :   
Vous avez défini une stratégie d’exportation qui ne respecte pas la plage requise.  

* Solution   
Assurez-vous que l’index n’est pas déjà utilisé et qu’il se trouve dans la plage de 1 à 5.

* Solution de contournement   
Il n’est pas obligatoire d’utiliser la stratégie d’exportation sur les volumes. Par conséquent, vous pouvez omettre la totalité de la stratégie d’exportation si vous n’avez pas besoin d’avoir des règles de stratégie d’exportation.


<a name="error_24"></a>***Erreur de valeur dupliquée pour l’objet {0}.***   
Cette erreur se produit lorsque la stratégie d’exportation n’est pas définie avec un index unique.  Lorsque vous définissez des stratégies d’exportation, toutes les règles de stratégie d’exportation doivent avoir un index unique compris entre 1 et 5.

* Cause :   
La stratégie d’exportation définie ne respecte pas la configuration requise pour les règles de stratégie d’exportation. Vous devez avoir au moins une règle de stratégie d’exportation et jusqu’à cinq règles de stratégie d’exportation au maximum.  

* Solution   
Assurez-vous que l’index n’est pas déjà utilisé et qu’il se trouve dans la plage de 1 à 5.

* Solution de contournement   
Utilisez un autre index pour la règle que vous essayez de définir.


