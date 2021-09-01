---
title: Résoudre les problèmes liés à l’analyse des dépendances sans agent et basée sur un agent
description: Obtenir de l’aide avec la visualisation des dépendances dans Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 7e618052a71f73c5559860fa09b495e9be2a14d9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290978"
---
# <a name="troubleshoot-dependency-visualization"></a>Résoudre les problèmes de visualisation des dépendances

Cet article vous aide à résoudre les problèmes liés à l’analyse des dépendances basée sur un agent et sans agent _(disponible uniquement pour les serveurs VMware)_ . [En savoir plus](concepts-dependency-visualization.md) sur les types de visualisation de dépendances pris en charge dans Azure Migrate.


## <a name="visualize-dependencies-for--1-hour-with-agentless-dependency-analysis"></a>Visualiser les dépendances pendant moins d’une heure avec l’analyse des dépendances sans agent

Avec l’analyse des dépendances sans agent, vous pouvez visualiser les dépendances ou les exporter dans une carte pour une durée allant jusqu’à 30 jours.

## <a name="visualized-dependencies-for--10-servers-with-agentless-dependency-analysis"></a>Visualiser les dépendances sur moins de 10 serveurs avec l’analyse des dépendances sans agent

Azure Migrate propose un modèle de Power BI que vous pouvez utiliser pour visualiser simultanément les connexions réseau de nombreux serveurs, et filtrer par processus et par serveur. [En savoir plus](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi) sur la visualisation des dépendances de plusieurs serveurs regroupés.

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>Le fichier CSV d’export des dépendances affiche « Processus inconnu » avec l’analyse de dépendance sans agent
Dans l’analyse des dépendances sans agent, les noms des processus sont capturés au mieux. Dans certains scénarios, bien que les noms des serveurs source et de destination et le port de destination soient capturés, il est impossible de déterminer les noms des processus aux deux extrémités de la dépendance. Dans ce cas, le processus est marqué comme « _Processus inconnu_ ».

## <a name="common-agentless-dependency-analysis-errors"></a>Erreurs courantes de l’analyse des dépendances sans agent

Azure Migrate prend en charge l’analyse des dépendances sans agent à l’aide de la fonction Azure Migrate : découverte et évaluation. L’analyse des dépendances sans agent est actuellement prise en charge pour VMware uniquement. [En savoir plus](how-to-create-group-machine-dependencies-agentless.md) sur la configuration requise pour l’analyse des dépendances sans agent.

La liste des erreurs d’analyse des dépendances sans agent est résumée dans le tableau ci-dessous.

> [!Note]
> Les mêmes erreurs peuvent également être rencontrées avec l’inventaire logiciel, car il suit la même méthodologie que l’analyse des dépendances sans agent pour collecter les données requises.

