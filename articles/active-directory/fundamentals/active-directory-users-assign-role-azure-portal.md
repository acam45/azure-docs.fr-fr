---
title: Comment assigner des rôles d’annuaire à des utilisateurs avec Azure Active Directory | Microsoft Docs
description: Découvrez comment attribuer des rôles d’annuaire à des utilisateurs avec Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: b73df5ec0381e83c54c8cd9f8c0335448def0c6d
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733040"
---
# <a name="how-to-assign-roles-and-administrators-to-users-with-azure-active-directory"></a>Comment : assigner des rôles et des administrateurs à des utilisateurs avec Azure Active Directory
Si un utilisateur de votre organisation a besoin d’une autorisation pour gérer les ressources Azure Active Directory (Azure AD), vous devez assigner un rôle approprié à l’utilisateur dans Azure AD en fonction des actions qu’il souhaite effectuer et pour lesquelles il a besoin d’une permission.

Pour plus d’informations sur les rôles disponibles, consultez [Assigner des rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Pour plus d’informations sur l’ajout d’utilisateurs, consultez [Ajouter de nouveaux utilisateurs à Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Attribuer des rôles
Une méthode courante pour assigner des rôles Azure AD à un utilisateur se trouve sur la page **Rôle d’annuaire** d’un utilisateur.

Vous pouvez également assigner des rôles à l’aide de Privileged Identity Management (PIM). Pour plus d’informations sur l’utilisation de PIM, consultez [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Assigner un rôle à un utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide d’un compte Administrateur général pour le répertoire.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur auquel vous souhaitez assigner le rôle. Par exemple, _Alain Charon_.

3. Sur la page **Alain Charon - Profil**, sélectionnez **Rôle d’annuaire**.

    La page **Alain Charon - Rôle d’annuaire** s’affiche.

4. Sélectionnez **Ajouter un rôle**. Ensuite, sélectionnez le rôle à assigner à Alain (par exemple, _Administrateur d’application_), puis choisissez **Sélectionner**.

    ![Page Rôles d’annuaire affichant le rôle sélectionné](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Le rôle Administrateur d’application est assigné à Alain Charon et il apparaît sur la page **Alain Charon - Rôle d’annuaire**.

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle
Si vous devez supprimer l’attribution de rôle d’un utilisateur, vous pouvez également le faire à partir de la page **Alain Charon - Rôle d’annuaire**.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Pour supprimer une attribution de rôle d’un utilisateur

1. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis recherchez et sélectionnez l’utilisateur auquel vous souhaitez supprimer un rôle assigné. Par exemple, _Alain Charon_.

2. Sélectionnez **Rôle d’annuaire**, **Administrateur d’application**, puis sélectionnez **Supprimer le rôle**.

    ![Page Rôles d’annuaire affichant le rôle sélectionné et l’option Supprimer](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Le rôle Administrateur d’application d’Alain Charon est supprimé et il n’apparaît plus sur la page **Alain Charon - Rôle d’annuaire**.

## <a name="next-steps"></a>Étapes suivantes
- [Ajouter ou supprimer des utilisateurs](add-users-azure-active-directory.md)

- [Ajouter ou modifier les informations de profil](active-directory-users-profile-azure-portal.md)

- [Ajouter des utilisateurs invités à partir d’un autre répertoire](../b2b/what-is-b2b.md)

Vous pouvez également réaliser d’autres tâches de gestion d’utilisateur, comme l’affectation de délégués, l’utilisation de stratégies et le partage de comptes d’utilisateur. Pour en savoir plus sur les autres actions disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).


