---
title: Résoudre les erreurs de fournisseur de ressources de fichiers NetApp Azure | Microsoft Docs
description: Décrit les causes, des solutions et des solutions de contournement pour les erreurs de fournisseur de ressources de fichiers NetApp Azure courantes.
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
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769435"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Corriger les erreurs du fournisseur de ressources Azure NetApp Files
Cet article décrit courantes fournisseur de ressources de fichiers Azure NetApp erreurs, leurs causes, solutions et solutions de contournement. 

<a name="error_01"></a>***Azure Key Vault ne pas configuré.***   
Azure Key Vault stocke les informations d’identification requises pour accéder à l’API sous-jacente. Cette erreur indique qu’Azure Key Vault n’a pas reçu les informations d’identification complets pour accéder à l’API sous-jacente.

* Cause :  
Azure Key Vault n’a pas reçu les informations d’identification correctes, ou les informations d’identification ne sont pas complets.  

* Solution   
Le service Azure NetApp Files utilise Azure Key Vault. Azure Key Vault s’authentifie à l’aide d’un jeton d’Azure Active Directory. Par conséquent, le propriétaire de l’application doit inscrire l’application dans Azure Active Directory.

* Solution de contournement   
Aucune.  Azure Key Vault doit être correctement configuré pour l’utilisation de fichiers NetApp de Azure.  

<a name="error_02"></a>***La création du jeton ne peut pas être modifié.***   
Cette erreur se produit lorsque vous essayez de modifier le jeton de la création une fois que le volume a été créé.
Jeton de création doit être définie lorsque le volume est créé et ne peut pas être modifié ultérieurement.

* Cause :   
Vous essayez de modifier le jeton de la création une fois que le volume a été créé, qui n’est pas une opération prise en charge.

* Solution   
Une fois que le Volume a été créé, supprimez le paramètre de la demande pour ignorer le message d’erreur.

* Solution de contournement   
Si vous devez modifier le jeton de la création, vous pouvez créer un nouveau volume avec un nouveau jeton de la création et puis migrer les données vers le nouveau volume.


<a name="error_03"></a>***La création du jeton doit être au moins 16 caractères.***   
Cette erreur se produit lorsque le jeton de la création ne répond pas à la longueur requise. La longueur du jeton de création doit être au moins 16 caractères.

* Cause :   
Le jeton de la création ne respecte pas les exigences de longueur.  Lorsque vous créez un volume à l’aide de l’API, un jeton de la création est nécessaire. Si vous utilisez le portail, le jeton peut être généré automatiquement.

* Solution   
Augmentez la longueur du jeton de la création. Par exemple, vous pouvez ajouter un autre mot au début ou à la fin du jeton de la création.

* Solution de contournement   
La configuration minimale requise longueur du jeton de la création ne peut pas être contourné.  Vous pouvez utiliser un préfixe ou un suffixe pour augmenter la longueur de la création du jeton.


<a name="error_04"></a>***Erreur lors de la suppression d’un volume qui est introuvable sur Azure Files de NetApp.***   
Cette erreur s’est produite, car le Registre interne des ressources est désynchronisé.

* Cause :   
Le volume peut rester affiché dans le portail pendant un certain temps après que qu’il a été supprimé. Si vous supprimez le volume à l’aide de l’API, il est possible que le volume n’a pas été spécifié correctement. L’erreur peut également provenir de cache de navigateur obsolète.

* Solution   
Cache de navigateur clair si vous utilisez le portail. Il existe également un cache interne qui est actualisé toutes les 10 minutes.  Vous pouvez essayer d’effacer le cache à nouveau.  Si le problème persiste après 10 minutes, vous pouvez créer un ticket de support.

* Solution de contournement   
Utiliser un autre volume entre-temps et ignorer l’existant.


<a name="error_05"></a>***Erreur lors de l’insertion d’un nouveau Volume, consultez les fichiers NetApp Azure.***   
Cette erreur se produit parce que le Registre interne des ressources est désynchronisé.

* Cause :   
Le volume peut rester affiché dans le portail pendant un certain temps après que qu’il a été supprimé. Si vous supprimez le volume à l’aide de l’API, il est possible que le volume n’a pas été spécifié correctement.

