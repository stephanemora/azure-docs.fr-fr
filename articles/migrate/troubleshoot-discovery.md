---
title: Résoudre les problèmes de détection de serveur, d’inventaire logiciel et de détection SQL en cours
description: Obtenir de l’aide sur la détection de serveur, l’inventaire logiciel et la détection d’applications SQL et Web.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: ffa77200a3f0341e74a049b7db3196ef1d726e73
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423861"
---
# <a name="troubleshoot-ongoing-server-discovery-software-inventory-and-sql-and-web-apps-discovery"></a>Résoudre les problèmes de détection de serveur, d’inventaire logiciel et de détection d’applications SQL et Web en cours

Cet article vous aide à résoudre les problèmes liés à la détection de serveur, à l’inventaire logiciel et à la détection d’instances et de bases de données du Serveur SQL en cours.

## <a name="discovered-servers-not-showing-in-the-portal"></a>Serveurs détectés n’apparaissant pas dans le portail

Vous obtenez cette erreur lorsque les serveurs ne sont pas encore visibles dans le portail et que l'état de détection est **Détection en cours**.
 
### <a name="remediation"></a>Correction

Si les serveurs n’apparaissent pas dans le portail, patientez quelques minutes, car la détection des serveurs s’exécutant sur un serveur vCenter prend environ 15 minutes. Il faut 2 minutes à chaque hôte Hyper-V ajouté sur l’appareil pour la détection des serveurs exécutés sur l’ordinateur hôte et une minute pour la détection de chaque serveur ajouté sur l’appareil physique.

Si l’état n’est toujours pas modifié :

- Sélectionnez **Actualiser** sur l’onglet **Serveurs** pour afficher le nombre de serveurs détectés dans Azure Migrate : Détection, évaluation et Azure Migrate : Migration de serveurs.

Si l’étape précédente ne fonctionne pas et que vous détectez des serveurs VMware :

1. Vérifiez que les autorisations du compte vCenter que vous avez spécifié ont été définies correctement, avec un accès à au moins un serveur.
1. Vérifiez sur VMware que le compte vCenter est autorisé à accéder au niveau du dossier de machine virtuelle vCenter. Azure Migrate ne peut pas détecter les serveurs sur VMware. [En savoir plus](set-discovery-scope.md) sur la définition de l’étendue de la découverte.

## <a name="server-data-not-updating-in-the-portal"></a>Données de serveur non mises à jour sur le portail

Cette erreur apparaît si des serveurs découverts n’apparaissent pas dans le portail ou si des données de serveur sont obsolètes. 

### <a name="remediation"></a>Correction

Attendez quelques minutes car il faut jusqu’à 30 minutes pour que les modifications apportées aux données de configuration du serveur détecté s’affichent dans le portail et quelques heures pour que les modifications apportées aux données d’inventaire logiciel apparaissent. Si aucune donnée n’apparaît après ce délai, actualisez et suivez les étapes suivantes :

1. Dans **les Serveurs Windows, Linux et SQL** > **Azure Migrate : Détection et évaluation**, sélectionnez **Vue d’ensemble**.
1. Sous **Gérer**, sélectionnez **Appliances**.
1. Sélectionnez **Actualiser les services**.
Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="deleted-servers-appear-in-the-portal"></a>Les serveurs supprimés s’affichent dans le portail

Vous recevez cette erreur lorsque les serveurs supprimés continuent à apparaître dans le portail.

### <a name="remediation"></a>Correction

Si les données continuent de s’afficher, patientez 30 minutes et suivez les étapes suivantes :

1. Dans **les Serveurs Windows, Linux et SQL** > **Azure Migrate : Détection et évaluation**, sélectionnez **Vue d’ensemble**.
1. Sous **Gérer**, sélectionnez **Appliances**.
1. Sélectionnez **Actualiser les services**.
Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="you-imported-a-csv-but-see-discovery-is-in-progress"></a>Vous avez importé un CSV, mais voyez le message « Détection en cours »

Cet état s’affiche si le chargement de votre CSV a échoué en raison d’une défaillance de validation.

### <a name="remediation"></a>Correction

Importez le CSV à nouveau. Téléchargez le rapport d’erreurs du chargement précédent et suivez les instructions de correction fournies dans le fichier pour corriger les erreurs. Téléchargez le rapport d’erreurs à partir de la section **Détails de l’importation** sur la page **Détecter les serveurs**.

## <a name="you-dont-see-software-inventory-details-after-you-update-guest-credentials"></a>Vous ne voyez pas les détails de l’inventaire logiciel après avoir mis à jour les informations d’identification de l’invité

Vous recevez cette erreur lorsque les détails de l’inventaire n’apparaissent pas, même après la mise à jour des informations d’identification de l’invité.

### <a name="remediation"></a>Correction

La détection de l’inventaire logiciel est effectuée une fois toutes les 24 heures. Ce processus peut prendre quelques minutes en fonction du nombre de serveurs détectés. Si vous voulez voir les détails immédiatement, actualisez comme suit :

1. Dans **les Serveurs Windows, Linux et SQL** > **Azure Migrate : Détection et évaluation**, sélectionnez **Vue d’ensemble**.
1. Sous **Gérer**, sélectionnez **Appliances**.
1. Sélectionnez **Actualiser les services**.
Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="unable-to-export-software-inventory"></a>Impossible d’exporter l’inventaire logiciel

Cette erreur apparaît lorsque vous n’avez pas de privilèges de collaborateur.

### <a name="remediation"></a>Correction

Vérifiez que l’utilisateur qui télécharge l’inventaire à partir du portail dispose de privilèges de contributeur sur l’abonnement.

## <a name="common-software-inventory-errors"></a>Erreurs courantes d’inventaire logiciel

Azure Migrate prend en charge l’inventaire logiciel à l’aide d’Azure Migrate : Détection et évaluation. L’inventaire logiciel est actuellement pris en charge pour VMware uniquement. [En savoir plus](how-to-discover-applications.md) sur les exigences de l’inventaire logiciel.

