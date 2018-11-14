---
title: Comprendre les meilleures pratiques de sécurité Azure Digital Twins | Microsoft Docs
description: Bonnes pratiques de sécurité relatives à Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959475"
---
# <a name="security-best-practices"></a>Meilleures pratiques en matière de sécurité

La sécurité Azure Digital Twins permet un accès précis aux ressources et actions de votre graphe IoT. Cette gestion granulaire des rôles et des autorisations est appelée contrôle d’accès en fonction du rôle.

Azure Digital Twins utilise également d’autres fonctionnalités de sécurité présentes dans Azure IoT, notamment Azure Active Directory (Azure AD). Pour cette raison, la configuration de votre application Azure Digital Twins implique l’utilisation de plusieurs [bonnes pratiques de sécurité Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) actuellement recommandées.

Cet article récapitule les principales bonnes pratiques à suivre.

> [!IMPORTANT]
> Pour garantir une sécurité maximale dans votre espace IoT, consultez d’autres ressources de sécurité (y compris celles de vos fournisseurs d’appareils).

## <a name="iot-security-best-practices"></a>Meilleures pratiques relatives à la sécurité IoT

Voici certaines des pratiques les plus importantes pour sécuriser vos appareils IoT :

> [!div class="checklist"]
> * Sécurisation de tous les appareils qui sont connectés à votre espace IoT de manière à empêcher toute falsification.
> * Limitation du rôle de chaque appareil, capteur et utilisateur au sein de votre espace IoT. Si l’un d’entre eux est compromis, l’effet en sera atténué.
> * Utilisation potentielle du filtrage des adresses IP d’appareil et de la restriction des ports.
> * Limitation des E/S et de la bande passante des appareils pour améliorer les performances. La limitation du débit peut améliorer la sécurité en empêchant les attaques par déni de service.
> * Maintien à jour du microprogramme des appareils.

Voici certaines des pratiques les plus importantes pour sécuriser un espace IoT :

> [!div class="checklist"]
> * Chiffrer les données enregistrées, stockées ou persistantes.
> * Changer ou actualiser régulièrement les mots de passe ou les clés.
> * Limiter soigneusement l’accès et les autorisations en fonction du rôle. Voir la section « Meilleures pratiques relatives au contrôle d’accès en fonction du rôle ».
> * Utiliser un chiffrement puissant. Cela implique d’utiliser des mots de passe longs, des protocoles sécurisés et l’authentification à deux facteurs.

Supervisez les ressources IoT pour identifier les valeurs hors norme, les menaces et les paramètres de ressources indiquant un fonctionnement inhabituel. Utilisez pour cela Azure Analytics.

> [!NOTE]
> Pour plus d’informations sur le traitement et le monitoring des événements, voir [Acheminer des événements et des messages avec Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Bonnes pratiques relatives à Azure Active Directory

Azure Digital Twins utilise Azure AD pour authentifier les utilisateurs et protéger les applications. Azure AD prend en charge l’authentification pour différentes architectures modernes. Elles sont toutes basées sur des protocoles standard comme OAuth 2.0 ou OpenID Connect. Voici quelques-unes des meilleures pratiques clés permettant de sécuriser un espace IoT pour Azure AD :

> [!div class="checklist"]
> * Stocker les clés et les secrets d’application Azure AD dans un emplacement sécurisé comme [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Utiliser un certificat émis par une [autorité de certification](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) approuvée pour s’authentifier, plutôt que des secrets d’application.
> * Limiter l’étendue d’accès OAuth 2.0 pour un jeton.
> * Vérifier la durée de validité d’un jeton, ainsi que de sa capacité à rester valide.
> * Définir des durées de validité appropriées pour les jetons.
> * Actualiser des jetons expirés.

## <a name="role-based-access-control-best-practices"></a>Bonnes pratiques relatives au contrôle d’accès en fonction du rôle

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les bonnes pratiques Azure IoT, consultez [Bonnes pratiques de sécurité IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, voir [Contrôle d’accès en fonction du rôle](./security-role-based-access-control.md).

Pour plus d’informations sur l’authentification, voir [S’authentifier auprès des API](./security-authenticating-apis.md).
