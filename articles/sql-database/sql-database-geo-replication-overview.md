---
title: 'Azure SQL Database : groupes de basculement et géo-réplication active | Documents Microsoft'
description: Utilisez des groupes de basculement automatique avec géoréplication active et activez le basculement automatique en cas de panne.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/29/2018
ms.openlocfilehash: 3495a923683d78446e61ff0545c7d86023c14bc0
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233852"
---
# <a name="overview-active-geo-replication-and-auto-failover-groups"></a>Vue d’ensemble : géoréplication active et groupes de basculement automatique

La géoréplication active est une fonctionnalité Azure SQL Database qui vous permet de créer des réplicas lisibles de votre base de données dans le même centre de données (région) ou dans un centre de données distinct.

![Géoréplication](./media/sql-database-geo-replication-failover-portal/geo-replication.png )

La géoréplication active est conçue comme une solution de continuité d’activité qui permet à l’application d’effectuer une reprise d’activité rapide en cas de panne à l’échelle du centre de données. Si la géoréplication est activée, l’application peut lancer le basculement vers une base de données secondaire dans une autre région Azure. Jusqu’à quatre bases de données secondaires sont prises en charge dans des régions identiques ou différentes, et les bases de données secondaires peuvent également servir pour les requêtes d’accès en lecture seule. Le basculement doit être lancé manuellement par l’application ou l’utilisateur. Après le basculement, la nouvelle base de données primaire présente un point de terminaison de connexion différent.

> [!NOTE]
> La géo-réplication active est disponible pour toutes les bases de données de tous les niveaux de service, dans toutes les régions.
> La géoréplication active n’est pas disponible dans Managed Instance.

Les groupes de basculement automatique sont une extension de la géoréplication active. Ils sont conçus pour gérer le basculement de plusieurs bases de données géorépliquées en même temps, à l’aide d’un basculement lancé par l’application ou en déléguant la réalisation du basculement au service SQL Database selon des critères définis par l’utilisateur. Cette dernière option vous permet de récupérer automatiquement plusieurs bases de données associées dans une région secondaire après une défaillance grave ou un autre événement non planifié qui entraîne une perte totale ou partielle de la disponibilité du service SQL Database dans la région primaire. En outre, vous pouvez utiliser les bases de données secondaires accessibles en lecture pour décharger les charges de travail de requêtes en lecture seule. Comme les groupes de basculement automatique impliquent de nombreuses bases de données, celles-ci doivent être configurées sur le serveur primaire. Les serveurs primaire et secondaire pour les bases de données dans le groupe de basculement doivent faire partie du même abonnement. Les groupes de basculement automatique prennent en charge la réplication de toutes les bases de données du groupe vers un seul serveur secondaire situé dans une autre région,

> [!NOTE]
> Utilisez la géoréplication active si plusieurs bases de données secondaires sont nécessaires.

Si vous utilisez la géo-réplication active et que, pour une raison quelconque, votre base de données principale échoue ou nécessite simplement d’être déconnectée, vous pouvez lancer le basculement sur une de vos bases de données secondaires. Lorsque le basculement est activé pour l’une des bases de données secondaires, toutes les autres bases de données secondaires sont automatiquement liées à la nouvelle base de données primaire. Si vous utilisez des groupes de basculement automatique pour gérer la récupération de bases de données, toute panne qui affecte une ou plusieurs des bases de données du groupe donne lieu à un basculement automatique. Vous pouvez configurer la stratégie de basculement automatique qui répond le mieux aux besoins de votre application ou l’annuler et utiliser l’activation manuelle. En outre, les groupes de basculement automatique fournissent des points de terminaison d’écouteur de lecture-écriture et de lecture seule qui restent inchangés pendant les basculements. Que vous utilisiez l’activation manuelle ou automatique du basculement, ce dernier bascule toutes les bases de données secondaires du groupe en bases de données primaires. Une fois le basculement des bases de données terminé, l’enregistrement DNS est automatiquement mis à jour pour rediriger les points de terminaison vers la nouvelle région.

Vous pouvez gérer la réplication et le basculement d’une base de données individuelle ou d’un ensemble de bases de données sur un serveur ou dans un pool élastique à l’aide de la géo-réplication active. Pour ce faire, vous pouvez utiliser les éléments suivants :

- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell : base de données unique](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell : pool de bases de données élastique](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell : groupe de basculement](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL : base de données unique ou pool de bases de données élastique](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST : base de données unique](https://docs.microsoft.com/rest/api/sql/replicationlinks)
- [API REST : groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups)

Après le basculement, assurez-vous que les exigences d’authentification de votre serveur et de votre base de données sont configurées sur la nouvelle base de données primaire. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md). Cela s’applique à la fois à la géo-réplication active et aux groupes de basculement automatique.