La liste des erreurs d’inventaire logiciel est résumée dans le tableau ci-dessous.

> [!Note]
> Les mêmes erreurs peuvent également être rencontrées avec l'analyse des dépendances sans agent car elle suit la même méthode que l'inventaire logiciel pour collecter les données requises.

| **Error** | **Cause** | **Action** |
|--|--|--|
| **9000 :** L’état des outils VMware sur le serveur ne peut pas être détecté. | Les outils VMware ne sont peut-être pas installés sur le serveur ou la version installée est endommagée. | Assurez-vous que les outils VMware postérieurs à la version 10.2.1 sont installés et en cours d’exécution sur le serveur. |
| **9001 :** Les outils VMware ne sont pas installés sur le serveur. | Les outils VMware ne sont peut-être pas installés sur le serveur ou la version installée est endommagée. | Assurez-vous que les outils VMware postérieurs à la version 10.2.1 sont installés et en cours d’exécution sur le serveur. |
| **9002 :** Les outils VMware ne sont pas en cours d’exécution sur le serveur. | Les outils VMware ne sont peut-être pas installés sur le serveur ou la version installée est endommagée. | Assurez-vous que les outils VMware postérieurs à la version 10.2.0 sont installés et en cours d’exécution sur le serveur. |
| **9003 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Si le serveur exécute un système d’exploitation Windows ou Linux, vérifiez le type de système d’exploitation spécifié dans le serveur vCenter. |
| **9004**: Le serveur n’est pas en état d’exécution. | Le serveur est dans un état hors tension. | Vérifiez que le serveur fonctionne. |
| **9005 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Le système d’exploitation \<FetchedParameter> n’est pas pris en charge pour l’instant. |
| **9006 :** L’URL requise pour télécharger le fichier de métadonnées de détection à partir du serveur est vide. | Il peut s’agir d’un problème temporaire dû au fait que l’agent de découverte sur l’appareil ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9007 :** Le processus qui exécute le script de collecte des métadonnées est introuvable sur le serveur. | Il peut s’agir d’un problème temporaire dû au fait que l’agent de découverte sur l’appareil ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9008 :** L’état du processus en cours d’exécution sur le serveur pour collecter les métadonnées ne peut pas être récupéré. | Ce problème peut être temporaire en raison d’une erreur interne. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9009 :** Le contrôle de compte d’utilisateur (UAC) Windows empêche l’exécution d’opérations de détection sur le serveur. | Les paramètres de contrôle de compte d’utilisateur (UAC) Windows limitent la détection des applications installées à partir du serveur. | Sur le serveur concerné, réduisez le niveau des paramètres de **contrôle de compte d’utilisateur** dans le panneau de configuration. |
| **9010 :** Le serveur est hors tension. | Le serveur est dans un état hors tension. | Assurez-vous que le serveur est allumé. |
| **9011 :** Le fichier contenant les métadonnées détectées est introuvable sur le serveur. | Ce problème peut être temporaire en raison d’une erreur interne. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9012 :** Le fichier contenant les métadonnées détectées sur le serveur est vide. | Ce problème peut être temporaire en raison d’une erreur interne. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9013 :** Un nouveau profil utilisateur temporaire est créé à chaque ouverture de session sur le serveur. | Un nouveau profil utilisateur temporaire est créé à chaque ouverture de session sur le serveur. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **9014 :** Impossible de récupérer le fichier contenant les métadonnées détectées en raison d’une erreur rencontrée sur l’hôte ESXi. Code d’erreur :%ErrorCode ; Détails :%ErrorMessage | Une erreur s’est produite sur l’hôte ESXi \<HostName>. Code d’erreur :%ErrorCode ; Détails :%ErrorMessage | Assurez-vous que le port 443 est ouvert sur l’hôte ESXi sur lequel le serveur est en cours d’exécution.<br/><br/> [En savoir plus](troubleshoot-discovery.md#error-9014-httpgetrequesttoretrievefilefailed) sur la résolution de ce problème.|
| **9015 :** Le compte d’utilisateur du Serveur vCenter fourni pour la détection de serveur n’a pas les privilèges d’opérations d’invité activés. | Les privilèges requis pour les opérations d’invité n’ont pas été activés sur le compte d’utilisateur du Serveur vCenter. | Assurez-vous que le compte d’utilisateur du Serveur vCenter dispose des privilèges activés pour les **opérations d’invité** des  > **machines virtuelles**: pour interagir avec le serveur et extraire les données requises. <br/><br/> [En savoir plus](tutorial-discover-vmware.md#prepare-vmware) sur la configuration du compte vCenter Server avec les privilèges requis. |
| **9016 :** Impossible de détecter les métadonnées, car l’agent des opérations d’invité sur le serveur est obsolète. | Il se peut que les outils VMware ne soient pas installés sur le serveur ou que la version installée ne soit pas à jour. | Vérifiez que les outils VMware sont installés et exécutés sur le serveur. La version des outils VMware doit être la version 10.2.1 ou une version ultérieure. |
| **9017 :** Le fichier contenant les métadonnées détectées est introuvable sur le serveur. | Ce problème peut être temporaire en raison d’une erreur interne. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **9018 :** PowerShell n’est pas installé sur le serveur. | PowerShell est introuvable sur le serveur. | Vérifiez que PowerShell 2.0 ou une version ultérieure est installé sur le serveur. <br/><br/> [En savoir plus](troubleshoot-discovery.md#error-9018-powershellnotfound) sur la résolution de ce problème.|
| **9019 :** Impossible de détecter les métadonnées en raison d’échecs de l’opération d’invité sur le serveur. | Échec de l’opération de l’invité VMware sur le serveur. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : ```<FriendlyNameOfCredentials>.``` | Vérifiez que les informations d’identification du serveur fournies sur l’appareil sont valides et que le nom d’utilisateur fourni dans les informations d’identification est au format UPN. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **9020 :** Impossible de créer le fichier requis pour contenir les métadonnées détectées sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appareil ou une stratégie de groupe locale empêche la création d’un fichier dans le dossier requis. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : ```<FriendlyNameOfCredentials>.``` | 1. Vérifiez si les informations d’identification fournies sur l’appareil ont créé l’autorisation fichier sur le dossier \<folder path/folder name> sur le serveur. <br/>2. Si les informations d’identification fournies sur l’appareil ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **9021 :** Impossible de créer le fichier requis pour contenir les métadonnées détectées à l’emplacement approprié sur le serveur. | Les outils VMware signalent un chemin de fichier incorrect pour créer le fichier. | Assurez-vous que les outils VMware postérieurs à la version 10.2.0 sont installés et en cours d’exécution sur le serveur. |
| **9022 :** L’accès est refusé pour l’exécution de l’applet de commande Get-WmiObject sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appareil ou une stratégie de groupe locale limite l’accès à l’objet WMI. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Vérifiez si les informations d’identification fournies sur l’appareil ont crée des privilèges d’administrateur de fichier et si WMI est activé. <br/> 2. Si les informations d’identification fournies sur l’appareil ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles).<br/><br/> [En savoir plus](troubleshoot-discovery.md#error-9022-getwmiobjectaccessdenied) sur la résolution de ce problème.|
| **9023 :** Impossible d’exécuter PowerShell, car la valeur de la variable d’environnement %SystemRoot% est vide. | La valeur de la variable d’environnement %SystemRoot% est vide pour le serveur. | 1. Vérifiez si la variable d’environnement retourne une valeur vide en exécutant la commande echo %systemroot% sur le serveur concerné. <br/> Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9024 :** Impossible d’effectuer la détection, car la valeur de la variable d’environnement %TEMP% est vide. | La valeur de la variable d’environnement %TEMP% est vide pour le serveur. | 1. Vérifiez si la variable d’environnement retourne une valeur vide en exécutant la commande echo %temp% sur le serveur concerné. <br/> Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9025 :** Impossible d’effectuer la détection car PowerShell est endommagé sur le serveur. | PowerShell est endommagé sur le serveur. | Réinstallez PowerShell et vérifiez qu’il s’exécute sur le serveur concerné. |
| **9026 :** Impossible d’exécuter les opérations d’invité sur le serveur. | L’état actuel du serveur n’autorise pas l’exécution des opérations d’invité. | 1. Assurez-vous que le serveur concerné est opérationnel et en cours d’exécution.<br/> Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9027 :** Impossible de détecter les métadonnées, car l’agent des opérations d’invité sur le serveur ne fonctionne pas. | Impossible de contacter l’agent des opérations invité sur le serveur. | Assurez-vous que les outils VMware postérieurs à la version 10.2.0 sont installés et en cours d’exécution sur le serveur. |
| **9028 :** Impossible de créer le fichier requis pour contenir les métadonnées détectées car le serveur ne dispose pas de suffisamment d’espace de stockage. | L’espace de stockage est insuffisant sur le disque du serveur. | Assurez-vous que l’espace disponible est suffisant dans le stockage sur disque du serveur concerné. |
| **9029 :** Les informations d’identification fournies sur l’appareil n’ont pas les autorisations d’accès pour exécuter PowerShell. | Les informations d’identification fournies sur l’appareil n’ont pas les autorisations d’accès pour exécuter PowerShell. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Assurez-vous que les informations d’identification fournies sur l’appareil peuvent accéder à PowerShell sur le serveur.<br/> 2. Si les informations d’identification fournies sur l’appareil ne disposent pas des autorisations d’accès requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **9030 :** Impossible de collecter les métadonnées détectées, car l’hôte ESXi sur lequel le serveur est hébergé est dans état déconnecté. | L’hôte ESXi sur lequel le serveur est hébergé est dans un état déconnecté. | Vérifiez que l’hôte ESXi exécutant le serveur fonctionne et qu’il est connecté. |
| **9031 :** Impossible de collecter les métadonnées détectées car l’hôte ESXi sur lequel le serveur est hébergé ne répond pas. | L’hôte ESXi sur lequel le serveur est hébergé est dans un état non valide. | Vérifiez que l’hôte ESXi exécutant le serveur fonctionne et qu’il est connecté. |
| **9032**: Détection impossible en raison d’une erreur interne. | Ce problème provient d’une erreur interne. | Suivez les étapes sur [ce site Web](troubleshoot-discovery.md#error-9032-invalidrequest) pour corriger le problème. Si le problème persiste, ouvrez un dossier de support Microsoft. |
| **9033 :** Détection impossible car le nom d’utilisateur des informations d’identification fournies sur l’appareil pour le serveur contient des caractères non valides. | Le nom d’utilisateur des informations d’identification fournies sur l’appliance contient des caractères non valides. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | Vérifiez que le nom d’utilisateur dans les informations d’identification fournies sur l’appareil ne contient pas de caractères non valides. Revenez au gestionnaire de configuration de l’appareil pour modifier les informations d’identification. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **9034 :** Détection impossible car le nom d’utilisateur des informations d’identification fournies sur l’appareil pour le serveur n’est pas au format UPN. | Le nom d’utilisateur des informations d’identification fournies sur l’appareil n’est pas au format UPN. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | Assurez-vous que le nom d’utilisateur des informations d’identification fournies sur l’appareil est au format UPN (nom d’utilisateur principal). Revenez au gestionnaire de configuration de l’appareil pour modifier les informations d’identification. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **9035**: Détection impossible car le mode de langage PowerShell n’est pas défini correctement. | Le mode de langage PowerShell n’est pas défini sur la **Langage complet**. | Assurez-vous que le mode de langage PowerShell est défini sur **Langage complet**. |
| **9036 :** Détection impossible car le nom d’utilisateur des informations d’identification fournies sur l’appareil pour le serveur n’est pas au format UPN. | Le nom d’utilisateur des informations d’identification fournies sur l’appareil n’est pas au format UPN. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | Assurez-vous que le nom d’utilisateur des informations d’identification fournies sur l’appareil est au format UPN (nom d’utilisateur principal). Revenez au gestionnaire de configuration de l’appareil pour modifier les informations d’identification. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **9037 :** La collection de métadonnées est temporairement suspendue en raison du temps de réponse élevé du serveur. | Le serveur prend trop de temps pour répondre. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **10000 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Le système d’exploitation \<GuestOSName> n’est pas pris en charge pour l’instant. |
| **10001 :** Le script requis pour collecter les métadonnées de détection est introuvable sur le serveur. | Le script requis pour effectuer la détection a peut-être été supprimé ou retiré de l’emplacement attendu. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **10002 :** Les opérations de détection ont expiré sur le serveur. | Il peut s’agir d’un problème temporaire dû au fait que l’agent de découverte sur l’appareil ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. S’il n’est pas résolu, suivez les étapes sur [ce site Web](troubleshoot-discovery.md#error-10002-scriptexecutiontimedoutonvm) pour corriger le problème. Si le problème persiste, ouvrez un dossier de support Microsoft.|
| **10003 :** Le processus d’exécution des opérations de détection a rencontré une erreur. | Le processus d’exécution des opérations de détection s’est terminé brutalement en raison d’une erreur.| Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **10004 :** Aucune information d’identification n’a été fournie sur le type de système d’exploitation du serveur. | Les informations d’identification pour le type de système d’exploitation du serveur n’ont pas été ajoutées sur l’appareil. | 1. Assurez-vous d’ajouter les informations d’identification pour le type de système d’exploitation du serveur concerné sur l’appareil.<br/> 2. Vous pouvez désormais ajouter plusieurs informations d’identification pour le serveur sur l’appliance. |
| **10005 :** Les informations d’identification pour le serveur fournies sur l’appareil sont invalides. | Les informations d’identification fournies sur l’appareil ne sont pas valides. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : ```\<FriendlyNameOfCredentials>.``` | 1. Assurez-vous que les informations d’identification fournies sur l’appareil sont valides et que le serveur est accessible à l’aide des informations d’identification.<br/> 2. Vous pouvez désormais ajouter plusieurs informations d’identification pour le serveur sur l’appliance.<br/> 3. Revenez au gestionnaire de configuration de l’appliance pour fournir un autre ensemble d’informations d’identification ou modifier un ensemble existant. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). <br/><br/> [En savoir plus ](troubleshoot-discovery.md#error-10005-guestcredentialnotvalid) sur la résolution de ce problème.|
| **10006 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Le système d’exploitation \<GuestOSName> n’est pas pris en charge pour l’instant. |
| **10007 :** Impossible de traiter les métadonnées détectées à partir du serveur. | Une erreur s’est produite lors de l’analyse du contenu du fichier contenant les métadonnées découvertes. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **10008 :** Impossible de créer le fichier requis pour contenir les métadonnées détectées sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appareil ou une stratégie de groupe locale empêche la création d’un fichier dans le dossier requis. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : ```<FriendlyNameOfCredentials>.``` | 1. Vérifiez si les informations d’identification fournies sur l’appareil ont créé des autorisations de fichier dans le dossier \<folder path/folder name> sur le serveur.<br/> 2. Si les informations d’identification fournies sur l’appareil ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **10009 :** Impossible d’écrire les métadonnées détectées dans le fichier sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appliance ou une stratégie de groupe locale empêche d’écrire dans le fichier sur le serveur. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Vérifiez que les informations d’identification fournies sur l’appareil disposent de l’autorisation d’écriture de fichier dans le dossier <chemin de dossier/nom de dossier> sur le serveur.<br/> 2. Si les informations d’identification fournies sur l’appareil ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification tentées par Azure Migrate parmi les causes possibles). |
| **10010 :** Détection impossible car la commande %CommandName; requise pour collecter des métadonnées manque sur le serveur. | Le package contenant la commande %CommandName; n’est pas installé sur le serveur. | Assurez-vous que le package contenant la commande %CommandName; est installé sur le serveur. |
| **10011 :** Les informations d’identification fournies sur l’appareil ont été utilisées pour se connecter et se déconnecter lors d’une session interactive. | L’ouverture et la fermeture d’une session interactive forcent le déchargement des clés de registre dans le profil du compte utilisé. Cette condition rend les clés indisponibles pour une utilisation ultérieure. | Utilisez les méthodes de résolution documentées sur [ce site Web](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error). |
| **10012 :** Aucune information d’identification n’a été fournie sur l’appareil pour le serveur. | Aucune information d’identification n’a été fournie pour le serveur ou vous avez fourni des informations d’identification de domaine avec un nom de domaine incorrect sur l’appareil. [En savoir plus](troubleshoot-discovery.md#error-10012-credentialnotprovided) sur la cause de cette erreur. | 1. Assurez-vous que les informations d’identification sont fournies sur l’appareil pour le serveur et que le serveur est accessible à l’aide de ces informations d’identification. <br/> 2. Vous pouvez désormais ajouter plusieurs informations d’identification sur l’appliance de serveurs. Revenez au gestionnaire de configuration de l’appliance pour fournir les informations d’identification du serveur.|

## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>Erreur 9014 : HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>Cause
Le problème se produit lorsque l’agent de détection VMware dans l’appareil tente de télécharger le fichier de sortie contenant les données de dépendance à partir du système de fichiers du serveur via l’hôte ESXi sur lequel le serveur est hébergé.

### <a name="remediation"></a>Correction
- Vous pouvez tester la connectivité TCP à l’hôte ESXi  _(nom indiqué dans le message d’erreur)_ sur le port 443 (qui doit être ouvert sur les hôtes ESXi pour extraire les données de dépendance) de l’appareil en ouvrant PowerShell sur le serveur de l’appareil et en exécutant la commande suivante :
 
   ````
   Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
   ````

- Si la commande renvoie une connectivité réussie, accédez au **projet Azure Migrate** > **Détection et évaluation** Vue > **d’ensemble** > **Gérer** > **Appareils**, sélectionnez le nom de l’appareil et sélectionnez **Actualiser les services**.

## <a name="error-9018-powershellnotfound"></a>Erreur 9018 : PowerShellNotFound

### <a name="cause"></a>Cause
L’erreur provient généralement des serveurs qui exécutent Windows Server 2008 ou une version antérieure.

### <a name="remediation"></a>Correction
Installez la version de PowerShell requise (2.0 ou ultérieure) à cet emplacement sur le serveur : ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe. [En savoir plus](/powershell/scripting/windows-powershell/install/installing-windows-powershell) sur l’installation de PowerShell sur le Serveur Windows.

Après avoir installé la version de PowerShell requise, vérifiez si l’erreur a été résolue en suivant les étapes sur [ce site Web](troubleshoot-discovery.md#mitigation-verification-by-using-vmware-powercli).

## <a name="error-9022-getwmiobjectaccessdenied"></a>Erreur 9022 : GetWMIObjectAccessDenied

### <a name="remediation"></a>Correction
Assurez-vous que le compte d’utilisateur fourni dans l’appareil a accès à l’espace de noms et aux sous-espaces de noms WMI. Pour définir l’accès :

1.  Accédez au serveur qui signale cette erreur.
1. Recherchez et sélectionnez **Exécuter** dans le menu **Démarrer**. Dans la boîte de dialogue **Exécuter**, entrez **wmimgmt.msc** dans la zone de texte **Ouvrir**, puis sélectionnez **Entrée**.
1. La console wmimgmt s’ouvre. Vous pouvez y trouver **Contrôle WMI (local)** dans le volet gauche. Faites un clic droit dessus et sélectionnez **Propriétés** dans le menu.
1. Dans la boîte de dialogue **Propriétés du contrôle WMI (local)** , sélectionnez l’onglet **Sécurité**.
1. Sélectionnez **Sécurité** pour ouvrir la boîte de dialogue **Sécurité pour RACINE**.
1. Sélectionnez **Avancé** pour ouvrir la boîte de dialogue **Paramètres de sécurité avancée pour Racine** . 
1. Cliquez sur le bouton **Ajouter** pour ouvrir la boîte de dialogue **Entrée d’autorisation pour Racine**.
1. Cliquez sur **Sélectionner un principal** pour ouvrir la boîte de dialogue **Sélectionner les utilisateurs, les ordinateurs, les comptes de service ou les groupes**.
1. Sélectionnez les noms d’utilisateur ou les groupes auxquels vous souhaitez accorder l’accès à WMI, puis cliquez sur **OK**.
1. Assurez-vous d’accorder les autorisations d’exécution et sélectionnez **Cet espace de noms et ces sous-espaces de noms** dans la liste déroulante **S’applique à :** .
1. Cliquez sur le bouton **Appliquer** pour enregistrer les paramètres et fermez toutes les boîtes de dialogue.

Une fois que vous avez obtenu l’accès requis, vérifiez si l’erreur a été résolue en suivant les étapes sur [ce site Web](troubleshoot-discovery.md#mitigation-verification-by-using-vmware-powercli).

## <a name="error-9032-invalidrequest"></a>Erreur 9032 : InvalidRequest

### <a name="cause"></a>Cause
Cette erreur peut avoir plusieurs causes. L’une des causes est lorsque le nom d’utilisateur fourni (informations d’identification du serveur) dans le gestionnaire de configuration de l’appareil contient des caractères XML non valides. Les caractères non valides provoquent une erreur lors de l’analyse de la demande SOAP.

### <a name="remediation"></a>Correction
- Assurez-vous que le nom d’utilisateur des informations d’identification du serveur ne contient pas de caractères XML non valides et qu’il est au format username@domain.com. Ce format est communément appelé le format UPN.
- Après avoir modifié les informations d’identification sur l’appareil, vérifiez si l’erreur a été résolue en suivant les étapes sur [ce site Web](troubleshoot-discovery.md#mitigation-verification-by-using-vmware-powercli).


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>Erreur 10002 : ScriptExecutionTimedOutOnVm

### <a name="cause"></a>Cause
- Cette erreur se produit lorsque le serveur est lent ou ne répond pas et que le script exécuté pour extraire les données de dépendance commence à expirer.
- Une fois que l’agent de détection a rencontré cette erreur sur le serveur, l’appareil n’essaie plus d’analyser les dépendances sans agent sur le serveur pour éviter de surcharger le serveur qui ne répond pas.
- Par conséquent, vous continuez à voir l’erreur jusqu’à ce que vous vérifiiez le problème avec le serveur et redémarriez le service de détection.

### <a name="remediation"></a>Correction

1. Connectez-vous au serveur qui rencontre cette erreur.
1. Exécutez les commandes suivantes dans PowerShell :

   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. Si les commandes génèrent le résultat en quelques secondes, accédez au **projet Azure Migrate** > **Détection et évaluation** > **Vue d’ensemble** > **Gérer** les  > **Appareils**, sélectionnez le nom de l’appareil, puis sélectionnez **Actualiser les services** pour redémarrer le service de détection.
1. Si les commandes expirent sans donner de résultat, alors vous devez :

   - déterminer quel processus consomme beaucoup de processeurs ou de mémoires sur le serveur.
   - essayer de fournir plus de cœurs ou de mémoire à ce serveur et réexécuter les commandes.

## <a name="error-10005-guestcredentialnotvalid"></a>Erreur 10005 : GuestCredentialNotValid

### <a name="remediation"></a>Correction
- Vérifiez la validité des informations d’identification _(nom convivial fourni dans l’erreur)_ en sélectionnant **Revalider les informations d’identification** dans le gestionnaire de configuration de l’appareil.
- Assurez-vous que vous pouvez vous connecter au serveur concerné en utilisant les mêmes informations d’identification que celles fournies dans l’appareil.
- Vous pouvez essayer d’utiliser un autre compte d’utilisateur (pour le même domaine, dans le cas où le serveur est joint à un domaine) pour ce serveur au lieu d’un compte Administrateur.
- Le problème peut se produire lorsque la communication entre le catalogue global et le contrôleur de domaine est rompue. Vous pouvez vérifier ce problème en créant un nouveau compte d’utilisateur dans le contrôleur de domaine et en fournissant le même dans l’appareil. Vous devrez peut-être également redémarrer le contrôleur de domaine.
- Une fois que vous avez effectué ces mesures de correction, vérifiez si l’erreur a été résolue en suivant les étapes sur [ce site Web](troubleshoot-discovery.md#mitigation-verification-by-using-vmware-powercli).

## <a name="error-10012-credentialnotprovided"></a>Erreur 10012 : CredentialNotProvided

### <a name="cause"></a>Cause
Cette erreur se produit lorsque vous avez fourni des informations d’identification de domaine avec un nom de domaine incorrect dans le gestionnaire de configuration de l’appareil. Par exemple, si vous avez fourni des informations d’identification de domaine avec le nom d’utilisateur user@abc.com, mais fourni le nom de domaine en tant que def.com, ces informations d’identification ne seront pas tentées si le serveur est connecté à def.com et vous obtiendrez ce message d’erreur.

### <a name="remediation"></a>Correction
- Accédez au gestionnaire de configuration de l’appareil pour ajouter des informations d’identification de serveur ou pour en modifier une existante, comme expliqué dans la cause.
- Une fois que vous avez effectué ces mesures de correction, vérifiez si l’erreur a été résolue en suivant les étapes sur [ce site Web](troubleshoot-discovery.md#mitigation-verification-by-using-vmware-powercli).

## <a name="mitigation-verification-by-using-vmware-powercli"></a>Vérification de l’atténuation des risques à l’aide de VMware PowerCLI
Après avoir suivi les étapes d’atténuation pour les erreurs listées ci-dessus, vérifiez si l’atténuation a fonctionné en exécutant quelques commandes PowerCLI à partir du serveur de l’appareil. Si les commandes aboutissent, cela signifie que le problème est résolu. Dans le cas contraire, vous avez besoin de revérifier et suivre à nouveau les étapes de correction.

1. Exécutez les commandes suivantes pour configurer PowerCLI sur le serveur d’appliance :
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
1. Connectez-vous au Serveur vCenter à partir de l’appareil en spécifiant l’adresse IPdu Serveur vCenter dans la commande et les informations d’identification dans l’invite :
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
1. Connectez-vous au serveur cible à partir de l’appareil en spécifiant le nom du serveur et les informations d’identification du serveur (comme indiqué sur l’appareil) :
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
1. Pour l’inventaire logiciel, exécutez les commandes suivantes pour voir si l’opération réussit :

   - Pour les serveurs Windows :

      ```` 
        Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WMIObject win32_operatingsystem'" -GuestCredential $credential

        Invoke-VMScript -VM $vm -ScriptText "powershell.exe Get-WindowsFeature" -GuestCredential $credential
      ```` 
   - Pour les serveurs Linux :
      ````
      Invoke-VMScript -VM $vm -ScriptText "ls" -GuestCredential $credential
      ````
1. Pour l’analyse des dépendances sans agent, exécutez les commandes suivantes pour voir si l’opération réussit :

   - Pour les serveurs Windows :

      ```` 
      Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WmiObject Win32_Process'" -GuestCredential $credential 
  
      Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'netstat -ano -p tcp'" -GuestCredential $credential 
      ```` 
   - Pour les serveurs Linux :
      ````
      Invoke-VMScript -VM $vm -ScriptText "ps -o pid,cmd | grep -v ]$" -GuestCredential $credential

      Invoke-VMScript -VM $vm -ScriptText "netstat -atnp | awk '{print $4,$5,$7}'" -GuestCredential $credential
      ````
1. Après avoir vérifié que l’atténuation a fonctionné, accédez au **projet Azure Migrate** > **Détection et évaluation** > **Vue d’ensemble**  > **Gérer** > **lesAppareils**, sélectionnez le nom de l’appareil et sélectionnez **Actualiser les services** pour démarrer un nouveau cycle de détection.

## <a name="discovered-sql-server-instances-and-databases-not-in-the-portal"></a>Instances et bases de données du Serveur SQL détectées qui ne sont pas dans le portail

Une fois que vous avez lancé la détection sur l’appareil, jusqu’à 24 heures peuvent s’écouler avant que les données d’inventaire ne commencent à s’afficher dans le portail.

Si vous n’avez pas fourni les informations d’identification pour l’authentification de Windows ou du Serveur SQL sur le gestionnaire de configuration de l’appareil, ajoutez ces informations d’identification afin que l’appareil puisse les utiliser et se connecter aux instances respectives du Serveur SQL.

Une fois connecté, l’appareil recueille les données de configuration et de performances pour les instances et bases de données du Serveur SQL. Les données de configuration du Serveur SQL sont mises à jour toutes les 24 heures, et les données de performances capturées toutes les 30 secondes. Par conséquent, jusqu’à 24 heures peuvent s’écouler avant la mise à jour sur le portail de toute modification des propriétés de l’instance et des bases de données du Serveur SQL, telles que l’état de la base de données et le niveau de compatibilité.

## <a name="sql-server-instance-is-showing-up-in-a-not-connected-state-on-the-portal"></a>L’instance du Serveur SQL s’affiche dans l’état « Non connecté » sur le portail

Pour afficher les problèmes rencontrés lors de la détection des instances et des bases de données du Serveur SQL, sélectionnez l’état **non connecté** dans la colonne état de la connexion sur la page **serveurs détectés** dans votre projet.

La création d’une évaluation sur des serveurs contenant des instances SQL qui n’ont pas été détectées complètement ou qui se trouvent dans un état non connecté peut entraîner une préparation marquée comme **Inconnue**.

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>Erreurs courantes de détection des instances SQL Server et des bases de données

Azure Migrate prend en charge la découverte des instances et des bases de données du Serveur SQL s’exécutant sur des machines locales, à l’aide d’Azure Migrate : Détection et évaluation. La détection SQL est actuellement prise en charge pour VMware uniquement. Consultez le didacticiel sur la [Détection](tutorial-discover-vmware.md) pour commencer.

Les erreurs classiques de détection SQL sont résumées dans le tableau suivant.

| **Error** | **Cause** | **Action** | **Guide**
|--|--|--|--|
|**30000** : Les informations d’identification associées à ce Serveur SQL n’ont pas fonctionné.|Les informations d’identification associées manuellement ne sont pas valides ou les informations d’identification auto-associées ne peuvent plus accéder au Serveur SQL.|Ajoutez des informations d’identification pour SQL Server sur l’appliance et attendez le prochain cycle de détection SQL ou forcez l’actualisation.| - |
|**30001**: impossible de se connecter au Serveur SQL à partir de l’appareil.|1. L’appareil ne dispose pas d’une ligne de vue réseau pour le Serveur SQL.<br/>2. Le pare-feu bloque la connexion entre le Serveur SQL et l’appareil.|1. Rendez le Serveur SQL accessible à partir de l’appareil.<br/>2. Autorisez les connexions entrantes de l’appareil vers le Serveur SQL.| - | 
|**30003**: le certificat n’est pas approuvé.|Un certificat approuvé n’est pas installé sur l’ordinateur exécutant le Serveur SQL.|Veuillez configurer un certificat approuvé sur le serveur. [En savoir plus](/troubleshoot/sql/connect/error-message-when-you-connect)| [Visualiser](/troubleshoot/sql/connect/error-message-when-you-connect) |
|**30004** : Autorisations insuffisantes.|Cette erreur peut se produire en raison du manque d’autorisations nécessaires pour analyser les instances du Serveur SQL. |Accordez le rôle sysadmin aux informations d’identification et au compte fournis sur l’appareil pour détecter les instances et les bases de données du Serveur SQL. [En savoir plus](/sql/t-sql/statements/grant-server-permissions-transact-sql)| [Visualiser](/sql/t-sql/statements/grant-server-permissions-transact-sql) |
|**30005** : La connexion au Serveur SQL a échoué en raison d'un problème avec sa base de données MASTER par défaut.|Soit la base de données n'est pas valide, soit la connexion ne dispose pas de l'autorisation CONNECT sur la base de données.|Utilisez ALTER LOGIN pour définir la base de données par défaut sur la base de données MASTER.<br/>Accordez le rôle sysadmin aux informations d’identification et au compte fournis sur l’appareil pour détecter les instances et les bases de données du Serveur SQL. [En savoir plus](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error)| [Visualiser](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error) |
|**30006**: Impossible d’utiliser la connexion du Serveur SQL avec l’authentification Windows.|1. La connexion peut être une connexion du Serveur SQL mais le serveur accepte uniquement l'authentification Windows.<br/>2. Vous essayez de vous connecter à l'aide de l'authentification du Serveur SQL mais la connexion utilisée n'existe pas sur le Serveur SQL.<br/>3. La connexion peut utiliser l'authentification Windows mais elle constitue un principal Windows non reconnu. Un principal Windows non reconnu signifie que la connexion ne peut pas être vérifiée par Windows. Ce problème peut se produire si la connexion Windows provient d’un domaine non approuvé.|Si vous essayez de vous connecter avec l’authentification du Serveur SQL, vérifiez que le Serveur SQL est configuré en mode d’authentification mixte et que la connexion du Serveur SQL existe.<br/>Si vous essayez de vous connecter à l'aide de l'authentification Windows, vérifiez que vous êtes correctement connecté au domaine approprié. [En savoir plus](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)| [Visualiser](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error) |
|**30007** : le mot de passe a expiré.|le mot de passe associé à ce compte a expiré.|Le mot de passe de connexion du Serveur SQL a peut-être expiré. Réinitialiser le mot de passe ou étendre la date d’expiration du mot de passe. [En savoir plus](/sql/relational-databases/native-client/features/changing-passwords-programmatically)| [Visualiser](/sql/relational-databases/native-client/features/changing-passwords-programmatically) |
|**30008** : Le mot de passe doit être modifié.|le mot de passe du compte doit être changé.|Modifiez le mot de passe des informations d’identification fournies pour la détection SQL Server. [En savoir plus](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105))| [Visualiser](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105)) |
|**30009** : Une erreur interne s’est produite.|Une erreur interne s’est produite lors de la détection des instances et des bases de données du Serveur SQL. |Si le problème persiste, contactez le support technique Microsoft.| - |
|**30010** : Aucune base de données trouvée.|Impossible de trouver des bases de données à partir de l’instance de serveur sélectionnée.|Accordez le rôle sysadmin aux informations d’identification et au compte fournis sur l’appareil pour détecter les bases de données SQL.| - |
|**30011** : Une erreur interne s’est produite lors de l’évaluation d’une instance ou d’une base de données SQL.|Une erreur interne s’est produite lors de l’évaluation.|Si le problème persiste, contactez le support technique Microsoft.| - |
|**30012** : La connexion SQL a échoué.|1. Le pare-feu sur le serveur a refusé la connexion.<br/>2. Le service Navigateur du Serveur SQL (sqlbrowser) n’a pas démarré.<br/>3. Le Serveur SQL n’a pas répondu à la requête du client parce que le serveur n’a probablement pas été démarré.<br/>4. Le client du Serveur SQL ne peut pas se connecter au serveur. Cette erreur s'est peut-être produite parce que le serveur n'est pas configuré pour accepter des connexions distantes.<br/>5. Le client du Serveur SQL ne peut pas se connecter au serveur. Cette erreur s’est peut-être produite parce que le client ne peut pas résoudre le nom du serveur ou que le nom du serveur est incorrect.<br/>6. Les protocoles TCP ou de canaux nommés ne sont pas activés.<br/>7. Le nom d’instance du Serveur SQL spécifié n’est pas valide.|Utilisez [ce guide de l’utilisateur interactif](https://go.microsoft.com/fwlink/?linkid=2153317) pour résoudre le problème de connectivité. Veuillez patienter 24 heures après avoir donné le guide pour les données à mettre à jour dans le service. Si le problème persiste, contactez le support technique Microsoft.| [Visualiser](https://go.microsoft.com/fwlink/?linkid=2153317) |
|**30013** : Une erreur s'est produite lors de l'établissement d'une connexion à l’instance du Serveur SQL.|1. Le nom du Serveur SQL ne peut pas être résolu à partir de l’appareil.<br/>2. Le Serveur SQL n’autorise pas les connexions à distance.|Si vous pouvez exécuter une commande ping sur le Serveur SQL à partir de l’appareil, veuillez patienter 24 heures pour vérifier si ce problème est résolu automatiquement. Si ce n’est pas le cas, contactez le support technique Microsoft. [En savoir plus](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)| [Visualiser](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error) |
|**30014** : Le nom d'utilisateur ou le mot de passe n’est pas valide.| Cette erreur peut se produire en raison d’un échec d’authentification qui implique un mot de passe ou un nom d’utilisateur incorrect.|Fournissez des informations d’identification avec un nom d’utilisateur et un mot de passe valides. [En savoir plus](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)| [Visualiser](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) |
|**30015**: une erreur interne s’est produite lors de la découverte de l’instance du Serveur SQL.|Une erreur interne s’est produite lors de la détection de l’instance du Serveur SQL.|Si le problème persiste, contactez le support technique Microsoft.| - |
|**30016**: échec de la connexion à l’instance'% instance ; 'en raison d’un délai d’attente.| Cela peut avoir lieu si le pare-feu du serveur rejette la connexion.|Vérifiez si le pare-feu du Serveur SQL est configuré pour accepter les connexions. Si l’erreur persiste, contactez le support technique Microsoft. [En savoir plus](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error)| [Visualiser](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error) |
|**30017** : Une erreur interne s’est produite.|Exception non gérée.|Si le problème persiste, contactez le support technique Microsoft.| - |
|**30018** : Une erreur interne s’est produite.|Une erreur interne s’est produite lors de la collecte de données telles que la taille de la base de données Temp, la taille du fichier, etc. de l’instance SQL.|Veuillez patienter 24 heures et contactez le support Microsoft si le problème persiste.| - |
|**30019**: une erreur interne s’est produite.|Une erreur interne s’est produite lors de la collecte des métriques de performances, telles que l’utilisation de la mémoire, etc., d’une base de données ou d’une instance.|Veuillez patienter 24 heures et contactez le support Microsoft si le problème persiste.| - |

## <a name="common-web-apps-discovery-errors"></a>Erreurs courantes de découverte des applications web

Azure Migrate prend en charge la détection des applications Web ASP.NET fonctionnant sur des machines sur site, à l’aide d’Azure Migrate : Détection et évaluation. La détection d’applications web est actuellement prise en charge pour VMware uniquement. Consultez le didacticiel sur la [Détection](tutorial-discover-vmware.md) pour commencer.

Les erreurs classiques de détection d’applications Web sont résumées dans le tableau suivant.

| **Error** | **Cause** | **Action** |
|--|--|--|
| **40001 :** La fonctionnalité de la console de gestion IIS n’est pas activée. | La détection d’applications Web IIS utilise l’API de gestion incluse dans la version locale d’IIS pour lire la configuration IIS. Cette API est disponible lorsque la fonctionnalité de la console de gestion IIS d’IIS est activée. Soit cette fonctionnalité n’est pas activée, soit le système d’exploitation n’est pas une version prise en charge pour la détection des applications Web IIS.| Assurez-vous que le rôle de Serveur Web (IIS), y compris la fonctionnalité de la Console de gestion IIS (qui fait partie des outils d’administration), est activé et que le système d’exploitation du serveur est Windows Server 2008 R2 ou une version ultérieure.|
| **40002** : Impossible de se connecter au serveur à partir de l’appareil. | La connexion au serveur a échoué en raison d’informations d’identification de connexion non valides ou d’ordinateur indisponible. | Vérifiez que les informations d’identification de connexion fournies pour le serveur sont correctes et que le serveur est en ligne et qu’il accepte les connexions à distance PowerShell WS-Management. |
| **40003:** Connexion impossible au serveur en raison d’informations d’identification non valides. | Échec de la connexion au serveur en raison d’informations d’identification de connexion non valides. | Vérifiez que les informations d’identification de connexion fournies pour le serveur sont correctes et que la communication à distance WS-Management PowerShell est activée. |
| **40004:** Impossible d’accéder à la configuration IIS. | Autorisations inexistantes ou insuffisantes. | Vérifiez que les informations d’identification de l’utilisateur fournies pour le serveur sont des informations d’identification de niveau administrateur et que l’utilisateur a l’autorisation d’accéder aux fichiers sous le répertoire IIS (%windir%\System32\inetsrv) et le répertoire de configuration du serveur IIS (%windir%\system32\inetsrv\config.). |
| **40005:** Impossible d’effectuer la découverte IIS. | Impossible d’effectuer la découverte sur la machine virtuelle. Ce problème peut se produire en raison de problèmes liés à l’accès à la configuration sur le serveur. L’erreur était : « % detailedMessage ; ». | Vérifiez que les informations d’identification de l’utilisateur fournies pour le serveur sont des informations d’identification de niveau administrateur et que l’utilisateur a l’autorisation d’accéder aux fichiers sous le répertoire IIS (%windir%\System32\inetsrv) et le répertoire de configuration du serveur IIS (%windir%\system32\inetsrv\config.). Ensuite, contactez le support Microsoft avec les détails de l’erreur. |
| **40006:** Exception non classée. | Nouveau scénario d’erreur. | Contactez le support Microsoft avec les journaux et les détails de l’erreur. Les journaux se trouvent sur le serveur de l’appareil sous le chemin d’accès C:\ProgramData\ Microsoft Azure \Logs. |
| **40007:** Aucune application Web n’a été trouvée pour le serveur Web. | Le serveur Web n’a pas d’applications hébergées. | Vérifiez la configuration du serveur Web. |

## <a name="next-steps"></a>Étapes suivantes

Configurez une appliance pour [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) ou des [serveurs physiques](how-to-set-up-appliance-physical.md).
