---
title: Problème lors de l’enregistrement des informations d’identification de l’administrateur lors de la configuration de l’application de galerie Azure AD
description: Comment résoudre les problèmes courants rencontrés lors de la configuration l’approvisionnement des utilisateurs pour une application déjà répertoriée dans la galerie d’applications Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77e48b8921a1ebe3affd981c068da7fa55fdf30f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275785"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problème d’enregistrement des informations d’identification d’administrateur lors de la configuration de l’approvisionnement des utilisateurs pour une application de galerie Azure Active Directory | Microsoft Docs 

Lorsque vous utilisez le portail Azure pour configurer [l’approvisionnement automatique des utilisateurs](user-provisioning.md) pour une application d’entreprise, vous pouvez rencontrer une situation où :

* Les **informations d’identification d’administrateur** saisies pour l’application sont valides et le bouton **Tester la connexion** fonctionne. Toutefois, les informations d’identification ne peuvent pas être enregistrées, et le portail Azure renvoie un message d’erreur générique.

Si l’authentification unique basée sur SAML est également configurée pour la même application, la cause la plus probable de l’erreur est que la limite de stockage par application interne d’Azure AD pour les certificats et les informations d’identification a été dépassée.

Actuellement, Azure AD a une capacité de stockage maximale de 1024 octets pour tous les certificats, les jetons secrets, les informations d’identification et les données de configuration connexes associées à une seule instance d’une application (également appelée enregistrement du principal de service dans Azure AD).

Lorsque l’authentification unique basée sur SAML est configurée, le certificat utilisé pour signer les jetons SAML est stocké ici et consomme généralement plus de 50 % de l’espace.

Les jetons secrets, les URI, les adresses e-mail de notification, les noms d’utilisateur et les mots de passe saisis pendant la configuration de l’approvisionnement d’utilisateurs peuvent être à l’origine d’un dépassement de la limite de stockage.

## <a name="how-to-work-around-this-issue"></a>Solution de contournement du problème 

Pour contourner ce problème, vous avez deux possibilités :

1. **Utiliser deux instances d’application, l’un pour l’authentification unique et l’autre pour l’approvisionnement d’utilisateurs** : par exemple, avec l’application de galerie [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md), vous pouvez ajouter LinkedIn Elevate à partir de la galerie et la configurer pour l’authentification unique. Pour l’approvisionnement, ajoutez une autre instance de LinkedIn Elevate à partir de la galerie d’applications Azure AD et nommez-la « LinkedIn Elevate (approvisionnement) ». Pour cette seconde instance, configurez [l’approvisionnement](../saas-apps/linkedinelevate-provisioning-tutorial.md), mais pas l’authentification unique. Lorsque vous utilisez cette solution de contournement, les mêmes utilisateurs et groupes doivent être [affectés](assign-user-or-group-access-portal.md) aux deux applications. 

2. **Réduire la quantité de données de configuration stockées** : toutes les données saisies dans la section [informations d’identification d’administrateur](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) de l’onglet Approvisionnement sont stockées au même endroit que le certificat SAML. S’il n’est pas possible de réduire la longueur de toutes ces données, certains champs de configuration facultatifs tels que **E-mail de notification** peuvent être supprimés.

## <a name="next-steps"></a>Étapes suivantes
[Configurer l’approvisionnement et l’annulation de l’approvisionnement pour les applications SaaS](user-provisioning.md)
