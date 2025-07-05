```
org.hibernate.boot.internal.StandardEntityNotFoundDelegate.handleEntityNotFound(StandardEntityNotFoundDelegate.java:28)
[7/5/25, 0:03:12:530 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.event.internal.DefaultLoadEventListener.load(DefaultLoadEventListener.java:216)
[7/5/25, 0:03:12:530 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.event.internal.DefaultLoadEventListener.proxyOrLoad(DefaultLoadEventListener.java:327)
[7/5/25, 0:03:12:530 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.event.internal.DefaultLoadEventListener.doOnLoad(DefaultLoadEventListener.java:108)
[7/5/25, 0:03:12:530 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.event.internal.DefaultLoadEventListener.onLoad(DefaultLoadEventListener.java:74)
[7/5/25, 0:03:12:531 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:118)
[7/5/25, 0:03:12:531 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.internal.SessionImpl.fireLoadNoChecks(SessionImpl.java:1231)
[7/5/25, 0:03:12:531 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.internal.SessionImpl.internalLoad(SessionImpl.java:1096)
[7/5/25, 0:03:12:531 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.type.EntityType.resolveIdentifier(EntityType.java:706)
[7/5/25, 0:03:12:531 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.type.EntityType.resolve(EntityType.java:465)
[7/5/25, 0:03:12:531 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.type.ManyToOneType.resolve(	.java:265)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.engine.internal.TwoPhaseLoad$EntityResolver.lambda$static$0(TwoPhaseLoad.java:576)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.engine.internal.TwoPhaseLoad.initializeEntityEntryLoadedState(TwoPhaseLoad.java:221)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.engine.internal.TwoPhaseLoad.initializeEntity(TwoPhaseLoad.java:155)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.engine.internal.TwoPhaseLoad.initializeEntity(TwoPhaseLoad.java:126)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.initializeEntitiesAndCollections(Loader.java:1201)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.processResultSet(Loader.java:1009)
[7/5/25, 0:03:12:532 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.doQuery(Loader.java:967)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:357)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.doList(Loader.java:2868)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.doList(Loader.java:2850)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2682)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.Loader.list(Loader.java:2677)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.loader.hql.QueryLoader.list(QueryLoader.java:540)
[7/5/25, 0:03:12:533 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.hql.internal.ast.QueryTranslatorImpl.list(QueryTranslatorImpl.java:400)
[7/5/25, 0:03:12:534 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.engine.query.spi.HQLQueryPlan.performList(HQLQueryPlan.java:218)
[7/5/25, 0:03:12:534 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.internal.SessionImpl.list(SessionImpl.java:1459)
[7/5/25, 0:03:12:534 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.query.internal.AbstractProducedQuery.doList(AbstractProducedQuery.java:1649)
[7/5/25, 0:03:12:534 EDT] 0000003e SystemErr                                                    R 	at org.hibernate.query.internal.AbstractProducedQuery.list(AbstractProducedQuery.java:1617)
[7/5/25, 0:03:12:534 EDT] 0000003e SystemErr                                                    R 	at org.springframework.orm.hibernate5.HibernateTemplate.lambda$find$30(HibernateTemplate.java:880)
[7/5/25, 0:03:12:534 EDT] 0000003e SystemErr                                                    R 	at org.springframework.orm.hibernate5.HibernateTemplate.doExecute(HibernateTemplate.java:367)
[7/5/25, 0:03:12:535 EDT] 0000003e SystemErr                                                    R 	at org.springframework.orm.hibernate5.HibernateTemplate.executeWithNativeSession(HibernateTemplate.java:334)
[7/5/25, 0:03:12:535 EDT] 0000003e SystemErr                                                    R 	at org.springframework.orm.hibernate5.HibernateTemplate.find(HibernateTemplate.java:872)
[7/5/25, 0:03:12:535 EDT] 0000003e SystemErr                                                    R 	at com.assurant.inc.sox.dao.ar.impl.RejectedUserDAO.findByValue(RejectedUserDAO.java:149)
[7/5/25, 0:03:12:535 EDT] 0000003e SystemErr                                                    R 	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
[7/5/25, 0:03:12:535 EDT] 0000003e SystemErr                                                    R 	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)


public List<RejectedUser> findByValue(String srcUserId, Long extrctSysId,
			String selectedDateCriteria, Date createdDateCriteria) {
		StringBuilder sb = new StringBuilder(256);
		sb.append(" SELECT r FROM RejectedUser as r ");
		sb.append(" WHERE r.deleteFlag <> 'Y'  ");
		sb.append(" AND r.extractSystem <> 9  ");
		if (!"".equals(srcUserId) && srcUserId != null) {
			sb.append(" AND r.srcUserId like '%" + srcUserId + "%' ");
		}
		if (extrctSysId != null && extrctSysId != -1) {
			sb.append(" AND r.extractSystem.extrctSysId = " + extrctSysId );
		}
		if (createdDateCriteria != null) {

			if ("gt".equals(selectedDateCriteria)) {
				sb.append(" AND trunc(r.createdDate) > :createdDate" );
			}
			if ("lt".equals(selectedDateCriteria)) {
				sb.append(" AND trunc(r.createdDate) < :createdDate" );
			}
			if ("eq".equals(selectedDateCriteria)) {
				sb.append(" AND trunc(r.createdDate) = :createdDate" );
			}
		}
		// to get the first occurrence this reject came in
		sb.append(" AND r.rejectedUserId in  ");
		sb.append(" (SELECT MIN (u.rejectedUserId) ");
		sb.append(" FROM RejectedUser as u GROUP BY u.srcUserId, u.extractSystem) ");
		sb.append(" ORDER BY r.createdDate DESC ");
        Object[] values   = { createdDateCriteria};
		String[] paramNames = {"createdDate"};


		if (createdDateCriteria != null) {
			return (List<RejectedUser>) this.hibernateTemplate.execute(
					(org.springframework.orm.hibernate5.HibernateCallback<List<RejectedUser>>) session -> {
						org.hibernate.Query query = session.createQuery(sb.toString());
						if (createdDateCriteria != null) {
							query.setParameter("r.createdDate", createdDateCriteria);
						}
						query.setMaxResults(100);
						return query.list();
					}
			);
		}else {
			System.out.println("*** without criteria :"+sb.toString());
			return this.hibernateTemplate.execute(
					(org.springframework.orm.hibernate5.HibernateCallback<List<RejectedUser>>) session -> {
						org.hibernate.Query query = session.createQuery(sb.toString());
						query.setMaxResults(100);
						return query.list();
					}
			);
		}
	}