* Solution   
Si vous utilisez le portail, le volume a déjà été créé.  Le volume doit apparaître automatiquement. Si le problème persiste, vous pouvez créer un ticket de support.

* Solution de contournement   
Vous pouvez créer un volume avec un autre nom et un jeton de création différentes.


<a name="error_06"></a>***Le nom de chemin d’accès de fichier peut contenir des lettres, des chiffres et des traits d’union (« »-« ») uniquement.***   
Cette erreur se produit lorsque le chemin d’accès de fichier contient des caractères non pris en charge, par exemple, une période («. »), virgule («, »), trait de soulignement («\_»), ou signe dollar (« $»).

* Cause :   
Le chemin d’accès de fichier contient des caractères non pris en charge, par exemple, une période («. »), virgule («, »), trait de soulignement («\_»), ou signe dollar (« $»).

* Solution   
Supprimer les caractères qui ne sont pas des caractères alphabétiques, des nombres ou des traits d’union («- ») à partir du chemin de fichier que vous avez entré.

* Solution de contournement   
Vous pouvez remplacer un trait de soulignement par un trait d’union ou utiliser des majuscules plutôt que des espaces pour indiquer le début de nouveaux mots (par exemple, à l’aide de « NewVolume » au lieu de « nouveau volume »).


<a name="error_07"></a>***ID de volume ne peut pas être modifié.***   
Cette erreur se produit lorsque vous essayez de modifier l’ID de volume.  Modification de l’ID de volume n’est pas une opération prise en charge.

* Cause :   
L’ID du système de fichiers est défini lorsque le volume est créé. L’ID de volume ne peut pas être modifié par la suite.

* Solution   
Aucune.

* Solution de contournement   
Aucune.  L’ID de volume est générée lorsque le volume est créé et ne peut pas être modifié par la suite.


<a name="error_08"></a>***Une valeur non valide '{0}' a été reçu pour {1}.***   
Ce message indique une erreur dans les champs pour RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Cause :   
La demande de validation d’entrée a échoué pour au moins un des champs suivants : RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Solution   
Veillez à définir tous les paramètres obligatoires et non conflictuelles sur la ligne de commande. Par exemple, vous ne peut pas définir les UnixReadOnly UnixReadWrite paramètres et en même temps.

* Solution de contournement   
Consultez la section de la Solution.  


<a name="error_09"></a>***Valeur manquante pour «{0}».***   
Cette erreur indique qu’un attribut requis est manquant dans la demande pour au moins un des paramètres suivants : RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Cause :   
La demande de validation d’entrée a échoué pour au moins un des champs suivants : RuleIndex AllowedClients, UnixReadOnly, UnixReadWrite, Nfsv3 et Nfsv4.

* Solution   
Veillez à définir tous les paramètres obligatoires et non conflictuelles sur la ligne de commande. Par exemple, vous ne peut pas définir les UnixReadOnly UnixReadWrite paramètres et en même temps

* Solution de contournement   
Consultez la section de la Solution.  


<a name="error_10"></a> ***{0} déjà en cours d’utilisation.***   
Cette erreur indique que le nom de la ressource a déjà été utilisé.

* Cause :   
Vous essayez de créer un volume avec un nom qui est identique à un volume existant.

* Solution   
Utilisez un nom unique lors de la création d’un volume.

* Solution de contournement   
Si nécessaire, vous pouvez modifier le nom du volume existant afin que le nouveau volume permettre utiliser le nom souhaité.


<a name="error_11"></a> ***{0} trop court.***   
Cette erreur indique que le nom du volume ne répond pas à la longueur minimale requise.

* Cause :   
Le nom de volume est trop court.

* Solution   
Augmentez la longueur du nom de volume.  

* Solution de contournement   
Vous pouvez ajouter un préfixe commun ou un suffixe au nom de volume.


<a name="error_12"></a>***API de fichiers NetApp Azure inaccessible.***   
L’API Azure s’appuie sur l’API de fichiers NetApp Azure pour gérer les volumes.  Cette erreur indique un problème avec la connexion d’API.

