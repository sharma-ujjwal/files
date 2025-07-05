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


package com.assurant.inc.sox.dao.ar.impl;

//default package

import com.assurant.inc.sox.dao.HibernateBaseSox;
import com.assurant.inc.sox.dao.ar.IRejectedUserDAO;
import com.assurant.inc.sox.domain.ar.RejectedUser;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.hibernate.criterion.DetachedCriteria;
import org.hibernate.criterion.Restrictions;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;

import java.util.Date;
import java.util.List;

/**
 * A data access object (DAO) providing persistence and search support for
 * RejectedUser entities. Transaction control of the save(), update() and
 * delete() operations can directly support Spring container-managed
 * transactions or they can be augmented to handle user-managed Spring
 * transactions. Each of these methods provides additional information for how
 * to configure it for the desired type of transaction control.
 * 
 * @see .RejectedUser
 * @author MyEclipse Persistence Tools
 */
@Repository
@Transactional
public class RejectedUserDAO extends HibernateBaseSox implements
		IRejectedUserDAO {

    @Autowired
	private JdbcTemplate jdbcTemplate;

    private static final Log log = LogFactory.getLog(RejectedUserDAO.class);
/*
	public static final String SRC_PROCESS_NAME = "srcProcessName";
	public static final String SRC_USER_ID = "srcUserId";
	public static final String SRC_USER_NAME = "srcUserName";
	public static final String SRC_APPLICATION_NAME = "srcApplicationName";
	public static final String SRC_EMAIL_ADDRESS = "srcEmailAddress";
	public static final String ORA_INSTNC_NM = "oraInstncNm";
	public static final String LDAP_OBJ_NM = "ldapObjNm";
	public static final String COMMENT_TEXT = "commentText";
	public static final String CREATED_BY = "createdBy";
	public static final String LAST_CHANGED_BY = "lastChangedBy";
	public static final String DELETE_FLAG = "deleteFlag";
*/
	//Sahdev@Transactional(readOnly = false)
	@Transactional
	public void save(RejectedUser transientInstance) {
		log.debug("saving RejectedUser instance");
		try {
			this.hibernateTemplate.saveOrUpdate(transientInstance);
			log.debug("save successful");
		} catch (RuntimeException re) {
			log.error("save failed", re);
			throw re;
		}
	}

	//Sahdev@Transactional(readOnly = false)
	@Transactional
	public void delete(RejectedUser persistentInstance) {
		log.debug("deleting RejectedUser instance");
		try {
			this.hibernateTemplate.delete(persistentInstance);
			log.debug("delete successful");
		} catch (RuntimeException re) {
			log.error("delete failed", re);
			throw re;
		}
	}

	@SuppressWarnings("unchecked")
	@Transactional
	public List<RejectedUser> findAll() {
		log.debug("finding all RejectedUser instances");
		try {

			// to get the first occurrence this reject came in
			StringBuilder sb = new StringBuilder(256);
			 sb.append(" SELECT r FROM RejectedUser as r ");
			 sb.append(" WHERE r.deleteFlag <> 'Y'  ");
			 sb.append(" AND r.extractSystem <> 9  ");
			 sb.append(" AND r.rejectedUserId in  ");
			 sb.append(" (SELECT MIN (u.rejectedUserId) ");
			 sb.append(" FROM RejectedUser as u GROUP BY u.srcUserId, u.extractSystem) ");
			 sb.append(" ORDER BY r.createdDate DESC ");
			 
			 return (List<RejectedUser>) this.hibernateTemplate.find(sb.toString());
/*
			 SELECT * FROM SDY199_RJCTD_USR
			  WHERE RJCTD_USR_ID IN 
			   (SELECT MAX (RJCTD_USR_ID) RJCTD_USR_ID 
			   FROM SDY199_RJCTD_USR GROUP BY SRC_USR_ID)
			ORDER BY CRTD_DT DESC
*/
		} catch (RuntimeException re) {
			log.error("find all failed", re);
			throw re;
		}
	}

	@SuppressWarnings("unchecked")
	@Transactional
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
							query.setParameter("createdDate", createdDateCriteria);
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

	@SuppressWarnings("unchecked")
	@Transactional
	public RejectedUser findByRejectedUserId(Long rejectedUserId) {
		log.debug("findby rejecteduser id");
		try {
			 DetachedCriteria criteria = DetachedCriteria.forClass(RejectedUser.class);
			 criteria.add(Restrictions.eq("rejectedUserId", rejectedUserId));
			 List<RejectedUser> results = (List<RejectedUser>) this.hibernateTemplate.findByCriteria(criteria);
			 return results.get(0);

		} catch (RuntimeException re) {
			log.error("find by id failed", re);
			throw re;
		}
	}

	@SuppressWarnings("unchecked")
	@Transactional
	public List<RejectedUser> findBySrcUserId(String srcUserId) {
		log.debug("findby srcUserId");
		try {
			 DetachedCriteria criteria = DetachedCriteria.forClass(RejectedUser.class);
			 criteria.add(Restrictions.eq("srcUserId", srcUserId));
			  return (List<RejectedUser>) this.hibernateTemplate.findByCriteria(criteria);

		} catch (RuntimeException re) {
			log.error("find by id failed", re);
			throw re;
		}
	}

    //Sahdev@Transactional(readOnly = false)
	@Transactional
    public void reconcileRejects(String lastChangedBy){
		StringBuilder sb =  new StringBuilder(256);
    	sb.append(" UPDATE SDY199_RJCTD_USR R ");
    	sb.append(" SET R.DLT_FLG = 'Y', R.LST_CHNGD_DT = SYSDATE, ");
    	sb.append(" R.LST_CHNGD_BY = ?   ");
    	sb.append(" WHERE R.SRC_USR_ID IN ");
    	sb.append(" (  ");
    	sb.append(" SELECT REJECTED.SRC_USR_ID ");
    	sb.append(" FROM SDY199_RJCTD_USR REJECTED, SDY025_USER U ");
    	sb.append(" WHERE  ");
    	sb.append(" REJECTED.SYSTM_ID <> 9");
    	sb.append(" AND   ");
    	sb.append(" REJECTED.DLT_FLG <> 'Y' ");
    	sb.append(" AND ");
    	sb.append(" ( U.DLT_FLG <> 'Y' AND U.ACTV_TO_DT > SYSDATE ) ");
    	sb.append(" AND ");
    	sb.append(" ( ");
    	sb.append("     U.KEY_ID = REJECTED.SRC_USR_ID ");
    	sb.append(" OR U.EMAIL_ADDR = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_ALT_ID1 = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_ALT_ID2 = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_ALT_ID3 = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_CF_ID = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_FDMS_ID = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_GF_ID = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_LCS_ID = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_MF_ID = REJECTED.SRC_USR_ID ");
	    sb.append(" OR U.SAT_SITEMINDER_ID = REJECTED.SRC_USR_ID ");
	    sb.append(" ) ");
    	sb.append(" ) ");

   	
		Object []params = new Object[ ]{ lastChangedBy };
this.jdbcTemplate.update(sb.toString(), params);
    }
}
