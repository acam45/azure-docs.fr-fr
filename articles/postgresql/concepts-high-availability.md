---
title: Concepts de haute disponibilité dans une base de données Azure pour PostgreSQL
description: Cet article fournit des informations de haute disponibilité lors de l’utilisation d’Azure Database pour PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6c39cfdd3bfe244296290c4083644ea7e1b35672
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298991"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Concepts de haute disponibilité dans une base de données Azure pour PostgreSQL
Le service Azure Database pour PostgreSQL fournit un haut niveau de disponibilité garanti. Le contrat de niveau de service (SLA) est de 99,99 % selon la disponibilité générale. Il n’existe pratiquement aucun temps d’arrêt d’application lors de l’utilisation de ce service.

## <a name="high-availability"></a>Haute disponibilité
Le modèle de haute disponibilité (HA) est basé sur des mécanismes de basculement intégrés quand une interruption au niveau du nœud se produit. Une interruption au niveau du nœud peut se produire en raison d’une défaillance matérielle ou en réponse à un déploiement de service.

À tout moment, les modifications apportées à un serveur de base de données Azure Database pour PostgreSQL se produisent dans le contexte d’une transaction. Les modifications sont enregistrées de manière synchrone dans Stockage Azure lorsque la transaction est validée. En cas d’interruption au niveau du nœud, le serveur de base de données crée automatiquement un nouveau nœud et associe le stockage de données au nouveau nœud. Toutes les connexions actives sont supprimées et aucune transaction séquentielle n’est validée.

## <a name="application-retry-logic-is-essential"></a>La logique de nouvelle tentative d’application est essentielle
Il est important que des applications de base de données PostgreSQL soient conçues pour détecter et relancer les connexions interrompues et transactions ayant échoué. Lorsque l’application lance une nouvelle tentative, la connexion de l’application est redirigée de façon transparente vers l’instance nouvellement créée, qui remplace l’instance qui a échoué.

En interne dans Azure, une passerelle est utilisée pour rediriger les connexions vers la nouvelle instance. Lors d’une interruption, tout le processus de basculement prend généralement quelques dizaines de secondes. Étant donné que la redirection est gérée en interne par la passerelle, la chaîne de connexion externe reste la même pour les applications clientes.

## <a name="scaling-up-or-down"></a>Augmentation ou diminution d’échelle
Comme pour le modèle de haute disponibilité, lorsqu’une base de données PostgreSQL Azure voit son échelle augmentée ou réduite, une nouvelle instance de serveur avec la taille spécifiée est créée. Le stockage de données existant est détaché de l’instance d’origine et attaché à la nouvelle instance.

Pendant l’opération de mise à l’échelle, une interruption se produit pour les connexions de base de données. Les applications clientes sont déconnectées, et les transactions non validées en cours sont annulées. Une fois que l’application cliente réessaie d’établir la connexion ou établit une nouvelle connexion, la passerelle dirige la connexion vers l’instance qui vient d’être dimensionnée. 

## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service à la page  [Vue d’ensemble d’Azure Database pour PostgreSQL](overview.md).
- Pour une vue d’ensemble de la logique de nouvelle tentative, consultez [Gestion des erreurs de connectivité temporaires pour Azure Database pour PostgreSQL](concepts-connectivity.md)
