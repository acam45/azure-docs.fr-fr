---
title: Générer des applications de stockage Azure hautement disponibles sur le stockage redondant interzone (ZRS) | Microsoft Docs
description: Le stockage redondant interzone (ZRS) offre un moyen simple de générer des applications hautement disponibles. ZRS protège contre les défaillances matérielles dans le data center et contre certains sinistres régionaux.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: b310c06f508395635976009005dd2c4db2917abc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218733"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Stockage redondant interzone (ZRS) : applications de stockage Azure hautement disponibles
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Couverture du support et disponibilité régionale
Le stockage ZRS prend actuellement en charge les types de comptes de stockage standard à usage général v2. Pour plus d’informations sur les types de comptes de stockage, voir [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

Le stockage ZRS est disponible pour les objets blob de blocs, les objets blob de pages non-disque, les fichiers, les tables et les files d’attente.

Le stockage ZRS est généralement disponible dans les régions suivantes :

- USA Est
- USA Est 2
- USA Ouest 2
- USA Centre
- Europe Nord
- Europe Ouest
- France Centre
- Asie Sud-Est

Microsoft continue d’activer le stockage ZRS dans d’autres régions Azure. Consultez la page [Mises à jour de service Azure](https://azure.microsoft.com/updates/) régulièrement pour plus d’informations sur les nouvelles régions.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Que se passe-t-il lorsqu’une zone n’est plus disponible ?
Vos données restent accessibles, même si une zone devient indisponible. Microsoft recommande de continuer à suivre les pratiques de gestion des erreurs temporaires. Ces pratiques incluent l’implémentation de stratégies de nouvelle tentative avec une interruption exponentielle.

Lorsqu’une zone n’est pas disponible, Azure procède à des mises à jour des réseaux, telles que le rejointoiement DNS. Ces mises à jour peuvent affecter votre application si vous accédez à vos données avant qu’elles soient terminées.

Le stockage ZRS ne peut pas protéger vos données contre un sinistre régional, lorsque plusieurs zones sont affectées définitivement. Au lieu de cela, le stockage ZRS contribue à la résilience de vos données s’il devient temporairement indisponible. En guise de protection face à des sinistres régionaux, Microsoft recommande l’utilisation d’un stockage géoredondant (GRS). Pour plus d’informations sur le stockage GRS, consultez [Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Conversion en réplication ZRS
Une migration vers ou à partir de LRS, GRS et RA-GRS est simple. Utilisez le portail Azure ou l’API Fournisseur de ressources de stockage pour modifier le type de redondance de votre compte. Azure va ensuite répliquer vos données en conséquence. 

Une migration de données vers ou à partir de stockage redondant interzone (ZRS) requiert une stratégie différente. Une migration de ZRS implique le déplacement physique de données à partir d’un tampon de stockage unique vers plusieurs tampons au sein d’une région.

Vous avez deux options principales pour migrer vers ou à partir de ZRS. 

- Copier ou déplacer manuellement les données vers un nouveau compte ZRS à partir d’un compte existant.
- Demander une migration dynamique.

Microsoft recommande vivement d’effectuer une migration manuelle. Une migration manuelle offre plus de souplesse qu’une migration dynamique. Avec une migration manuelle, vous contrôlez le minutage.

Pour effectuer une migration manuelle, vous disposez de plusieurs options :
- Vous pouvez vous servir d’outils existants tels qu’AzCopy, une des bibliothèques clientes de Stockage Azure, ou d’outils tiers fiables.
- Si vous êtes familiarisé avec Hadoop ou HDInsight, vous pouvez attacher les comptes (ZRS) source et de destination à votre cluster. Ensuite, vous parallélisez le processus de copie de données avec un outil tel que DistCp.
- Vous pouvez créer vos propres outils en utilisant l’une des bibliothèques clientes de Stockage Azure.

Une migration manuelle peut entraîner un temps d’arrêt de l’application. Si votre application requiert une haute disponibilité, Microsoft offre également une option de migration dynamique. Une migration dynamique est une migration sur place. 

Lors d’une migration dynamique, vous pouvez utiliser votre compte de stockage pendant le transfert de vos données entre les tampons de stockage source et de destination. Pendant le processus migration, vous bénéficiez toujours du même niveau de SLA pour la durabilité et la disponibilité que d’habitude.

Dans le cadre d’une migration dynamique, gardez à l’esprit les restrictions suivantes :

- Lorsque Microsoft gère votre demande de migration dynamique rapidement, il n’existe aucune garantie quant au moment où une migration dynamique s’achève. Si vos données doivent être migrées vers ZRS pour une certaine date, Microsoft recommande d’effectuer plutôt une migration manuelle. En général, plus la quantité de données présentes dans votre compte est élevée, plus la migration des données est longue. 
- La migration dynamique n’est prise en charge que pour des comptes de stockage utilisant une réplication LRS ou GRS. Si votre compte utilise une réplication RA-GRS, vous devez commencer par modifier le type de réplication de votre compte en LRS ou GRS avant de procéder. Cette étape intermédiaire supprime le point de terminaison secondaire en lecture seule fourni par la réplication RA-GRS avant la migration.
- Votre compte doit contenir des données.
- Vous ne pouvez migrer des données qu’au sein de la même région. Si vous souhaitez migrer vos données vers un compte de stockage ZRS situé dans une région différente de celle du compte source, vous devez effectuer une migration manuelle.
- Seuls les types de comptes de stockage standard prennent en charge la migration dynamique. Les comptes de stockage Premium doivent être migrés manuellement.

Vous pouvez demander une migration dynamique via le [portail du Support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). À partir du portail, sélectionnez le compte de stockage que vous souhaitez convertir en ZRS.
1. Sélectionnez **Nouvelle demande de support**.
2. Fournissez les informations **De base** de votre compte. Dans la section **Service**, sélectionnez **Gestion de compte de stockage**, puis la ressource que vous souhaitez convertir en ZRS. 
3. Sélectionnez **Suivant**. 
4. Dans la section **Problème**, spécifiez les valeurs suivantes : 
    - **Gravité** : laissez la valeur par défaut telle quelle.
    - **Type de problème** : sélectionnez **Migration des données**.
    - **Catégorie** : sélectionnez **Migrer vers ZRS à l’intérieur d’une région**.
    - **Titre** : tapez un titre descriptif tel que **Migration de compte ZRS**.
    - **Détails** : tapez des détails supplémentaires dans la zone **Détails**, par exemple, « Je souhaite migrer vers ZRS à partir de [LRS, GRS] dans la région ___ ». 
5. Sélectionnez **Suivant**.
6. Vérifiez que les informations de contact dans le panneau **Informations de contact** sont correctes.
7. Sélectionnez **Créer**.

Une personne du support technique vous contactera pour vous apporter l’aide dont vous aurez besoin. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS classique : une option héritée pour la redondance des objets blob de blocs
> [!NOTE]
> Le 31 mars 2021, Microsoft abandonnera et migrera les comptes ZRS classiques. Avant l’abandon de cette option, les clients du stockage ZRS classique recevront plus d’informations. 
>
> Une fois le stockage ZRS [généralement disponible](#support-coverage-and-regional-availability) dans une région, les clients ne peuvent plus créer de comptes ZRS classiques à partir du portail dans cette région. L’utilisation de Microsoft PowerShell et d’Azure CLI pour créer des comptes ZRS classiques reste une option tant que le stockage ZRS classique n’est pas abandonné.

ZRS classique réplique les données de manière asynchrone entre les centres de données d’une à deux régions. Il se peut que des données répliquées soient indisponibles jusqu’à ce que Microsoft opère le basculement vers la région secondaire. Un compte ZRS classique ne peut pas être converti en ou à partir d’un stockage LRS, GRS ou RA-GRS. De plus, les comptes ZRS classiques ne prennent en charge ni les métriques, ni la journalisation.

ZRS classique est disponible uniquement pour les **objets blob de blocs** dans les comptes de stockage à usage général v1 (GPv1). Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

Pour migrer manuellement des données de compte ZRS vers ou à partir d’un compte LRS, ZRS classique, GRS ou RA-GRS, servez-vous de l’un des outils suivants : AzCopy, Explorateur Stockage Azure, Azure PowerShell ou Azure CLI. Vous pouvez également créer votre propre solution de migration avec l’une des bibliothèques clientes de Stockage Azure.

## <a name="see-also"></a>Voir aussi
- [Réplication Azure Storage](storage-redundancy.md)
- [Stockage localement redondant (LRS) : redondance des données à faible coût pour Stockage Azure](storage-redundancy-lrs.md)
- [Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure](storage-redundancy-grs.md)