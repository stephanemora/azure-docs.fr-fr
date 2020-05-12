---
title: Outil de gestion Windows Virtual Desktop - Azure
description: Résoudre les problèmes rencontrés avec l’outil de gestion Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb9206d2c2c15c036b0cf1184b79ed126966eb3b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614074"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Résoudre les problèmes de l’outil de gestion Windows Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à la version Automne 2019 qui ne prend pas en charge les objets Azure Resource Manager Windows Virtual Desktop.

Cet article décrit les problèmes qui peuvent survenir lors du déploiement de l'outil de gestion Windows Virtual Desktop et explique comment les résoudre.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Erreur : Les services de l'outil de gestion sont configurés, mais l'installation automatisée échoue

Si vous avez réussi à configurer les services pour l'outil de gestion mais que l'installation automatisée échoue, vous verrez ce message d'erreur :

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Cela signifie généralement l'une des deux choses suivantes :

- L'utilisateur dispose des autorisations de propriétaire sur son abonnement et l'administration globale au niveau du locataire, mais il ne peut pas se connecter à Azure.
- L'authentification multifacteur est activée dans les paramètres du compte de l'utilisateur.

Pour résoudre ce problème :

1. Assurez-vous que l'utilisateur que vous avez créé pour le nom d'utilisateur principal Azure Active Directory possède le niveau d'abonnement « Contributeur ».
2. Connectez-vous à <portal.azure.com> avec le compte UPN pour vérifier les paramètres du compte et vérifiez que l'authentification multifacteur n'est pas active. Si elle est activée, désactivez-la.
3. Visitez la page de consentement de Windows Virtual Desktop et vérifiez que le serveur et les applications clientes ont donné leur consentent.
4. Passez en revue le tutoriel [Déployer un outil de gestion](manage-resources-using-ui.md) si le problème persiste, puis redéployez l'outil.

## <a name="error-job-with-specified-id-already-exists"></a>Erreur : La tâche avec l'ID indiqué existe déjà. ID de tâche

Si votre utilisateur voit le message d'erreur « La tâche avec l'ID indiqué existe déjà. ID de tâche », cela signifie qu'il n'a pas fourni de nom unique dans le paramètre « Nom de l’application » lors du déploiement du modèle.

Pour y remédier, redéployez l'outil de gestion en renseignant le paramètre « Nom de l'application ».

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Demande de consentement retardée à l'ouverture de l'outil de gestion

Lorsque vous déployez l'outil de gestion, l'invite de consentement risque de ne pas s'ouvrir immédiatement. Cela signifie que le chargement du service d'application Web Azure prend plus de temps que d'habitude. L'invite devrait apparaître après le chargement d'Azure Web.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>L'utilisateur ne peut pas déployer l'outil de gestion dans la région Est des États-Unis

Si un client choisit la région Est des États-Unis, il ne peut pas déployer l'outil de gestion.

Pour y remédier, déployez l'outil de gestion dans une autre région. Redéployer l'outil dans une autre région ne devrait pas affecter l'expérience utilisateur.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les procédures d’escalade, consultez [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview-2019.md).
- Découvrez comment signaler des problèmes rencontrés avec les outils Windows Virtual Desktop dans la section [Modèles ARM pour les services Bureau à distance](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Pour apprendre à déployer l'outil de gestion, voir [Déployer un outil de gestion](manage-resources-using-ui.md).