La géoréplication active tire parti de la technologie [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de SQL Server pour répliquer de manière asynchrone les transactions validées sur la base de données primaire vers une base de données secondaire à l’aide de l’isolement de capture instantanée. Les groupes de basculement automatique fournissent la sémantique de groupe en plus de la géo-réplication active. Cependant, le même mécanisme de réplication asynchrone est utilisé. À un moment donné, la base de données secondaire peut être légèrement en retard sur la base de données primaire, mais les données secondaire ne peuvent jamais contenir de transactions partielles. Grâce à la redondance entre régions, les applications peuvent récupérer rapidement d’une perte permanente de tout ou partie d’un centre de données résultant de catastrophes naturelles, de graves erreurs humaines ou d’actes de malveillance. Vous trouverez les données d’objectif de point de récupération dans [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

La figure suivante présente un exemple de géo-réplication active configurée avec une base de données primaire située dans la région nord du centre des États-Unis, ainsi qu’une base de données secondaire située dans la région sud du centre des États-Unis.

![Relation de géo-réplication](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Comme les bases de données secondaires sont accessibles en lecture, elles peuvent être utilisées pour décharger des charges de travail en lecture seule, telles que des travaux de génération de rapports. Si vous utilisez la géo-réplication active, il est possible de créer la base de données secondaire dans la même région que la base de données primaire, mais cela n’augmente pas la résistance de l’application aux défaillances graves. Si vous utilisez des groupes de basculement automatique, votre base de données secondaire est toujours créée dans une autre région.

En plus de la récupération d’urgence, la géo-réplication active peut être utilisée dans les scénarios suivants :

- **Migration de base de données** : vous pouvez vous servir de la géo-réplication active pour migrer une base de données d’un serveur vers un autre serveur en ligne avec un temps d’arrêt minimal.
- **Mises à niveau de l’application**: vous pouvez créer une base de données secondaire supplémentaire faisant office de copie de restauration automatique lors des mises à niveau de l’application.

Pour assurer vraiment la continuité des activités, l’ajout d’une redondance de base de données entre les centres de données n’est qu’une partie de la solution. La récupération d’une application (service) de bout en bout après une défaillance catastrophique implique la récupération de tous les composants constituant le service et tous les services dépendants. En voici quelques exemples : logiciel client (il peut s’agir par exemple d’un navigateur avec un code JavaScript personnalisé), serveurs web frontaux, ressources de stockage et DNS. Il est essentiel que tous les composants résistent aux mêmes défaillances et redeviennent disponibles dans l’objectif de délai de récupération (RTO) de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre les mesures appropriées pour vous assurer que votre service fonctionne pendant le basculement des services dont il dépend. Pour plus d’informations sur la conception de solutions pour la récupération d’urgence, consultez la page [Designing Cloud Solutions for Disaster Recovery Using active geo-replication](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Conception de solutions cloud pour la récupération d’urgence à l’aide de la géo-réplication active).

## <a name="active-geo-replication-capabilities"></a>Fonctionnalités de la géo-réplication active

La fonction de géo-réplication active fournit les capacités essentielles suivantes :

- **Réplication asynchrone automatique**

 Vous pouvez créer une base de données secondaire seulement par ajout à une base de données existante. La base de données secondaire peut être créée sur n’importe quel serveur Azure SQL Database. Une fois créée, la base de données secondaire est remplie avec les données copiées à partir de la base de données primaire. Ce processus est appelé amorçage. Une fois la base de données secondaire créée et amorcée, les mises à jour de la base de données primaire sont automatiquement répliquées de manière asynchrone sur la base de données secondaire. La réplication asynchrone signifie que les transactions sont validées sur la base de données primaire avant leur réplication sur la base de données secondaire.

- **Réplicas secondaires accessibles en lecture**

  Une application peut accéder à une base de données secondaire pour des opérations en lecture seule avec des principaux de sécurité identiques ou différents de ceux utilisés pour accéder à la base de données primaire. Les bases de données secondaires fonctionnent en mode d’isolement d’instantané pour garantir que la réplication des mises à jour de la base de données primaire n’est pas retardée par des requêtes exécutées sur la base de données secondaire.

  > [!NOTE]
  > La relecture du journal est différée sur la base de données secondaire s’il existe des mises à jour de schéma sur le Principal. Cette dernière nécessite un verrouillage de schéma sur la base de données secondaire.

- **Bases de données secondaires accessibles en lecture**

  Deux bases de données secondaires ou plus améliorent la redondance et le niveau de protection de la base de données primaire et de l’application. S’il existe plusieurs bases de données secondaires, l’application reste protégée même en cas d’échec de l’une des bases de données secondaires. S’il n’existe qu’une seule base de données secondaire, en cas d’échec de celle-ci, l’application est exposée à un risque plus élevé jusqu’à la création d’une nouvelle base de données secondaire.

  > [!NOTE]
  > Si vous utilisez la géoréplication active pour créer une application mondialement distribuée et que vous avez besoin de fournir un accès en lecture seule aux données dans plus de quatre régions, vous pouvez créer la base de données secondaire d’une base de données secondaire (processus connu sous le nom de chaînage). De cette manière, vous pouvez assurer une mise à l’échelle quasiment illimitée pour la réplication de base de données. En outre, le chaînage réduit la charge de réplication qui pèse sur la base de données primaire. Le compromis est l’augmentation du décalage de réplication sur les bases de données secondaires situées aux extrémités.

- **Prise en charge des bases de données de pool élastique**

  Chaque réplica peut participer séparément à un pool élastique ou ne faire partie d’aucun pool élastique. Le choix du pool pour chaque réplica est distinct et ne dépend pas de la configuration des autres réplicas (principaux comme secondaires). Chaque pool élastique est contenu dans une seule région. Par conséquent plusieurs réplicas de la même topologie ne peuvent jamais partager un pool élastique.

- **Taille de calcul configurable de la base de données secondaire**

  Les bases de données primaire et secondaire doivent offrir le même niveau de service. Il est également vivement recommandé de créer la base de données secondaire avec la même taille de calcul (DTU ou vCore) que la base de données primaire. Une base de données secondaire avec une taille de calcul inférieure à celle de la base de données primaire risque de subir un plus grand décalage de réplication, une indisponibilité potentielle et, par conséquent, une importante perte de données après un basculement. Par conséquent, un RPO publié de 5 s ne peut pas être garanti. L’autre risque est qu’après le basculement, les performances de l’application soient impactées en raison de la capacité de calcul insuffisante de la nouvelle base de données primaire, qui doit donc être mise à niveau vers une taille de calcul supérieure. La durée de la mise à niveau dépend de la taille de la base de données. De plus, une telle mise à niveau nécessite que les bases de données primaires et secondaires soient en ligne. Elle ne peut donc pas être effectuée tant que la panne n’a pas été résolue. Si vous décidez de créer une base de données secondaire avec une taille de calcul inférieure, vous pouvez utiliser le graphique de pourcentage d’E/S du journal sur le portail Azure pour estimer la taille de calcul minimale de la base de données secondaire qui est nécessaire pour supporter la charge de réplication. Par exemple, si votre base de données primaire est P6 (1 000 DTU) et si son pourcentage d’E/S du journal est de 50 %, la base de données secondaire doit être au moins P4 (500 DTU). Vous pouvez également récupérer les données d’E/S du journal à l’aide des vues de base de données [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).  Pour plus d’informations sur les tailles de calcul SQL Database, consultez [Présentation des niveaux de service SQL Database](sql-database-service-tiers.md).

- **Basculement et restauration automatique contrôlés par l’utilisateur**

  Une base de données secondaire peut être basculée explicitement à tout moment vers le rôle primaire par l’application ou l’utilisateur. Pendant une panne réelle, l’option « non planifiée » doit être utilisée, qui promeut immédiatement une base de données secondaire en base de données primaire. Lorsque la base de données primaire en échec récupère et redevient disponible, le système marque automatiquement la base de données primaire récupérée comme base de données secondaire, et la met à jour par rapport à la nouvelle base de données primaire. En raison de la nature asynchrone de la réplication, une petite quantité de données peut être perdue lors de basculements non planifiés si une base de données primaire échoue avant la réplication des modifications les plus récentes sur la base de données secondaire. Quand une base de données primaire avec plusieurs bases de données secondaires bascule, le système reconfigure automatiquement les relations de réplication et lie les bases de données secondaires restantes à la base de données nouvellement promue comme primaire sans aucune intervention de l’utilisateur. Une fois la panne à l’origine du basculement résolue, il peut être judicieux de rétablir l’application dans la région primaire. Pour ce faire, la commande de basculement doit être appelée avec l’option « planifiée ».

- **Synchronisation des informations d’identification et des règles de pare-feu**

  Nous recommandons d’utiliser des [règles de pare-feu de base de données](sql-database-firewall-configure.md) pour les bases de données géorépliquées, de façon à ce que ces règles puissent être répliquées avec la base de données, garantissant ainsi que toutes les bases de données secondaires ont les mêmes règles de pare-feu que la base de données primaire. Cette approche évite aux clients de devoir configurer et tenir à jour manuellement les règles de pare-feu sur les serveurs hébergeant les bases de données primaire et secondaires. De même, le recours à des [utilisateurs de base de données contenus](sql-database-manage-logins.md) pour l’accès aux données garantit que les bases de données primaires et secondaires ont toujours les mêmes informations d’identification d’utilisateur afin qu’un basculement n’entraîne aucune interruption due à une discordance d’ID de connexion et de mots de passe. Avec l’ajout [d’Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), les clients peuvent gérer l’accès utilisateur aux bases de données primaires et secondaires. Cela élimine également la nécessité de gérer les informations d’identification dans l’ensemble des bases de données.

## <a name="auto-failover-group-capabilities"></a>Fonctionnalités des groupes de basculement automatique

La fonction de groupes de basculement automatique fournit une abstraction puissante de la géo-réplication active en prenant en charge la réplication et le basculement automatique au niveau du groupe. En outre, elle élimine la nécessité de modifier la chaîne de connexion SQL après le basculement en fournissant les points de terminaison d’écouteur supplémentaires.

> [!NOTE]
> Le basculement automatique n’est pas disponible dans Managed Instance.
>  

- **Groupe de basculement**

  Un ou plusieurs groupes de basculement peuvent être créés entre deux serveurs situés dans des régions différentes (serveurs principal et serveur secondaire). Chaque groupe peut inclure une ou plusieurs bases de données qui sont récupérées ensemble dans le cas où une partie ou la totalité des bases de données primaires deviennent indisponibles en raison d’une panne dans la région principale.  

- **Serveur principal**

  Serveur qui héberge les bases de données primaires dans le groupe de basculement.

- **Serveur secondaire**

  Serveur qui héberge les bases de données secondaires dans le groupe de basculement. Le serveur secondaire ne peut pas être situé dans la même région que le serveur principal.

- **Ajout de bases de données au groupe de basculement**

  Vous pouvez placer plusieurs bases de données d’un serveur ou d’un pool élastique dans le même groupe de basculement. Si vous ajoutez une base de données unique à un groupe, celui-ci crée automatiquement une base de données secondaire en utilisant la même édition et la même taille de calcul. Si la base de données primaire se trouve dans un pool élastique, la base de données secondaire est automatiquement créée dans le pool élastique avec le même nom. Si vous ajoutez une base de données qui présente déjà une base de données secondaire dans le serveur secondaire, cette géo-réplication est héritée par le groupe.

  > [!NOTE]
  > Lors de l’ajout d’une base de données qui présente déjà une base de données secondaire sur un serveur qui ne fait pas partie du groupe de basculement, une nouvelle base de données secondaire est créée sur le serveur secondaire.

- **Écouteur en lecture-écriture du groupe de basculement**

  Un enregistrement DNS CNAME formé comme suit : **&lt;nom du groupe de basculement&gt;.database.windows.net**, et qui pointe vers l’URL du serveur principal actuel. Il permet aux applications SQL en lecture-écriture de se reconnecter en toute transparence à la base de données primaire lorsqu’elle est modifiée après le basculement.

- **Écouteur en lecture seule du groupe de basculement**

  Un enregistrement DNS CNAME formé comme suit : **&lt;nom du groupe de basculement&gt;.secondary.database.windows.net**, et qui pointe vers l’URL du serveur secondaire. Il permet aux applications SQL en lecture seule de se connecter en toute transparence à la base de données secondaire à l’aide des règles d’équilibrage de charge spécifiées.

- **Stratégie de basculement automatique**

  Par défaut, le groupe de basculement est configuré avec une stratégie de basculement automatique. Le système déclenche le basculement dès que la défaillance est détectée et que la période de grâce a expiré. Le système doit vérifier que la panne ne peut pas être atténuée par l’infrastructure de haute disponibilité intégrée du service SQL Database en raison de l’échelle de l’impact. Si vous souhaitez contrôler le flux de travail de basculement à partir de l’application, vous pouvez désactiver le basculement automatique.

- **Stratégie de basculement en lecture seule**

  Par défaut, le basculement de l’écouteur en lecture seule est désactivé. Il garantit que les performances du serveur principal ne sont pas affectées lorsque le serveur secondaire est hors connexion. Toutefois, cela signifie également que les sessions en lecture seule ne seront pas en mesure de se connecter tant que le serveur secondaire n’aura pas été récupérée. Si vous ne pouvez pas tolérer des temps d’arrêt pour les sessions en lecture seule et si vous acceptez d’utiliser temporairement le serveur principal pour le trafic en lecture seule et en lecture-écriture au prix d’une dégradation potentielle des performances du serveur principal, vous pouvez activer le basculement pour l’écouteur en lecture seule. Dans ce cas, le trafic en lecture seule est automatiquement redirigé vers le serveur principal si le serveur secondaire est indisponible.  

- **Basculement manuel**

  Vous pouvez lancer manuellement le basculement à tout moment, quelle que soit la configuration du basculement automatique. Si la stratégie de basculement automatique n’est pas configurée, un basculement manuel est nécessaire pour récupérer les bases de données dans le groupe de basculement. Vous pouvez lancer un basculement forcé ou convivial (avec synchronisation complète des données). Ce dernier peut être utilisé pour relocaliser le serveur actif dans la région primaire. Une fois le basculement terminé, les enregistrements DNS sont automatiquement mis à jour pour garantir la connexion au serveur approprié.

- **Période de grâce avec perte de données**

  Comme les bases de données primaires et secondaires sont synchronisées avec la réplication asynchrone, le basculement peut entraîner une perte de données. Vous pouvez personnaliser la stratégie de basculement automatique en fonction de la tolérance de votre application aux pertes de données. En configurant la commande **GracePeriodWithDataLossHours**, vous pouvez contrôler le délai observé par le système avant d’initialiser le basculement qui est susceptible d’entraîner une perte de données.

  > [!NOTE]
  > Lorsque le système détecte que les bases de données au sein du groupe sont toujours en ligne (si la panne a affecté uniquement le plan de contrôle des services, par exemple), il active immédiatement le basculement avec synchronisation complète des données (basculement convivial), quelle que soit la valeur définie par la commande **GracePeriodWithDataLossHours**. Ce comportement garantit que les données ne seront pas perdues lors de la récupération. La période de grâce prend effet uniquement lorsqu’un basculement convivial n’est pas possible. Si la panne est résolue avant l’expiration de la période de grâce, le basculement n’est pas activé.

- **Plusieurs groupes de basculement**

  Vous pouvez configurer plusieurs groupes de basculement pour la même paire de serveurs afin de contrôler l’échelle des basculements. Chaque groupe bascule indépendamment. Si votre application mutualisée fait appel à des pools élastiques, vous pouvez utiliser cette fonctionnalité pour combiner des bases de données primaires et secondaires dans chaque pool. De cette manière, vous pouvez réduire l’impact d’une panne à la moitié seulement des locataires.

## <a name="best-practices-of-using-failover-groups-for-business-continuity"></a>Bonnes pratiques d’utilisation de groupes de basculement pour la continuité d’activité

Quand vous concevez un service en pensant à la continuité d’activité, vous devez suivre ces instructions :

- **Utiliser un ou plusieurs groupes de basculement pour gérer le basculement de plusieurs bases de données**

  Un ou plusieurs groupes de basculement peuvent être créés entre deux serveurs situés dans des régions différentes (serveurs principal et serveur secondaire). Chaque groupe peut inclure une ou plusieurs bases de données qui sont récupérées ensemble dans le cas où une partie ou la totalité des bases de données primaires deviennent indisponibles en raison d’une panne dans la région principale. Le groupe de basculement crée une base de données géo-secondaire avec le même objectif de service que la base de données primaire. Si vous ajoutez une relation de géoréplication existante au groupe de basculement, vérifiez que la base de données géosecondaire est configurée avec le même niveau de service et la même taille de calcul que la base de données primaire.

- **Utiliser un écouteur en lecture-écriture pour la charge de travail OLTP**

  Quand vous effectuez des opérations OLTP, utilisez **&lt;nom-groupe-basculement&gt;.database.windows.net**, car l’URL du serveur et les connexions sont automatiquement dirigées vers le serveur principal. L’URL ne change pas après le basculement. Notez que le basculement implique la mise à jour de l’enregistrement DNS de façon à ce que les connexions clients soient redirigées vers le nouveau serveur primaire seulement après l’actualisation du cache DNS.

- **Utiliser un écouteur en lecture seule pour une charge de travail en lecture seule**

  Si vous avez une charge de travail en lecture seule isolée logiquement et qui est tolérante à une certaine obsolescence des données, vous pouvez utiliser la base de données secondaire dans l’application. Pour les sessions en lecture seule utilisez **&lt;failover-group-name&gt;.secondary.database.windows.net** en tant qu’URL du serveur et la connexion sera automatiquement dirigée vers la base de données secondaire. Il est également recommandé d’indiquer dans la tentative de lecture de la chaîne de connexion à l’aide de **ApplicationIntent = ReadOnly**.

- **Se préparer à une dégradation des performances**

  La décision de basculement de SQL est indépendante du reste de l’application ou des autres services utilisés. L’application peut être mélangée à certains composants dans une région et d’autres composants dans une région différente. Afin d’éviter la dégradation, assurez le déploiement redondant d’applications dans la région de récupération d’urgence et suivez les instructions de cet article. Notez qu’il n’est pas obligatoire pour l’application dans la région de récupération d’urgence d’utiliser une chaîne de connexion différente.  

- **Se préparer à une perte de données**

  Si une panne est détectée, SQL déclenche automatiquement le basculement en lecture-écriture dans le cas où il n’y a à notre connaissance pas de perte de données. Le cas contraire, SQL patiente pendant le temps que vous avez défini à l’aide de la commande **GracePeriodWithDataLossHours**. Si vous avez spécifié **GracePeriodWithDataLossHours**, attendez-vous à une perte de données. En général, en cas de panne, Azure favorise la disponibilité. Si vous ne pouvez pas vous permettre de perdre des données, veillez à définir dans la commande **GracePeriodWithDataLossHours** un nombre suffisamment grand, par exemple, 24 heures.

> [!IMPORTANT]
> Les pools élastiques disposant de 800 DTU au maximum et de plus de 250 bases de données utilisant la géoréplication peuvent rencontrer des problèmes, notamment des basculements planifiés plus longs et une dégradation des performances.  Ces problèmes sont davantage susceptibles de se produire pour les charges de travail intensives en écriture, quand les points de terminaison de géoréplication sont géographiquement très éloignés, ou quand plusieurs points de terminaison secondaires sont utilisés pour chaque base de données.  Les symptômes de ces problèmes sont signalés quand le décalage de la géoréplication augmente au fil du temps.  Ce décalage peut être surveillé avec [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Si ces problèmes se produisent, l’atténuation des risques inclut l’augmentation du nombre de DTU du pool ou la réduction du nombre de bases de données géorépliquées dans ce même pool.

## <a name="failover-groups-and-network-security"></a>Groupes de basculement et sécurité réseau

Pour certaines applications, les règles de sécurité nécessitent que l’accès réseau à la couche Données soit limité à un ou plusieurs composants comme une machine virtuelle, un service web, etc. Cette exigence présente quelques défis pour la conception de la continuité d’activité et l’utilisation des groupes de basculement. Vous devez envisager les options suivantes lors de l’implémentation d’un accès restreint.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Utilisation de groupes de basculement et de règles de réseau virtuel

Si vous utilisez des [règles et points de terminaison de service de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) pour restreindre l’accès à votre base de données SQL, n’oubliez pas que chaque point de terminaison de service de réseau virtuel s’applique à une seule région Azure. Le point de terminaison ne permet pas à d’autres régions d’accepter les communications provenant du sous-réseau. Par conséquent, seules les applications client déployées dans la même région peuvent se connecter à la base de données primaire. Comme le basculement amène les sessions du client SQL à être redirigées vers un serveur dans une autre région (secondaire), ces sessions échouent si elles proviennent d’un client en dehors de cette région. Pour cette raison, la stratégie de basculement automatique ne peut pas être activée si les serveurs participants sont inclus dans les règles de réseau virtuel. Pour prendre en charge le basculement manuel, effectuez les étapes suivantes :

1. Provisionnez les copies redondantes des composants frontend de votre application (service web, machines virtuelles, etc.) dans la région secondaire
2. Configurez les [règles de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) individuellement pour les serveurs primaire et secondaire
3. Activez le [basculement frontend à l’aide d’une configuration Traffic Manager](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Lancez un basculement manuel quand la panne est détectée

Cette option est optimisée pour les applications qui requièrent une latence constante entre le frontend et la couche Données, et prend en charge la récupération quand le frontend et/ou la couche Données sont affectés par la panne.

> [!NOTE]
> Si vous utilisez **l’écouteur en lecture seule** pour équilibrer une charge de travail en lecture seule, vérifiez que cette charge de travail est exécutée dans une machine virtuelle ou une autre ressource dans la région secondaire pour qu’elle puisse se connecter à la base de données secondaire.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Utilisation de groupes de basculement et de règles de pare-feu de base de données SQL

Si votre plan de continuité d’activité nécessite un basculement à l’aide de groupes avec basculement automatique, vous pouvez restreindre l’accès à votre base de données SQL à l’aide des règles de pare-feu classiques.  Pour prendre en charge le basculement automatique, effectuez les étapes suivantes :

1. [Créez une adresse IP publique](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).
2. [Créez un équilibreur de charge public](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) et affectez-lui l’adresse IP publique.
3. [Créez un réseau virtuel et les machines virtuelles](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) pour vos composants frontend.
4. [Créez un groupe de sécurité réseau](../virtual-network/security-overview.md) et configurez les connexions entrantes.
5. Vérifiez que les connexions sortantes sont ouvertes pour Azure SQL Database à l’aide de la [balise de service](../virtual-network/security-overview.md#service-tags) « Sql ».
6. Créez une [règle de pare-feu de base de données SQL](sql-database-firewall-configure.md) pour autoriser le trafic entrant à partir de l’adresse IP publique que vous créez à l’étape 1.

Pour plus d’informations sur la configuration de l’accès sortant et l’adresse IP à utiliser dans les règles de pare-feu, consultez [Connexions sortantes de l’équilibreur de charge](../load-balancer/load-balancer-outbound-connections.md).

La configuration ci-dessus garantit que le basculement automatique ne bloque pas les connexions à partir des composants frontend et part du principe que l’application peut tolérer la plus longue latence entre le frontend et la couche Données.

> [!IMPORTANT]
> Pour garantir la continuité d’activité en cas de pannes régionales, vous devez vérifier la redondance géographique pour les composants frontend et les bases de données.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Mise à niveau ou rétrogradation d’une base de données primaire

Vous pouvez augmenter ou diminuer la taille de calcul d’une base de données primaire (au sein du même niveau de service) sans déconnecter les bases de données secondaires. Lors d’une mise à niveau, nous vous recommandons de mettre à niveau la base de données secondaire dans un premier temps, avant de mettre à niveau la base de données primaire. Lors d’une rétrogradation, inversez l’ordre : rétrogradez tout d’abord la base de données primaire, puis dans un second temps la base de données secondaire. Lorsque vous passez la base de données à un niveau de service supérieur ou inférieur, cette recommandation est appliquée.

> [!NOTE]
> Si vous avez créé une base de données secondaire dans le cadre de la configuration des groupes de basculement, il n’est pas conseillé de passer la base de données secondaire à un niveau de service inférieur. En effet, votre couche Données pourrait manquer de capacité pour traiter votre charge de travail normale après l’activation du basculement.

## <a name="preventing-the-loss-of-critical-data"></a>Prévention de la perte de données critiques

En raison de la latence élevée des réseaux étendus, la copie continue utilise un mécanisme de réplication asynchrone. La perte de certaines données reste donc inévitable en cas de défaillance. Or, pour certaines applications, une perte de données est inacceptable. Pour protéger ces mises à jour critiques, un développeur d’applications peut appeler la procédure système [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) immédiatement après la validation de la transaction. L’appel de **sp_wait_for_database_copy_sync** bloque le thread appelant jusqu’à ce que la dernière transaction validée ait été transmise à la base de données secondaire. Toutefois, il n’attend pas que les transactions transmises soient relues et validées sur la base de données secondaire. **sp_wait_for_database_copy_sync** est limité à une relation de copie continue spécifique. Tout utilisateur disposant de droits de connexion à la base de données primaire peut appeler cette procédure.

> [!NOTE]
> **sp_wait_for_database_copy_sync** empêche la perte de données après un basculement, mais il ne garantit pas la synchronisation complète pour l’accès en lecture. Le délai causé par un appel de procédure **sp_wait_for_database_copy_sync** peut être significatif et dépend de la taille du journal des transactions au moment de l’appel.

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Gestion de la géo-réplication active et des groupes de basculement par programmation

Comme indiqué plus haut, les groupes de basculement automatique et la géo-réplication active peuvent aussi être gérés par programme à l’aide d’Azure PowerShell et de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles. La géoréplication active comprend un ensemble d’API Azure Resource Manager pour la gestion, notamment [l’API REST Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) et les [applets de commande Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Ces API nécessitent l’utilisation de groupes de ressources et la prise en charge de la sécurité basée sur les rôles (RBAC). Pour plus d’informations sur l’implémentation de rôles d’accès, consultez la page sur le [contrôle d’accès en fonction du rôle Azure](../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover-using-transact-sql"></a>Gérer le basculement de base de données à l’aide de Transact-SQL

| Commande | Description |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Utilise l’argument ADD SECONDARY ON SERVER afin de créer une base de données secondaire pour une base de données existante puis lance la réplication des données |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Utilise l’argument FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS pour basculer d’une base de données secondaire à une base de données principale afin de lancer le basculement |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Utilise l’argument REMOVE SECONDARY ON SERVER pour mettre fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [sys.geo_replication_links (base de données SQL Azure)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retourne des informations concernant tous les liens de réplication existants pour chaque base de données sur le serveur logique de base de données SQL Azure. |
| [sys.dm_geo_replication_link_status (base de données SQL Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtient l’heure de la dernière réplication, le dernier décalage de la réplication et d’autres informations sur le lien de réplication pour une base de données SQL spécifique. |
| [sys.dm_operation_status (base de données SQL Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Affiche l’état de toutes les opérations de base de données, y compris l’état des liens de réplication. |
| [sp_wait_for_database_copy_sync (base de données SQL Azure)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |oblige l’application à attendre que toutes les transactions validées sont répliquées et acceptées par la base de données secondaire active. |
|  | |

### <a name="manage-sql-database-failover-using-powershell"></a>Gérer le basculement de base de données à l’aide de PowerShell

| Applet de commande | Description |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtient une ou plusieurs bases de données. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Crée une base de données secondaire pour une base de données existante puis lance la réplication des données. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Bascule d’une base de données secondaire à une base de données principale afin de lancer le basculement. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Met fin à une réplication de données entre une base de données SQL et la base de données secondaire spécifiée. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Obtient les liens de géo-réplication entre une base de données SQL Azure et un groupe de ressources ou SQL Server. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Cette commande crée un groupe de basculement et l’enregistre dans les serveurs primaire et secondaire|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | Supprime le groupe de basculement du serveur et efface toutes les bases de données secondaires incluses dans le groupe. |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Récupère la configuration du groupe de basculement. |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |Modifie la configuration du groupe de basculement. |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Déclenche le basculement du groupe de basculement vers le serveur secondaire. |
|  | |

> [!IMPORTANT]
> Pour plus d’exemples de scripts, consultez [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [Configurer et basculer une base de données regroupée à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md) et [Configurer et basculer un groupe de basculement pour une base de données unique](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="manage-sql-database-failover-using-the-rest-api"></a>Gérer le basculement de base de données à l’aide de l’API REST

| API | Description |
| --- | --- |
| [.Créer ou mettre à jour la base de données (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Crée, met à jour ou restaure une base de données principale ou secondaire. |
| [Créer ou mettre à jour l’état de la base de données](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Retourne l’état durant une opération de création. |
| [Définir la base de données secondaire comme principale (basculement planifié)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Définit la base de données réplica principale en basculant à partir de la base de données réplica principale actuelle. |
| [Définir la base de données secondaire comme principale (basculement non planifié)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Définit la base de données réplica principale en basculant à partir de la base de données réplica principale actuelle. Cette opération peut entraîner une perte de données. |
| [Obtenir un lien de réplication](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtient un liens de réplication spécifique pour une base de données SQL particulière dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. |
| [Liens de réplication - Liste par base de données](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtient tous les liens de réplication pour une base de données SQL donnée dans un partenariat de géo-réplication. Récupère les informations visibles dans la vue de catalogue sys.geo_replication_links. |
| [Supprimer un lien de réplication](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Supprime un lien de réplication de base de données. Opération impossible pendant le basculement. |
| [Créer ou mettre à jour un groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Crée ou met à jour un groupe de basculement |
| [Supprimer un groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Supprime un groupe de basculement du serveur |
| [Basculement (planifié)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Bascule du serveur principal actuel vers ce serveur. |
| [Forcer le basculement et autoriser la perte de données](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Bascule du serveur principal actuel vers ce serveur. Cette opération peut entraîner une perte de données. |
| [Get Failover Group](https://docs.microsoft.com/rest/api/sql/failovergroups/get) (Obtenir un groupe de basculement) | Obtient un groupe de basculement. |
| [Répertorier les groupes de basculement par serveur](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Répertorie les groupes de basculement d’un serveur. |
| [Mettre à jour un groupe de basculement](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Met à jour un groupe de basculement. |
|  | |

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des exemples de scripts, consultez :
  - [Configurer et basculer une base de données unique à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurer et basculer une base de données regroupée à l’aide de la géoréplication active](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Configurer et basculer un groupe de basculement pour une base de données unique](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur les exigences d’authentification pour de nouveaux serveurs et bases de données primaires, consultez [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).
