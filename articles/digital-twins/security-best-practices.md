---
title: Présentation des bonnes pratiques de sécurité relatives à Azure Digital Twins | Microsoft Docs
description: Bonnes pratiques de sécurité relatives à Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: a17fe8ed47384ed248b339643be11269b8b9cdc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092183"
---
# <a name="security-best-practices"></a>Bonnes pratiques de sécurité

La sécurité Azure Digital Twins permet un accès précis aux ressources et actions de votre graphe IoT. En effet, elle utilise une gestion granulaire des rôles et des autorisations appelée « contrôle d’accès en fonction du rôle ».

Azure Digital Twins utilise également d’autres fonctionnalités de sécurité d’Azure IoT, notamment Azure Active Directory. Pour cette raison, la configuration de votre application Azure Digital Twins implique l’utilisation de plusieurs [bonnes pratiques de sécurité Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) actuellement recommandées.

Cet article récapitule les principales bonnes pratiques à suivre.

> [!IMPORTANT]
> Passez en revue les autres ressources de sécurité (y compris celles de votre fournisseur d’appareils) afin de garantir une sécurité maximale pour votre espace IoT.

## <a name="iot-security-best-practices"></a>Meilleures pratiques relatives à la sécurité IoT

Voici certaines des pratiques les plus importantes pour sécuriser vos appareils IoT :

> [!div class="checklist"]
> * Sécurisation de tous les appareils qui sont connectés à votre espace IoT de manière à empêcher toute falsification.
> * Limitation du rôle de chaque appareil, capteur et utilisateur au sein de votre espace IoT. De cette façon, si l’espace est compromis, l’impact sera réduit.
> * Utilisation potentielle du filtrage des adresses IP d’appareil et de la restriction de port.
> * Limitation des E/S et de la bande passante des appareils pour améliorer les performances. La limitation du débit peut améliorer la sécurité en empêchant les attaques par déni de service.
> * Maintien à jour du microprogramme de l’appareil.

Voici certaines des pratiques les plus importantes pour sécuriser un espace IoT :

> [!div class="checklist"]
> * Chiffrer les données enregistrées, stockées ou persistantes.
> * Changer ou actualiser régulièrement les mots de passe ou les clés.
> * Limiter les accès et les autorisations selon le rôle (voir ci-dessous les bonnes pratiques relatives au contrôle d’accès en fonction du rôle).
> * Utiliser un chiffrement puissant. Cela implique l’utilisation de mots de passe longs, de protocoles sécurisés et d’une authentification à deux facteurs.

La supervision des ressources IoT visant à surveiller les valeurs hors norme, les menaces et les paramètres de ressources impliquant un fonctionnement inhabituel, est gérée via Azure Analytics.

> [!NOTE]
> Pour plus d’informations sur le traitement et la supervision des événements, consultez notre article sur [Événements > Routage](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Bonnes pratiques relatives à Azure Active Directory

Azure Digital Twins utilise Azure Active Directory pour authentifier les utilisateurs et protéger les applications. Azure Active Directory prend en charge l’authentification pour diverses architectures récentes, toutes basées sur des protocoles standard comme OAuth 2.0 ou OpenID Connect. Voici certaines bonnes pratiques clés permettant de sécuriser votre espace IoT avec Azure Active Directory :

> [!div class="checklist"]
> * Stocker les clés et les secrets d’application Azure Active Directory dans un emplacement sécurisé tel que [Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Utiliser un certificat émis par une [autorité de certification](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) approuvée au lieu d’utiliser des secrets d’application devant s’authentifier.
> * Limiter l’étendue d’accès OAuth 2.0 pour un jeton.
> * Vérifier la durée de validité d’un jeton, ainsi que de sa capacité à rester valide.
> * Définir des durées de validité appropriées pour les jetons.
> * Actualiser des jetons expirés.

## <a name="role-based-access-control-best-practices"></a>Bonnes pratiques relatives au contrôle d’accès en fonction du rôle

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les bonnes pratiques Azure IoT, consultez [Bonnes pratiques de sécurité IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle](./security-role-based-access-control.md).

Pour l’authentification, consultez [Authentification auprès des API](./security-authenticating-apis.md).