| **Error** | **Cause** | **Action** |
|--|--|--|
| **9000 :** L’état des outils VMware sur le serveur ne peut pas être détecté | Les outils VMware ne sont peut-être pas installés sur le serveur ou la version installée est endommagée. | Assurez-vous que les outils VMware postérieurs à la version 10.2.1 sont installés et en cours d’exécution sur le serveur. |
| **9001 :** Les outils VMware ne sont pas installés sur le serveur. | Les outils VMware ne sont peut-être pas installés sur le serveur ou la version installée est endommagée. | Assurez-vous que les outils VMware postérieurs à la version 10.2.1 sont installés et en cours d’exécution sur le serveur. |
| **9002 :** Les outils VMware ne sont pas en cours d’exécution sur le serveur. | Les outils VMware ne sont peut-être pas installés sur le serveur ou la version installée est endommagée. | Assurez-vous que les outils VMware postérieurs à la version 10.2.0 sont installés et en cours d’exécution sur le serveur. |
| **9003 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Si le serveur exécute effectivement un système d’exploitation Windows ou Linux, vérifiez le type de système d’exploitation spécifié dans vCenter Server. |
| **9004 :** Le serveur n’est pas en cours d’exécution. | Le serveur est hors tension. | Vérifiez que le serveur fonctionne. |
| **9005 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Le système d’exploitation \<FetchedParameter> n’est pas pris en charge pour l’instant. |
| **9006 :** L’URL requise pour télécharger le fichier de métadonnées de découverte à partir du serveur est vide. | Il peut s’agir d’un problème temporaire dû au fait que l’agent de découverte sur l’appliance ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9007 :** Le processus qui exécute le script de collecte des métadonnées est introuvable sur le serveur. | Il peut s’agir d’un problème temporaire dû au fait que l’agent de découverte sur l’appliance ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9008 :** L’état du processus en cours d’exécution sur le serveur pour collecter les métadonnées ne peut pas être récupéré. | Il peut s’agir d’un problème temporaire dû à une erreur interne. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9009 :** Le contrôle de compte d’utilisateur (UAC) Windows empêche l’exécution d’opérations de découverte sur le serveur. | Les paramètres de contrôle de compte d’utilisateur (UAC) Windows empêchent la détection des applications installées sur le serveur. | Sur le serveur concerné, réduisez le niveau des paramètres « contrôle de compte d’utilisateur » dans le panneau de configuration. |
| **9010 :** Le serveur est hors tension. | Le serveur est hors tension. | Assurez-vous que le serveur est allumé. |
| **9011 :** Le fichier contenant les métadonnées découvertes est introuvable sur le serveur. | Il peut s’agir d’un problème temporaire dû à une erreur interne. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9012 :** Le fichier contenant les métadonnées découvertes sur le serveur est vide. | Il peut s’agir d’un problème temporaire dû à une erreur interne. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9013 :** Un nouveau profil utilisateur temporaire est créé à chaque ouverture de session sur le serveur. | Un nouveau profil utilisateur temporaire est créé à chaque ouverture de session sur le serveur. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **9014 :** Impossible de récupérer le fichier contenant les métadonnées découvertes en raison d’une erreur rencontrée sur l’hôte ESXi.  Code d’erreur :%ErrorCode ; détails :%ErrorMessage | Une erreur s’est produite sur l’hôte ESXi \<HostName>.  Code d’erreur :%ErrorCode ; détails :%ErrorMessage | Assurez-vous que le port 443 est ouvert sur l’hôte ESXi sur lequel le serveur est en cours d’exécution.<br/><br/> [En savoir plus](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed) sur la résolution de ce problème.|
| **9015 :** Le compte d’utilisateur vCenter Server fourni pour la détection de serveur n’a pas les privilèges d’opérations d’invité activés. | Les privilèges requis pour les Opérations invité n’ont pas été activés sur le compte d’utilisateur vCenter Server. | Assurez-vous que le compte d’utilisateur vCenter Server dispose des privilèges activés pour Machines virtuelles > Opérations invité, afin d’interagir avec le serveur et d’extraire les données requises. <br/><br/> [En savoir plus](tutorial-discover-vmware.md#prepare-vmware) sur la configuration du compte vCenter Server avec les privilèges requis. |
| **9016 :** Impossible de détecter les métadonnées, car l’agent des opérations invité sur le serveur est obsolète. | Il se peut que les outils VMware ne soient pas installés sur le serveur ou que la version installée ne soit pas à jour. | Vérifiez que les outils VMware sont installés et exécutés sur le serveur. La version des outils VMware doit être la version 10.2.1 ou une version ultérieure. |
| **9017 :** Le fichier contenant les métadonnées découvertes est introuvable sur le serveur. | Il peut s’agir d’un problème temporaire dû à une erreur interne. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **9018 :** PowerShell n’est pas installé sur le serveur. | PowerShell est introuvable sur le serveur. | Vérifiez que PowerShell 2.0 ou une version ultérieure est installé sur le serveur. <br/><br/> [En savoir plus](troubleshoot-dependencies.md#error-9018-powershellnotfound) sur la résolution de ce problème.|
| **9019 :** Impossible de détecter les métadonnées en raison d’échecs d’opération invité sur le serveur. | Échec des opérations invité VMware sur le serveur. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur :<FriendlyNameOfCredentials>. | Vérifiez que les informations d’identification fournies sur l’appliance sont valides et que le nom d’utilisateur est au format UPN. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles) |
| **9020 :** Impossible de créer le fichier requis pour contenir les métadonnées découvertes sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appliance ou une stratégie de groupe locale empêche la création du fichier dans le dossier requis. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : <FriendlyNameOfCredentials>. | 1. Vérifiez si les informations d’identification fournies sur l’appliance disposent de l’autorisation de création de fichiers dans le dossier \<folder path/folder name> sur le serveur. <br/>2. Si les informations d’identification fournies sur l’appliance ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles) |
| **9021 :** Impossible de créer le fichier requis pour contenir les métadonnées découvertes à l’emplacement approprié sur le serveur. | Les outils VMware signalent un chemin de fichier incorrect pour créer le fichier. | Assurez-vous que les outils VMware postérieurs à la version 10.2.0 sont installés et en cours d’exécution sur le serveur. |
| **9022 :** L’accès est refusé pour l’exécution de la cmdlet Get-WmiObject sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appliance ou une stratégie de groupe locale empêche l’accès à l’objet WMI. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Vérifiez si les informations d’identification fournies sur l’appliance disposent des privilèges de création d’un administrateur de fichier. Vérifiez également si WMI est activé. <br/> 2. Si les informations d’identification fournies sur l’appliance ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles).<br/><br/> [En savoir plus ](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied) sur la résolution de ce problème.|
| **9023 :** Impossible d’exécuter PowerShell, car la valeur de la variable d’environnement %SystemRoot% est vide. | La valeur de la variable d’environnement %SystemRoot% est vide pour le serveur. | 1. Vérifiez si la variable d’environnement retourne une valeur vide en exécutant la commande echo %systemroot% sur le serveur concerné. <br/> 2. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9024 :** Impossible d’effectuer la détection, car la valeur de la variable d’environnement %TEMP% est vide. | La valeur de la variable d’environnement %TEMP% est vide pour le serveur. | 1. Vérifiez si la variable d’environnement retourne une valeur vide en exécutant la commande echo %temp% sur le serveur concerné. <br/> 2. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9025 :** Impossible d’effectuer la détection, PowerShell est endommagé sur le serveur. | PowerShell est endommagé sur le serveur. | Réinstallez PowerShell et vérifiez qu’il s’exécute sur le serveur concerné. |
| **9026 :** Impossible d’exécuter les opérations invité sur le serveur. | L’état actuel du serveur n’autorise pas l’exécution des opérations invité. | 1. Assurez-vous que le serveur concerné est opérationnel.<br/> 2. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **9027 :** Impossible de détecter les métadonnées, car l’agent des opérations invité sur le serveur ne fonctionne pas. | Impossible de contacter l’agent des opérations invité sur le serveur. | Assurez-vous que les outils VMware postérieurs à la version 10.2.0 sont installés et en cours d’exécution sur le serveur. |
| **9028 :** Impossible de créer le fichier requis pour contenir les métadonnées découvertes, car le serveur ne dispose pas de suffisamment d’espace de stockage. | L’espace de stockage est insuffisant sur le disque du serveur. | Assurez-vous que l’espace disponible est suffisant dans le stockage sur disque du serveur concerné. |
| **9029 :** Les informations d’identification fournies sur l’appliance n’ont pas les autorisations d’accès pour exécuter PowerShell. | Les informations d’identification fournies sur l’appliance n’ont pas les autorisations d’accès pour exécuter PowerShell. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Assurez-vous que les informations d’identification fournies sur l’appliance peuvent accéder à PowerShell sur le serveur.<br/> 2. Si les informations d’identification fournies sur l’appliance ne disposent pas des autorisations d’accès requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles) |
| **9030 :** Impossible de collecter les métadonnées découvertes, car l’hôte ESXi sur lequel le serveur est hébergé est déconnecté. | L’hôte ESXi sur lequel le serveur est hébergé est déconnecté. | Vérifiez que l’hôte ESXi exécutant le serveur fonctionne et qu’il est connecté. |
| **9031 :** Impossible de collecter les métadonnées découvertes, car l’hôte ESXi sur lequel le serveur est hébergé ne répond pas. | L’état de l’hôte ESXi sur lequel le serveur est hébergé est non valide. | Vérifiez que l’hôte ESXi exécutant le serveur fonctionne et qu’il est connecté. |
| **9032 :** Détection impossible en raison d’une erreur interne. | Ce problème provient d’une erreur interne. | Suivez les étapes indiquées [ici](troubleshoot-dependencies.md#error-9032-invalidrequest) pour résoudre ce problème. Si le problème persiste, ouvrez un dossier de support Microsoft. |
| **9033 :** Détection impossible, car le nom d’utilisateur des informations d’identification fournies sur l’appliance pour le serveur contient des caractères non valides. | Le nom d’utilisateur des informations d’identification fournies sur l’appliance contient des caractères non valides. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | Vérifiez que le nom d’utilisateur dans les informations d’identification fournies sur l’appliance ne contient pas de caractères non valides. Vous pouvez revenir au gestionnaire de configuration de l’appliance pour modifier les informations d’identification. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles). |
| **9034 :** Détection impossible, car le nom d’utilisateur des informations d’identification fournies sur l’appliance pour le serveur n’est pas au format UPN. | Le nom d’utilisateur des informations d’identification fournies sur l’appliance n’est pas au format UPN. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | Assurez-vous que le nom d’utilisateur des informations d’identification fournies sur l’appliance est au format UPN (nom d’utilisateur principal). Vous pouvez revenir au gestionnaire de configuration de l’appliance pour modifier les informations d’identification. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles). |
| **9035 :** Détection impossible, car le mode de langage PowerShell n’est pas défini correctement. | Le mode de langage PowerShell défini doit être « Full Language » (Langage complet). | Assurez-vous que le mode de langage PowerShell est défini sur « Full Language » (Langage complet). |
| **9036 :** Détection impossible, car le nom d’utilisateur des informations d’identification fournies sur l’appliance pour le serveur n’est pas au format UPN. | Le nom d’utilisateur des informations d’identification fournies sur l’appliance n’est pas au format UPN. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | Assurez-vous que le nom d’utilisateur des informations d’identification fournies sur l’appliance est au format UPN (nom d’utilisateur principal). Vous pouvez revenir au gestionnaire de configuration de l’appliance pour modifier les informations d’identification. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles). |
| **9037 :** La collection de métadonnées est temporairement suspendue en raison du temps de réponse élevé du serveur. | Le serveur prend trop de temps pour répondre. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **10000 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Le système d’exploitation \<GuestOSName> n’est pas pris en charge pour l’instant. |
| **10001 :** Le script requis pour collecter les métadonnées de découverte est introuvable sur le serveur. | Le script requis pour effectuer la détection a peut-être été supprimé ou retiré de l’emplacement attendu. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **10002 :** Les opérations de découverte ont expiré sur le serveur. | Il peut s’agir d’un problème temporaire dû au fait que l’agent de découverte sur l’appliance ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si cela ne suffit pas, suivez les étapes indiquées [ici](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm) pour résoudre le problème. Si le problème persiste, ouvrez un dossier de support Microsoft.|
| **10003 :** Le processus d’exécution de l’opération de détection a rencontré une erreur. | Le processus d’exécution de l’opération de détection s’est terminé brutalement en raison d’une erreur.| Le problème devrait être résolu automatiquement lors du cycle au cours des prochaines 24 heures. Si le problème persiste, envoyez une demande de support à Microsoft. |
| **10004 :** Aucune information d’identification n’a été fournie sur le type de système d’exploitation du serveur. | Les informations d’identification pour le type de système d’exploitation du serveur n’ont pas été ajoutées sur l’appliance. | 1. Assurez-vous d’ajouter les informations d’identification pour le type de système d’exploitation du serveur concerné sur l’appliance.<br/> 2. Vous pouvez désormais ajouter plusieurs informations d’identification pour le serveur sur l’appliance. |
| **10005 :** Les informations d’identification pour le serveur fournies sur l’appliance sont invalides. | Les informations d’identification fournies sur l’appliance ne sont pas valides. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Assurez-vous que les informations d’identification fournies sur l’appliance sont valides et que le serveur est accessible à l’aide des informations d’identification.<br/> 2. Vous pouvez désormais ajouter plusieurs informations d’identification pour le serveur sur l’appliance.<br/> 3. Revenez au gestionnaire de configuration de l’appliance pour fournir un autre ensemble d’informations d’identification ou modifier un ensemble existant. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles). <br/><br/> [En savoir plus](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid) sur la résolution de ce problème.|
| **10006 :** Le type de système d’exploitation en cours d’exécution sur le serveur n’est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Seuls les types de systèmes d’exploitation Windows et Linux sont pris en charge. Le système d’exploitation \<GuestOSName> n’est pas pris en charge pour l’instant. |
| **10007 :** Impossible de traiter les métadonnées découvertes à partir du serveur. | Une erreur s’est produite lors de l’analyse du contenu du fichier contenant les métadonnées découvertes. | Veuillez envoyer une demande de support à Microsoft pour résoudre ce problème. |
| **10008 :** Impossible de créer le fichier requis pour contenir les métadonnées découvertes sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appliance ou une stratégie de groupe locale empêche la création du fichier dans le dossier requis. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : <FriendlyNameOfCredentials>. | 1. Vérifiez si les informations d’identification fournies sur l’appliance disposent de l’autorisation de création de fichiers dans le dossier \<folder path/folder name> sur le serveur.<br/> 2. Si les informations d’identification fournies sur l’appliance ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles) |
| **10009 :** Impossible d’écrire les métadonnées détectées dans le fichier sur le serveur. | Le rôle associé aux informations d’identification fournies sur l’appliance ou une stratégie de groupe locale empêche d’écrire dans le fichier sur le serveur. Le problème s’est produit lors d’une tentative avec les informations d’identification suivantes sur le serveur : \<FriendlyNameOfCredentials>. | 1. Vérifiez que les informations d’identification fournies sur l’appliance disposent de l’autorisation d’écriture de fichier dans le dossier <chemin de dossier/nom de dossier> sur le serveur.<br/> 2. Si les informations d’identification fournies sur l’appliance ne disposent pas des autorisations requises, fournissez un autre ensemble d’informations d’identification ou modifiez un ensemble existant. (recherchez le nom convivial des informations d’identification utilisées par Azure Migrate parmi les causes possibles) |
| **10010 :** Détection impossible, car la commande %CommandName; requise pour collecter des métadonnées manque sur le serveur. | Le package contenant la commande %CommandName; n’est pas installé sur le serveur. | Assurez-vous que le package contenant la commande %CommandName; est installé sur le serveur. |
| **10011 :** Les informations d’identification fournies sur l’appliance ont été utilisées pour se connecter et se déconnecter lors d’une session interactive. | La connexion et la déconnexion interactives forcent le déchargement des clés de Registre dans le profil du compte utilisé. Cette condition rend les clés indisponibles pour une utilisation ultérieure. | Appliquez les méthodes de résolution décrites [ici](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error) |
| **10012 :** Aucune information d’identification n’a été fournie sur l’appliance pour le serveur. | Aucune information d’identification n’a été fournie pour le serveur, ou vous avez fourni des informations d’identification de domaine avec un nom de domaine incorrect sur l’appliance. [En savoir plus](troubleshoot-dependencies.md#error-10012-credentialnotprovided) sur ce qui cause cette erreur. | 1. Assurez-vous d’avoir fourni des informations d’identification pour le serveur sur l’appliance. Vérifiez également que le serveur est accessible à l’aide des informations d’identification. <br/> 2. Vous pouvez désormais ajouter plusieurs informations d’identification pour les serveurs sur l’appliance. Revenez au gestionnaire de configuration de l’appliance pour fournir les informations d’identification du serveur.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>Erreur 970 : DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>Cause
L’erreur provient généralement des serveurs Linux, si vous n’avez pas fourni les informations d’identification avec les privilèges requis sur l’appliance.

### <a name="remediation"></a>Correction
- Vérifiez que vous avez défini un compte d’utilisateur racine OU
- un compte disposant de ces autorisations sur les fichiers /bin/netstat et /bin/ls :
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE
- Pour vérifier si le compte d’utilisateur fourni sur l’appliance dispose des privilèges requis, procédez comme suit :
1. Connectez-vous au serveur sur lequel vous avez rencontré cette erreur avec le même compte d’utilisateur que celui mentionné dans le message d’erreur.
2. Exécutez les commandes suivantes dans Shell. Des messages d’erreur apparaîtront si vous n’avez pas les privilèges requis pour l’analyse des dépendances sans agent :

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
3. Définissez les autorisations requises sur les fichiers/bin/netstat et/bin/ls en exécutant les commandes suivantes :

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
4. Vous pouvez vérifier si les commandes ci-dessus ont affecté les autorisations requises au compte d’utilisateur :

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
5. Réexécutez les commandes de l’étape 2 pour que l’opération réussisse.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>Erreur 9014 : HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>Cause
Le problème se produit lorsque l’agent de découverte VMware dans l’appliance tente de télécharger le fichier de sortie contenant les données de dépendance à partir du système de fichiers du serveur via l’hôte ESXi sur lequel le serveur est hébergé.

### <a name="remediation"></a>Correction
- Vous pouvez tester la connectivité TCP à l’hôte ESXi  _(nom indiqué dans le message d’erreur)_ sur le port 443 (qui doit être ouvert sur les hôtes ESXi pour extraire les données de dépendance) de l’appliance en ouvrant PowerShell sur le serveur de l’appliance et en exécutant la commande suivante :
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````
- Si la commande renvoie la réussite de la connectivité, vous pouvez accéder au projet Azure Migrate> Découverte et évaluation>Vue d’ensemble>Gestion>Appliances, sélectionner le nom de l’appliance et sélectionnez **Actualiser les services**.

## <a name="error-9018-powershellnotfound"></a>Erreur 9018 : PowerShellNotFound

### <a name="cause"></a>Cause
L’erreur provient généralement des serveurs qui exécutent Windows Server 2008 ou une version antérieure.

### <a name="remediation"></a>Correction
Vous devez installer la version de PowerShell requise (2.0 ou ultérieure) à cet emplacement sur le serveur : ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe. [En savoir plus](/powershell/scripting/windows-powershell/install/installing-windows-powershell) sur l’installation de PowerShell dans Windows Server.

Après avoir installé la version de PowerShell requise, vous pouvez vérifier si l’erreur a été résolue en suivant les étapes indiquées [ici](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="error-9022-getwmiobjectaccessdenied"></a>Erreur 9022 : GetWMIObjectAccessDenied

### <a name="remediation"></a>Correction
Assurez-vous que le compte d’utilisateur fourni dans l’appliance a accès à l’espace de noms WMI et aux sous-espaces de noms. Vous pouvez définir l’accès en suivant ces étapes :
1.  Accédez au serveur qui signale cette erreur.
2.  Recherchez et sélectionnez « Exécuter » dans le menu Démarrer. Dans la boîte de dialogue « Exécuter », tapez wmimgmt.msc dans le champ de texte « Ouvrir : », puis appuyez sur Entrée.
3.  La console wmimgmt s’ouvre. Vous pouvez y trouver « Contrôle WMI (local) » dans le volet gauche. Faites un clic droit dessus et sélectionnez « Propriétés » dans le menu.
4.  Dans la boîte de dialogue « Propriétés du contrôle WMI (local) », cliquez sur l’onglet « Sécurité ».
5.  Dans l’onglet « Sécurité », cliquez sur le bouton « Sécurité » pour ouvrir la boîte de dialogue « Sécurité pour la racine ».
7.  Cliquez sur le bouton « Avancé » pour ouvrir la boîte de dialogue « Paramètres de sécurité avancés pour la racine ». 
8.  Cliquez sur le bouton « Ajouter » pour ouvrir la boîte de dialogue « Entrée d’autorisation pour la racine ».
9.  Cliquez sur « Sélectionner un principal » pour ouvrir la boîte de dialogue « Sélectionner les utilisateurs, les ordinateurs, les comptes de service ou les groupes ».
10. Sélectionnez les noms d’utilisateur ou les groupes auxquels vous souhaitez accorder l’accès à WMI, puis cliquez sur « OK ».
11. Assurez-vous d’accorder les autorisations d’exécution et sélectionnez « Cet espace de noms et ces sous-espaces de noms » dans la liste déroulante « s’applique à : ».
12. Cliquez sur le bouton « Appliquer »pour enregistrer les paramètres et fermez toutes les boîtes de dialogue.

Après avoir défini les accès requis, vous pouvez vérifier si l’erreur a été résolue en suivant les étapes indiquées [ici](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="error-9032-invalidrequest"></a>Erreur 9032 : InvalidRequest

### <a name="cause"></a>Cause
Il peut y avoir plusieurs raisons à ce problème. L’une des raisons est que le nom d’utilisateur fourni (informations d’identification du serveur) sur le gestionnaire de configuration de l’appliance contient des caractères XML non valides, ce qui provoque une erreur lors de l’analyse de la requête SOAP.

### <a name="remediation"></a>Correction
- Assurez-vous que le nom d’utilisateur des informations d’identification du serveur ne contient pas de caractères XML non valides et qu’il est au format username@domain.com connu sous le nom de format UPN.
- Après avoir modifié les informations d’identification sur l’appliance, vous pouvez vérifier si l’erreur a été résolue en suivant les étapes décrites [ici](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>Erreur 10002 : ScriptExecutionTimedOutOnVm

### <a name="cause"></a>Cause
- Cette erreur se produit lorsque le serveur est lent ou ne répond pas et que le script exécuté pour extraire les données de dépendance commence à expirer.
- Une fois que l’agent de découverte a rencontré cette erreur sur le serveur, l’appliance n’essaie plus d’analyser les dépendances sans agent sur le serveur pour éviter de surcharger le serveur qui ne répond pas.
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
3. Si les commandes génèrent le résultat en quelques secondes, vous pouvez accéder au projet Azure Migrate> Découverte et évaluation>Vue d’ensemble>Gérer>Appliances, sélectionner le nom de l’appliance puis **Actualiser les services** pour redémarrer le service de détection.
4. Si les commandes expirent sans donner de résultat, alors 
- Vous devez déterminer quels processus consomment beaucoup de processeurs ou de mémoires sur le serveur.
- Vous pouvez essayer de fournir plus de cœurs/de mémoire à ce serveur et réexécuter les commandes.

## <a name="error-10005-guestcredentialnotvalid"></a>Erreur 10005 : GuestCredentialNotValid

### <a name="remediation"></a>Correction
- Pour garantir la validité des informations d’identification _(nom convivial indiqué dans l’erreur)_ , cliquez sur « Valider à nouveau les informations d’identification » dans le gestionnaire de configuration de l’appliance.
- Assurez-vous que vous êtes en mesure de vous connecter au serveur concerné en utilisant les mêmes informations d’identification que celles fournies dans l’appliance.
- Vous pouvez essayer d’utiliser un autre compte d’utilisateur (pour le même domaine, dans le cas où le serveur est joint à un domaine) pour ce serveur au lieu d’un compte Administrateur.
- Le problème peut se produire lorsque la communication entre le catalogue global et le contrôleur de domaine est rompue. Vous pouvez vérifier cela en créant un nouveau compte d’utilisateur dans le contrôleur de domaine et en fournissant le même dans l’appliance. Cela peut également nécessiter le redémarrage du contrôleur de domaine.
- Une fois que vous avez effectué ces mesures de correction, vous pouvez vérifier si l’erreur a été résolue en suivant les étapes indiquées [ici](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="error-10012-credentialnotprovided"></a>Erreur 10012 : CredentialNotProvided

### <a name="cause"></a>Cause
Cette erreur se produit lorsque vous avez fourni des informations d’identification de domaine avec un nom de domaine incorrect dans le gestionnaire de configuration de l’appliance. Par exemple, si vous avez fourni des informations d’identification de domaine avec le nom d’utilisateur user@abc.com, mais que vous avez fourni le nom de domaine def.com. Ces informations d’identification ne seront pas utilisées si le serveur est connecté à def.com et ce message d’erreur apparaîtra.

### <a name="remediation"></a>Correction
- Accédez au gestionnaire de configuration de l’appliance pour ajouter des informations d’identification de serveur ou pour les modifier, comme expliqué dans la cause.
- Une fois que vous avez effectué ces mesures de correction, vous pouvez vérifier si l’erreur a été résolue en suivant les étapes indiquées [ici](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="mitigation-verification-using-vmware-powercli"></a>Vérification de l’atténuation des risques à l’aide de VMware PowerCLI

Après avoir suivi les étapes d’atténuation sur les erreurs listées ci-dessus, vous pouvez vérifier si elles ont fonctionné en exécutant quelques commandes PowerCLI à partir du serveur de l’appliance. Si les commandes aboutissent à une réussite, cela signifie que le problème est maintenant résolu, sinon vous devez de nouveau consulter et suivre les étapes de correction.

1. Exécutez les commandes suivantes pour configurer PowerCLI sur le serveur d’appliance :
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
2. Connectez-vous à vCenter Server à partir de l’appliance en spécifiant l’adresse IP vCenter Server dans la commande et les informations d’identification dans l’invite :
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
3. Connectez-vous au serveur cible à partir de l’appliance en spécifiant le nom du serveur et les informations d’identification du serveur (comme indiqué sur l’appliance) :
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
4. Pour l’analyse des dépendances sans agent, exécutez les commandes suivantes pour voir si l’opération réussit :

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
5. Après avoir vérifié que l’atténuation des risques a fonctionné, accédez au projet Azure Migrate> Découverte et évaluation>Vue d’ensemble>Gérer>Appliances, sélectionnez le nom de l’appliance puis **Actualiser les services** pour démarrer un nouveau cycle de découverte.


## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>Mon espace de travail Log Analytics n’est pas répertorié lorsque je tente de configurer l’espace de travail dans Azure Migrate pour l’analyse des dépendances basée sur un agent
Azure Migrate prend actuellement en charge la création d’un espace de travail OMS dans les régions USA Est, Asie Sud-Est et Europe Ouest. Si l’espace de travail est créé en dehors d’Azure Migrate dans une autre région, actuellement il ne peut pas être associé à un projet.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Visualisation des dépendances basée sur un agent dans Azure Government

L’analyse des dépendances basée sur les agents n’est pas prise en charge dans Azure Government. Utilisez l’analyse des dépendances sans agent _(disponible uniquement pour les serveurs VMware)_ .

## <a name="agent-based-dependencies-dont-show-after-agent-install"></a>Les dépendances basées sur un agent ne sont pas affichées après l’installation des agents

Une fois que vous avez installé les agents de visualisation des dépendances sur les machines virtuelles locales, Azure Migrate prend généralement 15 à 30 minutes pour afficher les dépendances dans le portail. Si vous avez attendu plus de 30 minutes, assurez-vous que le Microsoft Monitoring Agent (MMA) peut se connecter à l’espace de travail Log Analytics.

Pour les machines virtuelles Windows :
1. Dans le panneau de configuration, démarrez MMA.
2. Dans les **propriétés Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** , assurez-vous que l’**état** de l’espace de travail est vert.
3. Si l’état n’est pas vert, essayez de supprimer l’espace de travail et de le rajouter à MMA.

    ![État MMA](./media/troubleshoot-assessment/mma-properties.png)

Pour les machines virtuelles Linux, vérifiez que tout s’est bien passé pour les commandes d’installation MMA et Dependency Agent. Reportez-vous à cet autre [guide de résolution des problèmes](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>Systèmes d’exploitation pris en charge pour l’analyse des dépendances basée sur un agent

- **Agent MMS** : Passez en revue les systèmes d’exploitation [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) et [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) pris en charge.
- **Agent des dépendances** : les systèmes d’exploitation [Windows et Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) pris en charge.

## <a name="visualize-dependencies-for--1-hour-with-agent-based-dependency-analysis"></a>Visualiser les dépendances pendant moins d’une heure avec l’analyse des dépendances basée sur un agent

Avec l’analyse des dépendances sans agent, même si Azure Migrate vous permet de revenir à une date du mois passé, la durée maximale pendant laquelle vous pouvez visualiser les dépendances est fixée à 1 heure. Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement sur une période d’une heure. Vous pouvez aussi utiliser les journaux d’activité Azure Monitor pour [interroger les données de dépendance](./how-to-create-group-machine-dependencies.md) sur une plus longue durée.

## <a name="visualized-dependencies-for--10-servers-with-agent-based-dependency-analysis"></a>Visualiser les dépendances sur moins de 10 serveurs avec l’analyse des dépendances basée sur un agent

Dans Azure Migrate, avec l’analyse des dépendances sans agent, vous pouvez [visualiser les dépendances pour des groupes](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) contenant jusqu’à 10 machines virtuelles. Pour les groupes plus grands, nous vous recommandons de fractionner les machines virtuelles en groupes plus petits pour visualiser les dépendances.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>Les serveurs affichent « Installer l’agent » pour l’analyse des dépendances basée sur un agent

Après la migration des serveurs avec la visualisation des dépendances activée vers Azure, les serveurs peuvent afficher l’action « Installer l’agent » au lieu de « Afficher les dépendances » en raison du comportement suivant :

- Après la migration vers Azure, les serveurs locaux sont désactivés et les machines virtuelles équivalentes sont lancées dans Azure. Ces serveurs acquièrent une adresse MAC différente.
- Les serveurs peuvent également avoir une adresse IP différente, selon que vous avez ou non conservé l’adresse IP locale.
- Si les adresses MAC et IP sont différentes des adresses locales, Azure Migrate n’associe pas les serveurs locaux aux données de dépendance Service Map. Dans ce cas, l’option d’installation de l’agent s’affiche au lieu des dépendances.
- Après une migration de test vers Azure, les serveurs locaux restent allumés comme prévu. Les serveurs équivalents lancés dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que vous ne bloquiez le trafic sortant des journaux Azure Monitor provenant de ces serveurs, Azure Migrate n’associera pas les serveurs locaux aux données de dépendances Service Map, et affichera donc l’option permettant d’installer des agents au lieu de celle permettant d’afficher les dépendances.

## <a name="capture-network-traffic"></a>Capturez le trafic

Collectez les journaux du trafic comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Appuyez sur F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
3. Sélectionnez l’onglet **Réseau**, puis commencez la capture du trafic réseau :
   - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si le cercle rouge ne s’affiche pas, sélectionnez le cercle noir pour démarrer la capture.
   - Dans Microsoft Edge et Internet Explorer, l’enregistrement doit commencer automatiquement. Si ce n’est pas le cas, sélectionnez le bouton de lecture vert.
4. Essayez de reproduire l'erreur.
5. Après avoir rencontré l'erreur pendant l'enregistrement, arrêtez l'enregistrement et enregistrez une copie de l'activité enregistrée :
   - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette action compresse et exporte les journaux dans un fichier archive HTTP (har).
   - Dans Microsoft Edge ou Internet Explorer, sélectionnez l’option **Exporter le trafic capturé**. Cette action compresse et exporte le journal.
6. Sélectionnez l’onglet **Console** pour vérifier la présence d’avertissements ou d’erreurs. Pour enregistrer le journal de la console :
   - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
   - Dans Microsoft Edge ou Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**.
7. Fermez les outils de développement.


## <a name="next-steps"></a>Étapes suivantes

[Créer](how-to-create-assessment.md) ou [personnaliser](how-to-modify-assessment.md) une évaluation.