* Cause :   
L’API sous-jacente ne répond pas, ce qui se traduit par une erreur interne. Cette erreur est susceptible d’être temporaire.

* Solution   
Le problème est susceptible d’être temporaire.  La demande doit réussir après un certain temps.

* Solution de contournement   
Aucune. L’API sous-jacente est essentiel pour la gestion des volumes.  


<a name="error_13"></a>***Aucune information d’identification ne trouvé pour l’abonnement '{0}'.***   
Cette erreur indique que les informations d’identification fournies sont pas valides ou n’ont pas été définies correctement dans l’abonnement.

* Cause :   
Informations d’identification qui sont non valides ou définie de façon incorrecte empêchent l’accès au service de gestion des volumes.

* Solution   
Assurez-vous que les informations d’identification sont définies et avez correctement entrées sur la ligne de commande.

* Solution de contournement   
Aucune.  Définition des informations d’identification correctement est essentiel pour l’utilisation de fichiers NetApp de Azure.  


<a name="error_14"></a>***Aucun id de résultat d’opération trouvé pour «{0}».***   
Cette erreur indique qu’une erreur interne n’empêche l’opération de se terminer.

* Cause :   
Une erreur interne s’est produite et a empêché l’opération de se terminer.

* Solution   
Cette erreur est susceptible d’être temporaire.  Patientez quelques minutes et réessayez. Si le problème persiste, créez un ticket au support technique pour étudier le problème.

* Solution de contournement   
Patientez quelques minutes et vérifiez si le problème persiste.


<a name="error_15"></a>***Opération '{0}' non pris en charge.***   
Cette erreur indique que la commande n’est pas disponible pour l’abonnement actif ou de la ressource.

* Cause :   
L’opération n’est pas disponible pour l’abonnement ou de la ressource.

* Solution   
Assurez-vous que la commande est correcte et disponible pour la ressource et l’abonnement que vous utilisez.

* Solution de contournement   
Consultez la section de la Solution.  


<a name="error_16"></a>***Opération patch n’est pas pris en charge pour ce type de ressource.***   
Cette erreur se produit lorsque vous essayez de modifier la cible de montage ou de la capture instantanée.

* Cause :   
La cible de montage est définie lorsqu’il est créé, et il ne peut pas être modifié par la suite.

* Solution   
Aucune.  La cible de montage ne peut pas être modifiée une fois que le volume est créé.

* Solution de contournement   
Aucune.


<a name="error_17"></a>***A reçu une valeur de propriété en lecture seule '{0}'.***   
Cette erreur se produit lorsque vous définissez une valeur pour une propriété qui ne peut pas être modifiée. Par exemple, vous ne pouvez pas modifier l’ID de volume.

* Cause :   
Vous avez tenté de modifier un paramètre (par exemple, l’ID de volume) qui ne peut pas être modifié.

* Solution   
Aucune. Le paramètre pour l’ID de volume ne peut pas être modifié.

* Solution de contournement   
L’ID de volume ne doit pas nécessiter de modification.  Par conséquent, une solution de contournement n’est pas nécessaire.

<a name="error_18"></a>***Demandé {0} n’a été trouvé.***   
Cette erreur se produit lorsque vous tentez de référencer une ressource qui n’existe pas, par exemple, un volume ou un instantané. La ressource a peut-être été supprimée ou avoir un nom de ressource avec une faute.

* Cause :   
Vous tentez de référencer une ressource qui n’existe pas (par exemple, un volume ou un instantané) qui a déjà été supprimée ou a un nom de ressource mal orthographiés figurent.

* Solution   
Vérifiez la demande de fautes d’orthographe pour vous assurer qu’il est correctement référencée.

* Solution de contournement   
Consultez la section de la Solution.

<a name="error_19"></a>***Impossible d’obtenir des informations d’identification pour l’abonnement '{0}'.***   
Cette erreur indique que les informations d’identification fournies sont pas valides ou pas définie correctement dans l’abonnement.

* Cause :   
Informations d’identification qui ne sont pas valides ou incorrectement défini dans l’abonnement empêcher l’accès au service de gestion des volumes.

