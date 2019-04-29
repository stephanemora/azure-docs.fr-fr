---
title: Environnement Windows virtuel Desktop Preview - Azure
description: Les éléments de base d’un environnement Windows virtuel Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870317"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Environnement de version préliminaire de Windows Virtual Desktop

Windows Virtual Desktop Preview est un service qui permet aux utilisateurs un accès facile et sécurisé à leurs postes de travail virtualisés et le RemoteApps. Cette rubrique vous indiquera un peu plus d’informations sur la structure générale de l’environnement de bureau virtuel Windows.

## <a name="tenants"></a>Locataires

Le client de bureau virtuel Windows est l’interface principale pour la gestion de votre environnement de bureau virtuel Windows. Chaque client de bureau virtuel Windows doit être associé à Azure Active Directory contenant les utilisateurs qui seront connecteront à l’environnement. Du client de bureau virtuel de Windows, vous pouvez commencer la création de pools d’hôte pour exécuter des charges de travail de vos utilisateurs.

## <a name="host-pools"></a>Pools d’hôte

Un pool de l’hôte est une collection de machines virtuelles qui s’inscrivent pour bureau virtuel Windows en tant qu’hôtes de session lorsque vous exécutez l’agent de bureau virtuel Windows. Toutes les machines virtuelles hôtes de session dans un pool de l’hôte doit provenir de la même image pour une expérience utilisateur cohérente.

Un pool de l’hôte peut être un des deux types :

- Personnel, où chaque hôte de session est affectée à des utilisateurs individuels.
- Mise en pool, où les hôtes de session peuvent accepter les connexions à partir de n’importe quel utilisateur autorisé à un groupe de l’application au sein du pool de l’hôte.

Vous pouvez définir des propriétés supplémentaires sur le pool de l’hôte pour modifier son comportement d’équilibrage de charge, le nombre de sessions chaque hôte de session peut prendre, et ce que l’utilisateur peut faire pour les hôtes de session dans le pool de l’hôte en étant connecté à leurs sessions de bureau virtuel Windows. Pour contrôler les ressources publiées aux utilisateurs via des groupes d’applications.

## <a name="app-groups"></a>Groupes d’applications

Un groupe de l’application est un regroupement logique des applications installées sur des hôtes de session dans le pool de l’hôte. Un groupe de l’application peut être un des deux types :

- RemoteApp, où les utilisateurs accéder à la RemoteApps vous sélectionner individuellement et que vous publiez sur le groupe de l’application
- Bureau, où les utilisateurs accéder à l’intégralité du bureau

Par défaut, un groupe de l’application de bureau (nommé « Groupe d’applications de bureau ») est créé automatiquement chaque fois que vous créez un pool de l’hôte. Vous pouvez supprimer ce groupe d’application à tout moment. Toutefois, vous ne peut pas créer un autre groupe de l’application de bureau dans le pool de l’hôte tant qu’un groupe de l’application de bureau existe. Pour publier RemoteApps, vous devez créer un groupe d’application de RemoteApp. Vous pouvez créer plusieurs groupes d’applications RemoteApp pour les scénarios de travail différent. Différents groupes d’applications RemoteApp peuvent également contenir RemoteApps de chevauchement.

Pour publier des ressources pour les utilisateurs, vous devez les affecter à des groupes d’applications. Lors de l’affectation d’utilisateurs à des groupes d’applications, prenez en compte les éléments suivants :

- Un utilisateur ne peut pas être affecté à un groupe de l’application de bureau et un groupe d’applications RemoteApp dans le même pool d’hôte.
- Un utilisateur peut être affecté à plusieurs groupes d’applications au sein du même pool d’hôte, et leur flux sera une accumulation des deux groupes de l’application.

## <a name="tenant-groups"></a>Groupes de client

Dans Windows de bureau virtuel, le client de bureau virtuel Windows est où la majeure partie du programme d’installation et la configuration se produit. Le client de bureau virtuel Windows contient les pools d’hôte, les groupes d’applications et les attributions du groupe d’application utilisateur. Toutefois, il peut y être certaines situations où vous devez gérer plusieurs clients de bureau virtuel Windows à la fois, en particulier si vous êtes un fournisseur de services Cloud (CSP) ou un partenaire d’hébergement. Dans ces situations, vous pouvez utiliser un groupe de client de bureau virtuel Windows personnalisé pour placer chaque les clients bureau virtuel Windows et de gérer de manière centralisée l’accès. Toutefois, si vous gérez uniquement un seul client de bureau virtuel de Windows, le concept de groupe client ne s’applique pas et vous pouvez continuer à fonctionner et de gérer votre client qui existe dans le groupe de client par défaut.

## <a name="end-users"></a>Utilisateurs finaux

Une fois que vous avez affecté des utilisateurs à leurs groupes d’applications, ils peuvent se connecter à un déploiement de bureau virtuel Windows avec tous les clients de bureau virtuel Windows.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’accès délégué et comment attribuer des rôles aux utilisateurs à [délégué l’accès dans la version préliminaire de Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Pour savoir comment configurer votre client de bureau virtuel de Windows, consultez [créer un client en version préliminaire de Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Pour savoir comment se connecter à un bureau virtuel Windows, consultez les articles suivants :

- [Se connecter au client Bureau à distance sur Windows 7 et Windows 10](connect-windows-7-and-10.md)
- [Se connecter au client web Windows Virtual Desktop Preview](connect-web.md)
