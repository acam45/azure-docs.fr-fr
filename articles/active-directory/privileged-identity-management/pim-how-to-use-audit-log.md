---
title: Afficher l’historique d’audit des rôles d’annuaire Azure AD dans PIM | Microsoft Docs
description: Découvrez comment afficher l’historique d’audit des rôles d’annuaire Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ab5a072d845bfdbaafabe1e0e7bdce2dfce6184d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188177"
---
# <a name="view-audit-history-for-azure-ad-directory-roles-in-pim"></a>Afficher l’historique d’audit pour les rôles d’annuaire Azure AD dans PIM
Vous pouvez utiliser l’historique d’audit PIM (Privileged Identity Management) pour voir toutes les activations et toutes les affectations d’utilisateurs qui ont été effectuées durant une période donnée pour tous les rôles privilégiés. Si vous souhaitez consulter l’historique d’audit complet de l’activité dans votre client, notamment l’activité de l’administrateur, de l’utilisateur final et de la synchronisation, vous pouvez utiliser les [rapports d’accès et d’utilisation d’Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Accéder à l’historique d’audit
À partir du [portail Azure](https://portal.azure.com) et du tableau de bord, sélectionnez l’application **Azure AD Privileged Identity Management** . À partir de là, accédez à l’historique d’audit en cliquant sur **Gérer les rôles privilégiés** > **Historique d’audit** dans le tableau de bord PIM.

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
Vous pouvez trier les données par Action, puis recherchez « Activation approuvée »


## <a name="audit-history-graph"></a>Graphique de l’historique d’audit
L’historique d’audit indique le nombre total d’activations, le nombre maximal d’activations par jour et le nombre moyen d’activations par jour dans un graphique linéaire.  Vous pouvez également filtrer les données par rôle s’il existe plusieurs rôles dans l’historique d’audit.

Utilisez les boutons **temps**, **action** et **rôle** pour trier l’historique.

## <a name="audit-history-list"></a>Liste de l’historique d’audit
La liste de l’historique d’audit comprend les colonnes suivantes :

* **Demandeur** : personne qui a demandé l’activation de rôle ou la modification.  Si la valeur est « Système Azure », consultez l’historique d’audit Azure pour obtenir plus d’informations.
* **Utilisateur** : l’utilisateur qui active un rôle ou y est affecté.
* **Rôle** : le rôle affecté ou activé par l’utilisateur.
* **Action** : les mesures prises par le demandeur. Ceci peut inclure l'attribution, la non-attribution, l’activation ou la désactivation.
* **Heure** : heure à laquelle l’action s’est produite.
* **Motif** : tout texte éventuellement entré dans le champ de motif pendant l’activation.
* **Expiration** : concerne uniquement l’activation de rôles.

## <a name="filter-audit-history"></a>Filtrer l’historique d’audit
Vous pouvez filtrer les informations qui s’affichent dans l’historique d’audit en cliquant sur le bouton **Filtrer**.  Le panneau **Mettre à jour les paramètres du graphique** s’affiche.

Une fois les filtres appliqués, cliquez sur **Mettre à jour** pour filtrer les données de l’historique.  Si les données ne s’affichent pas immédiatement, actualisez la page.

### <a name="change-the-date-range"></a>Modifier la plage de dates
Utilisez les boutons **Aujourd’hui**, **Semaine dernière**, **Mois dernier** ou **Personnalisé** pour changer de période dans l’historique d’audit.

Si vous choisissez le bouton **Personnalisé**, les champs de date **De** et **À** s’affichent pour que vous puissiez spécifier une plage de dates pour l’historique.  Vous pouvez entrer les dates au format JJ/MM/AAAA ou cliquer sur l’icône de **calendrier** et sélectionner une date dans un calendrier.

### <a name="change-the-roles-included-in-the-history"></a>Modifier les rôles inclus dans l’historique
Cochez ou décochez la case **Rôle** en regard de chaque rôle à inclure dans le journal ou à exclure de celui-ci.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

- [Afficher l’historique d’audit pour les rôles de ressources Azure dans PIM](pim-resource-roles-use-the-audit-log.md)