* Solution   
Assurez-vous que les informations d’identification sont définies et avez correctement entrées sur la ligne de commande.

* Solution de contournement   
Aucune.  Correctement les informations d’identification de l’ensemble sont essentielles pour l’utilisation de fichiers NetApp de Azure.

<a name="error_20"></a>***Erreur de fichiers NetApp Azure inconnu.***   
L’API Azure s’appuie sur l’API de fichiers NetApp Azure pour gérer les volumes. L’erreur indique un problème dans la communication avec l’API.

* Cause :   
L’API sous-jacente envoie une erreur inconnue.  Cette erreur est susceptible d’être temporaire.

* Solution   
Le problème est susceptible d’être temporaire et la demande doit réussir après un certain temps. Si le problème persiste, créez un ticket de support pour que le problème examiné.

* Solution de contournement   
Aucune.  L’API sous-jacente est essentiel pour la gestion des volumes.

<a name="error_21"></a>***Valeur reçue pour une propriété inconnue '{0}'.***   
Cette erreur se produit lorsque les propriétés qui n’existe pas sont fournies pour une ressource, telles que le volume, un instantané ou une cible de montage.

* Cause :   
La requête a un ensemble de propriétés qui peuvent être utilisées avec chaque ressource.  Vous ne pouvez pas inclure toutes les propriétés qui n’existe pas dans la demande.

* Solution   
Assurez-vous que tous les noms de propriété sont correctement orthographiés et les propriétés sont disponibles pour l’abonnement et de la ressource.

* Solution de contournement   
Réduisez le nombre de propriétés définies dans la demande pour éliminer la propriété qui a provoqué l’erreur.


<a name="error_22"></a>***Opération de mise à jour n’est pas prise en charge pour ce type de ressource.***   
Seuls les volumes peuvent être mis à jour. Cette erreur se produit lorsque vous essayez d’effectuer une opération de mise à jour non pris en charge, par exemple, un instantané de la mise à jour.

* Cause :   
La ressource que vous voulez mettre à jour ne prend pas en charge l’opération de mise à jour.  Seuls les volumes peuvent avoir leurs propriétés modifiées.

* Solution   
Aucune.  La ressource que vous essayez de mettre à jour ne prend pas en charge l’opération de mise à jour. Par conséquent, il ne peut pas être modifié.

* Solution de contournement   
Pour un volume, créez une ressource avec la mise à jour sur place et migrer les données.


<a name="error_23"></a>***Nombre d’éléments : {0} pour l’objet : {1} est hors de portée min-max.***   
Cette erreur se produit lorsque les règles de stratégie d’exportation ne répondent pas à la spécification de plage minimale ou maximale.  Si vous définissez la stratégie d’exportation, il doit avoir la règle de stratégie d’une exportation au minimum et cinq règles de stratégie d’exportation au maximum.

* Cause :   
Vous avez défini la stratégie d’exportation ne respecte pas la plage requise.  

* Solution   
Assurez-vous que l’index n’est pas déjà utilisé et qui se trouve dans la plage de 1 à 5.

* Solution de contournement   
Il n’est pas obligatoire d’utiliser la stratégie d’exportation sur les volumes. Par conséquent, vous pouvez omettre la stratégie d’exportation de totalement si vous n’avez pas besoin d’avoir des règles de stratégie d’exportation.


<a name="error_24"></a>***Erreur de valeur pour l’objet dupliquée {0}.***   
Cette erreur se produit lorsque la stratégie d’exportation n’est pas définie avec un index unique.  Lorsque vous définissez des stratégies d’exportation, toutes les règles de stratégie d’exportation doivent avoir un index unique compris entre 1 et 5.

* Cause :   
La stratégie d’exportation défini ne respecte pas la configuration requise pour les règles de stratégie d’exportation. Vous devez disposer de règle de stratégie d’une exportation au minimum et cinq règles de stratégie d’exportation au maximum.  

* Solution   
Assurez-vous que l’index n’est pas déjà utilisé et qu’il s’agit de la plage de 1 à 5.

* Solution de contournement   
Utilisez un autre index de la règle que vous cherchez à définir.


