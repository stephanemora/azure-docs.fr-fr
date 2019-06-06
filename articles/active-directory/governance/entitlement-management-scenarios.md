---
title: Scénarios courants dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez les principales étapes que vous devez suivre pour les scénarios courants dans la gestion des droits Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a50f4a8a63022668dac68c974f8c828c72777c9
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473138"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Scénarios courants dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il existe plusieurs façons que vous pouvez configurer la gestion des habilitations pour votre organisation. Toutefois, si vous n’êtes pas familiarisé, il est utile de comprendre les scénarios courants pour les administrateurs, les approbateurs et les demandeurs.

## <a name="administrators"></a>Administrateurs

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Je suis novice à gestion des habilitations et je veux vous aider avec mise en route

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | [Suivez le didacticiel pour créer votre premier package d’accès](entitlement-management-access-package-first.md) | [![Icône du portail Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Je souhaite permettre aux utilisateurs dans mon répertoire pour demander l’accès à des groupes, des applications ou des sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Créer un nouveau package d’accès dans un catalogue](entitlement-management-access-package-create.md#start-new-access-package) | ![Créer un package d’accès](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Ajouter des rôles de ressource pour accéder au package](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Groupes</li><li>APPLICATIONS</li><li>Sites SharePoint</li></ul> | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Ajouter une stratégie](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Pour les utilisateurs dans votre répertoire</li><li>Exiger une approbation</li><li>Paramètres d’expiration</li></ul> | ![Add policy](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Je souhaite permettre aux utilisateurs de mon répertoire de partenaires d’entreprise (y compris les utilisateurs ne sont pas encore dans mon répertoire) pour demander l’accès à des groupes, des applications ou des sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Créer un nouveau package d’accès dans un catalogue](entitlement-management-access-package-create.md#start-new-access-package) | ![Créer un package d’accès](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Ajouter des rôles de ressource pour accéder au package](entitlement-management-access-package-edit.md#add-resource-roles) | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Ajouter une stratégie pour les utilisateurs externes](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Pour les utilisateurs non inclus dans votre répertoire</li><li>Exiger une approbation</li><li>Paramètres d’expiration</li></ul> | ![Ajouter une stratégie pour les utilisateurs externes](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Envoyez le lien portail mon accès pour demander le package de l’accès à votre partenaire commercial](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Partenaire commercial peut partager un lien avec leurs utilisateurs</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Je souhaite modifier des groupes, des applications ou des sites SharePoint dans un package d’accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** Ouvrez le package de l’accès | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Ajouter ou supprimer des rôles de ressources](entitlement-management-access-package-edit.md#add-resource-roles) | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Je veux afficher qui a une assignation à des groupes, des applications ou des sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** Ouvrir un package d’accès | ![Ajouter des rôles de ressources](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Afficher les affectations](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Afficher les utilisateurs ayant accès à un package de l’accès</li><li>Vue qui l’accès utilisateur a expiré.</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Je souhaite afficher des groupes, des applications ou un utilisateur a accès à des sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | [Afficher le rapport des affectations utilisateur](entitlement-management-reports.md)<ul><li>Vue lorsqu’elles demandées et qui a approuvé</li></ul> |  |

## <a name="approvers"></a>Approbateurs

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Je souhaite approuver les demandes d’accéder à des groupes, des applications ou des sites SharePoint

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Demande d’ouverture dans le portail mon accès](entitlement-management-request-approve.md#open-request) | [![Icône de mon portail d’accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Approuver la demande d’accès](entitlement-management-request-approve.md#approve-or-deny-request) | ![Approuver l’accès](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Demandeurs

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Je souhaite afficher des groupes, des applications ou des sites SharePoint à ma disposition et demander l’accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Connectez-vous au portail mon accès](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icône de mon portail d’accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Rechercher le package de l’accès |  |
> | **3.** [Demander l’accès](entitlement-management-request-access.md#request-an-access-package) | ![Demander l'accès](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Je suis un utilisateur externe et je souhaite demander l’accès à des groupes, des applications ou des sites SharePoint avec un lien direct

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Recherchez le lien portail mon accès que vous avez reçu](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Connectez-vous au portail mon accès](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icône de mon portail d’accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Demander l’accès](entitlement-management-request-access.md#request-an-access-package) | ![Accès utilisateur externe](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Je souhaite afficher les groupes, les applications ou les sites SharePoint à que j’ai déjà accès

> [!div class="mx-tableFixed"]
> | Étapes | Exemples |
> | --- | --- |
> | **1.** [Connectez-vous au portail mon accès](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Icône de mon portail d’accès](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Afficher les packages accès active |  |

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer votre premier package d’accès](entitlement-management-access-package-first.md)
- [Modifier et gérer un package d’accès existant](entitlement-management-access-package-edit.md)
