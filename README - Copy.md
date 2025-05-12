```
package com.assurant.inc.sox.dao.luad.impl;

import com.assurant.inc.sox.consts.DateUtil;
import com.assurant.inc.sox.dao.HibernateBaseSox;
import com.assurant.inc.sox.dao.luad.IUserDao;
import com.assurant.inc.sox.domain.ar.Department;
import com.assurant.inc.sox.domain.ar.Division;
import com.assurant.inc.sox.domain.ar.Review;
import com.assurant.inc.sox.domain.ar.ReviewOwner;
import com.assurant.inc.sox.domain.luad.User;
import com.assurant.inc.sox.domain.luad.UserPk;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.hibernate.criterion.DetachedCriteria;
import org.hibernate.criterion.Disjunction;
import org.hibernate.criterion.MatchMode;
import org.hibernate.criterion.Restrictions;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.orm.hibernate5.HibernateTemplate;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Date;
import java.util.List;

//import org.hibernate.criterion.Disjunction;
//import org.apache.commons.lang.StringUtils;
//import com.assurant.inc.sox.domain.ar.UserStatus;
//import com.assurant.inc.sox.domain.ar.UserType;

/**
 * @author BB68602
 *
 */
@Repository
@Transactional
public class UserDao extends HibernateBaseSox implements IUserDao {


	//private static int MAX_RESULTS = 300;
	private static final String COST_CENTERS_HQL;
	private static final String FIND_USERS_HQL;
	private static final String EFFECTIVE_HQL;

	private static final Logger log = LoggerFactory.getLogger(UserDao.class);

	@Autowired
	private JdbcTemplate jdbcTemplate;

	static{

		EFFECTIVE_HQL = "AND (user.deleteFlag is NULL OR user.deleteFlag <> 'Y') "
				+ "AND user.pk.effectiveFromDate < ?  "
				+ "AND (user.pk.effectiveToDate is NULL OR user.pk.effectiveToDate > ?) ";

		StringBuilder sb = new StringBuilder(512);
		sb.append("SELECT DISTINCT user.costCenter ");
		sb.append("FROM User as user ");
		sb.append("WHERE user.costCenter like ? ");
		sb.append(EFFECTIVE_HQL);
		COST_CENTERS_HQL = sb.toString();

		sb.setLength(0);
		sb.append("FROM User as user ");
		sb.append("WHERE CONCAT(user.lastName, CONCAT(', ', CONCAT(user.firstName,");
		sb.append(" CONCAT(' ', user.middleName)))) like ? ");
		sb.append(EFFECTIVE_HQL);
		FIND_USERS_HQL = sb.toString();

	}


	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findByMgrUserId(java.lang.Long)
     */
	public User findByMgrUserId(Long id) {
		List<User> results = this.findByMgrUserIds(Collections.singletonList(id));
		return (results.size() == 0) ? null : results.get(0);
	}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findByMgrUserIds(java.util.List)
     */
	@SuppressWarnings("unchecked")
	public List<User> findByMgrUserIds(List<Long> ids) {
		int cnt = ids.size();
		if(cnt > 100){
			log.warn("fingByMgrUserIds() -- recieved a large list of user ids to find.  List size: "
					+ cnt);
		}
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.in("userId", ids));
		// ------ CR6 removed addDelete restriction
		//this.addDeleteActiveRestrictions(criteria);
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findByUserId(java.lang.Long)
     */
	public User findByUserId(Long id) {
		List<User> results = this.findByUserIds(Collections.singletonList(id));
		return (results.size() == 0) ? null : results.get(0);
	}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findByUserIds(java.util.List)
     */
	@SuppressWarnings("unchecked")
	public List<User> findByUserIds(List<Long> ids) {
		int cnt = ids.size();
		if(cnt > 100){
			log.warn("fingByUserIds() -- recieved a large list of user ids to find.  List size: "
					+ cnt);
		}
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.in("userId", ids));
		this.addDeleteActiveRestrictions(criteria);
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findCostCenters(java.lang.String)
     */
	@SuppressWarnings("unchecked")
	public List<String> findCostCenters(String searchString) {
		Date now = new Date();
		Object[] params = new Object[3];
		params[0] = new StringBuilder(32).append('%').append(searchString).append('%').toString();
		params[1] = now;
		params[2] = now;

		HibernateTemplate template = new HibernateTemplate(this.sessionFactory);
		//template.setMaxResults(MAX_RESULTS);
		return (List<String>) template.find(COST_CENTERS_HQL, params);
	}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findUsersSupervisor(java.lang.Long)
     */
	@SuppressWarnings("unchecked")
	public User findUsersSupervisor(Long userId) {
		System.out.println("MYCARS soxDataAccess UserDao.findUsersSupervisor() --> Start userId: " + userId);
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.eq("userId", userId));
		this.addDeleteActiveRestrictions(criteria);
		List<User> result = (List<User>) this.hibernateTemplate.findByCriteria(criteria);

		if(result.size() == 0){
			return null;
		}

		User user = result.get(0);
		Long supervisorId = user.getSupervisorId();
		if(supervisorId == null){
			return null;
		}

		criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.eq("userId", supervisorId));
		this.addDeleteActiveRestrictions(criteria);
		result = (List<User>) this.hibernateTemplate.findByCriteria(criteria);
		return (result.size() == 0) ? null : result.get(0);
	}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findActiveUsers(java.lang.String)
     */
	@SuppressWarnings("unchecked")
	public List<User> findActiveUsers(String searchName) {

		Date now = new Date();
		Object[] params = new Object[3];
		params[0] = new StringBuilder(32).append('%').append(searchName.toUpperCase()).append('%')
				.toString();
		params[1] = now;
		params[2] = now;

		HibernateTemplate template = new HibernateTemplate(this.sessionFactory);
		//template.setMaxResults(MAX_RESULTS);
		return (List<User>) template.find(FIND_USERS_HQL, params);
	}

	@SuppressWarnings("unchecked")
	public List<User> findActiveUsers() {

		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);

		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	private void addDeleteActiveRestrictions(DetachedCriteria criteria) {
		// delete flag restrictions
		criteria.add(Restrictions.or(Restrictions.ne("deleteFlag", "Y"), Restrictions
				.isNull("deleteFlag")));
		// active to/from restrictions
		Date now = new Date();
		System.out.println("Date for userdao:" + now);
		criteria.add(Restrictions.le("pk.effectiveFromDate", now)).add(
				Restrictions.or(Restrictions.isNull("pk.effectiveToDate"), Restrictions.ge(
						"pk.effectiveToDate", now)));
		}

	/*
     * (non-Javadoc)
     *
     * @see com.assurant.inc.sox.dao.luad.IUserDao#findUsersForSupervisor(java.lang.String)
     */
	@SuppressWarnings("unchecked")
	public List<User> findUsersForSupervisor(Long supervisorId) {
		System.out.println("MYCARS soxDataAccess UserDao.findUsersForSupervisor() --> Start supervisorId: " + supervisorId);
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.eq("supervisorId", supervisorId));
		this.addDeleteActiveRestrictions(criteria);
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	@SuppressWarnings("unchecked")
	public User findByUserIdInactive(Long id) {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.eq("userId", id));

		List<User> result = (List<User>) this.hibernateTemplate.findByCriteria(criteria);

		return (result.size() == 0) ? null : result.get(0);
	}


	//------------------------------AdminConsole---------------------

	@SuppressWarnings("unchecked")
	public List<User> findAll() {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	@SuppressWarnings("unchecked")
	public List<User> findAllActiveUsers() {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	@SuppressWarnings("unchecked")
	public List<User> findByValue(boolean isActive, String searchKeyIdOrAltId,
								  Long searchSupervisor, String searchFirstNm, String searchLastNm,
								  List searchUserType, List searchStatus, List searchDprtmntId,
								  List searchDivisionId, List<String> searchCostCenter,
								  List<String> searchBsnssSgmnt) {
		System.out.println("In find by valve : isActive : " +isActive);
		System.out.println("searchKeyIdOrAltId : "+searchKeyIdOrAltId);
		System.out.println("searchSupervisor :"+ searchSupervisor);
		System.out.println("searchFirstNm :"+ searchFirstNm);
		System.out.println("searchLastNm :"+ searchLastNm);
		System.out.println("searchUserType :"+ searchUserType);
		System.out.println("searchStatus :"+ searchStatus);
		System.out.println("searchDprtmntId :"+ searchDprtmntId);
		System.out.println("searchDivisionId :"+ searchDivisionId);
		System.out.println("searchCostCenter" +searchCostCenter);
		System.out.println("searchBsnssSgmnt :" + searchBsnssSgmnt);

		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		if (isActive)
			this.addDeleteActiveRestrictions(criteria);

		if (!"".equals(searchKeyIdOrAltId) && (searchKeyIdOrAltId!=null)) {

			Disjunction disc = Restrictions.disjunction();
			disc.add(Restrictions.like("pk.keyId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satMfId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satGfId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satCfId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satLcsId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satFdmsId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satSiteminderId", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satAltId1", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satAltId2", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			disc.add(Restrictions.like("satAltId3", searchKeyIdOrAltId, MatchMode.ANYWHERE));
			criteria.add(disc);
		}

		if (searchSupervisor != null && searchSupervisor !=0) {
			criteria.add(Restrictions.eq("supervisorId", searchSupervisor));
			System.out.println("in searchSupervisor equal"+searchSupervisor);
		}
		if (!"".equals(searchFirstNm) && (searchFirstNm!=null)) {
			criteria.add(Restrictions.like("firstName", searchFirstNm,
					MatchMode.ANYWHERE).ignoreCase());
			System.out.println("in searchFirstNm like"+searchFirstNm);
		}
		if (!"".equals(searchLastNm) && (searchLastNm != null)) {
			criteria.add(Restrictions.like("lastName", searchLastNm,
					MatchMode.ANYWHERE).ignoreCase());
			System.out.println("in lastName like"+ searchLastNm);
		}
		if (searchUserType != null && searchUserType.size()>0) {
			if (this.getLongValues(searchUserType).size() >0)
				criteria.add(Restrictions.in("userType.id", this.getLongValues(searchUserType)));
			System.out.println("in searchUserType in long value"+ getLongValues(searchUserType));
		}
		if (searchStatus != null && searchStatus.size()>0) {
			if (this.getLongValues(searchStatus).size() >0)
				criteria.add(Restrictions.in("userStatus.id", this.getLongValues(searchStatus)));
			System.out.println("in searchStatus in long value"+ getLongValues(searchStatus));
		}
		if (searchDprtmntId != null && searchDprtmntId.size()>0) {
			if (this.getLongValues(searchDprtmntId).size() >0)
				criteria.add(Restrictions.in("department.id", this.getLongValues(searchDprtmntId)));
			System.out.println("in searchDprtmntId in long value"+ getLongValues(searchDprtmntId));
		}
		if (searchDivisionId != null && searchDivisionId.size()>0) {
			if (this.getLongValues(searchDivisionId).size() >0)
				criteria.add(Restrictions.in("division.id", this.getLongValues(searchDivisionId)));
			System.out.println("in searchDivisionId in long value"+ getLongValues(searchDivisionId));
		}
		if ( searchCostCenter !=null && searchCostCenter.size()>0)  {
			if (this.removeBlankValues(searchCostCenter).size() >0)
				criteria.add(Restrictions.in("costCenter", this.removeBlankValues(searchCostCenter)));
			System.out.println("in searchCostCenter value"+ removeBlankValues(searchCostCenter));
		}
		if (searchBsnssSgmnt != null && searchBsnssSgmnt.size()>0) {
			if (this.removeBlankValues(searchBsnssSgmnt).size() >0)
				criteria.add(Restrictions.in("businessSegment", this.removeBlankValues(searchBsnssSgmnt)));
			System.out.println("in searchBsnssSgmnt value"+ removeBlankValues(searchBsnssSgmnt));
		}

		List<User> u = (List<User>) this.hibernateTemplate.findByCriteria(criteria);
		log.debug("***Dao List Size is: "+u.size());
		return u;
	}

	@SuppressWarnings("unchecked")
	private List<Long> getLongValues(List valueList){
		List<Long> longValues = new ArrayList<Long>();
		String value;
		for(Object l:valueList){
			value = l.toString();
			if (value!= null && !"".equals(value) )
				longValues.add(new Long(l.toString()));
		}
		return longValues;
	}

	@SuppressWarnings("unchecked")
	private List<String> removeBlankValues(List valueList){
		List<String> nonBlankValues = new ArrayList<String>();
		String value;
		for(Object l:valueList){
			value = l.toString();
			if (value!= null && !"".equals(value) )
				nonBlankValues.add(value);
		}
		return nonBlankValues;
	}

	@SuppressWarnings("unchecked")
	public List<User> findUserExist(String searchKeyId, String satMFId, String satGFId,
									String satCFId, String satLCSId,String satFDMSId, String satSiteminderId,
									String searchSatAltId1,	String searchSatAltId2, String searchSatAltId3) {
		List<User> results = new ArrayList<User>();
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		Disjunction disc = Restrictions.disjunction();
		disc = this.createDisjunction(disc,searchKeyId);
		if(!"".equals(satMFId))
			disc = this.createDisjunction(disc,satMFId);
		if(!"".equals(satGFId))
			disc = this.createDisjunction(disc,satGFId);
		if(!"".equals(satCFId))
			disc = this.createDisjunction(disc,satCFId);
		if(!"".equals(satLCSId))
			disc = this.createDisjunction(disc,satLCSId);
		if(!"".equals(satFDMSId))
			disc = this.createDisjunction(disc,satFDMSId);
		if(!"".equals(satSiteminderId))
			disc = this.createDisjunction(disc,satSiteminderId);
		if(!"".equals(searchSatAltId1))
			disc = this.createDisjunction(disc,searchSatAltId1);
		if(!"".equals(searchSatAltId2))
			disc = this.createDisjunction(disc,searchSatAltId2);
		if(!"".equals(searchSatAltId3))
			disc = this.createDisjunction(disc,searchSatAltId3);

		criteria.add(disc);
		results = (List<User>) this.hibernateTemplate.findByCriteria(criteria);
		return results;
	}

	public Disjunction createDisjunction(Disjunction disc, String searchKeyIdOrAltId){
		disc.add(Restrictions.eq("pk.keyId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satMfId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satGfId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satCfId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satLcsId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satFdmsId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satSiteminderId", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satAltId1", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satAltId2", searchKeyIdOrAltId));
		disc.add(Restrictions.eq("satAltId3", searchKeyIdOrAltId));
		return disc;

	}

	@SuppressWarnings("unchecked")
	public List<User> populateSupevisorData(Long searchSupervisor) {
		List<Long> userTypeList = new ArrayList<Long>();
		userTypeList.add(5L); // for Contractor
		userTypeList.add(8L); // for Employees

		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		criteria.add(Restrictions.eq("supervisorId", searchSupervisor));
		// exclude status of Inactive Retired records
		criteria.add(Restrictions.ne("userStatus.id", 3L ));
//		criteria.add(Restrictions.in("userType.id", userTypeList));
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	//Sahdev@Transactional(readOnly = false)
	public void updateUsers(List<User> persistentInstances) {
		log.debug("Updating user instances");
		try {
			this.hibernateTemplate.saveOrUpdate(persistentInstances);
			log.debug("Update successful");
		} catch (RuntimeException re) {
			log.error("Update failed", re);
			throw re;
		}
	}

	@SuppressWarnings("unchecked")
	public List<User> findByUserName(String searchFirstName, String searchLastName) {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		criteria.add(Restrictions.or(Restrictions.like("firstName", searchFirstName, MatchMode.ANYWHERE)
				, Restrictions.like("lastName", searchLastName, MatchMode.ANYWHERE)));
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

	// Same method exists in ReviewOwnerDao
	@SuppressWarnings("unchecked")
	public List<ReviewOwner> findUserExistInReviewOwner(Long searchUserId) {
		DetachedCriteria criteria = DetachedCriteria
				.forClass(ReviewOwner.class);
		criteria.add(Restrictions.eq("userId", searchUserId));
		return (List<ReviewOwner>) this.hibernateTemplate.findByCriteria(criteria);
		// return (!results.isEmpty());
	}

	@SuppressWarnings("unchecked")
	@Transactional (readOnly = true)
	public List<User> searchAssociatedUser(String searchFirstNm,
										   String searchLastNm, String keyId, Long supervisorId,
										   List userStatuses, List userTypes,
										   List departments, List divisions, String location) {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		// Disjunction disc = Restrictions.disjunction();
		if (!"".equals(searchFirstNm)) {
			criteria.add(Restrictions.like("firstName", searchFirstNm,
					MatchMode.ANYWHERE).ignoreCase());
		}
		if (!"".equals(searchLastNm)) {
			criteria.add(Restrictions.like("lastName", searchLastNm,
					MatchMode.ANYWHERE).ignoreCase());
		}
		if (keyId != null && !"".equals(keyId)) {
			criteria.add(Restrictions.like("pk.keyId", keyId, MatchMode.ANYWHERE).ignoreCase());
		}
		if (supervisorId != null && supervisorId!=0) {
			criteria.add(Restrictions.eq("supervisorId", supervisorId));
		}
		if (userTypes != null && userTypes.size()>0) {
			if (this.getLongValues(userTypes).size() >0)
				criteria.add(Restrictions.in("userType.id", this.getLongValues(userTypes)));
		}
		if (userStatuses != null && userStatuses.size()>0) {
			if (this.getLongValues(userStatuses).size() >0)
				criteria.add(Restrictions.in("userStatus.id", this.getLongValues(userStatuses)));
		}
		if (departments != null && departments.size()>0) {
			if (this.getLongValues(departments).size() >0)
				criteria.add(Restrictions.in("department.id", this.getLongValues(departments)));
		}
		if (divisions != null && divisions.size()>0) {
			if (this.getLongValues(divisions).size() >0)
				criteria.add(Restrictions.in("division.id", this.getLongValues(divisions)));
		}
		// criteria.add(disc);
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}


	public User findByUserKeyId(UserPk userPk) {
		return (User) this.hibernateTemplate.get(User.class, userPk);
	}

	public Long findActiveUserIdByKeyId(String keyId) {
		StringBuilder sb =  new StringBuilder(256);
		Object []params = new Object[ ]{keyId };
		Long userId = null;
		// check for null
		sb.append(" SELECT COUNT(*) FROM SDY025_USER WHERE KEY_ID = ? ");
		sb.append(" AND ACTV_FROM_DT <= SYSDATE ");
		sb.append(" AND ACTV_TO_DT >= SYSDATE ");
		sb.append(" AND (DLT_FLG <> 'Y' OR DLT_FLG IS NOT NULL) ");

		int count = this.jdbcTemplate.queryForObject(sb.toString(), params, Integer.class);

		if(count==1){
			sb.setLength(0);
			sb.append(" SELECT USER_ID FROM SDY025_USER WHERE KEY_ID = ? ");
			sb.append(" AND ACTV_FROM_DT <= SYSDATE ");
			sb.append(" AND ACTV_TO_DT >= SYSDATE ");
			sb.append(" AND (DLT_FLG <> 'Y' OR DLT_FLG IS NOT NULL) ");

			userId = this.jdbcTemplate.queryForObject(sb.toString(), params, Long.class);
		}
		return userId;

	}

	@SuppressWarnings("unchecked")
	public User findActiveUserByUserId(Long userId) {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class).add(
				Restrictions.eq("userId", userId));
		this.addDeleteActiveRestrictions(criteria);
		List<User> result = (List<User>) this.hibernateTemplate.findByCriteria(criteria);
		return (result.size() == 0) ? null : result.get(0);
	}

	@SuppressWarnings("unchecked")
	public List<String> findDistinctCostCenter() {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct upper(user.costCenter) ");
		sb.append("FROM User user ");
		sb.append(" Where user.costCenter is not null ");
		// for active records
		sb.append(EFFECTIVE_HQL);
		sb.append(" order by upper(user.costCenter)");
		Object[] params = { new Date(), new Date()} ;
		return (List<String>) this.hibernateTemplate.find(sb.toString(), params);
	}

	@SuppressWarnings("unchecked")
	public List<String> findDistinctBusinessSegment() {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct upper(user.businessSegment) ");
		sb.append("FROM User user ");
		sb.append(" Where user.businessSegment is not null ");
		// for active records
		sb.append(EFFECTIVE_HQL);
		sb.append(" order by upper(user.businessSegment) ");
		Object[] params = { new Date(), new Date()} ;
		return (List<String>) this.hibernateTemplate.find(sb.toString(), params);
	}

	@SuppressWarnings("unchecked")
	public List<String> findSupDistinctJobTitle(Long supervisorID) {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct user.jobTitle ");
		sb.append("FROM User user ");
		sb.append(" where user.jobTitle is not null");
		sb.append(" and user.supervisorId = ?1");
//		sb.append(" and (user.userType.id =5 OR user.userType.id=8) ");
		sb.append(EFFECTIVE_HQL);
		sb.append(" order by upper(user.jobTitle) ");
		Object[] params = { supervisorID, new Date(), new Date()} ;
		return (List<String>) this.hibernateTemplate.find(sb.toString(), params);
	}

	@SuppressWarnings("unchecked")
	public List<String> findSupDistinctLocation(Long supervisorID) {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct user.location ");
		sb.append("FROM User user ");
		sb.append(" where user.location is not null");
		sb.append(" and user.supervisorId = ? ");
//		sb.append(" and (user.userType.id =5 OR user.userType.id=8) ");
		sb.append(EFFECTIVE_HQL);
		sb.append(" order by upper(user.location) ");
		Object[] params = { supervisorID, new Date(), new Date()} ;
		return (List<String>) this.hibernateTemplate.find(sb.toString(), params);
	}

	@SuppressWarnings("unchecked")
	public List<Department> findSupDistinctDepartments(Long supervisorID) {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct user.department ");
		sb.append("FROM User as user, Department as d ");
		sb.append(" where user.supervisorId = ? ");
		sb.append(" and user.department.id = d.id ");
		sb.append(" and d.activeToDate = ? ");
		sb.append(" and d.deleteFlag <> 'Y' ");
//		sb.append(" and (user.userType.id =5 OR user.userType.id=8) ");
		sb.append(EFFECTIVE_HQL);
		Object[] params = new Object[4];
		params[0] = new Long(supervisorID);
		params[1] = DateUtil.END_OF_TIME;
		params[2] = new Date();
		params[3] = new Date();
		return (List<Department>) this.hibernateTemplate.find(sb.toString(), params);
	}


	@SuppressWarnings("unchecked")
	public List<Division> findSupDistinctDivision(Long supervisorID) {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct user.division ");
		sb.append("FROM User as user, Division as d ");
		sb.append(" where user.supervisorId = ?");
		sb.append(" and user.division.id = d.id ");
		sb.append(" and d.activeToDate = ? ");
		sb.append(" and d.deleteFlag <> 'Y' ");
//		sb.append(" and (user.userType.id =5 OR user.userType.id=8) ");
		sb.append(EFFECTIVE_HQL);
		Object[] params = new Object[4];
		params[0] = new Long(supervisorID);
		params[1] = DateUtil.END_OF_TIME;
		params[2] = new Date();
		params[3] = new Date();
		return (List<Division>) this.hibernateTemplate.find(sb.toString(), params);
	}

	@SuppressWarnings("unchecked")
	public List<String> findSupDistinctCostCenter(Long supervisorID) {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct user.costCenter ");
		sb.append("FROM User user ");
		sb.append(" Where user.costCenter is not null");
		sb.append(" and user.supervisorId = ?0");
//		sb.append(" and (user.userType.id =5 OR user.userType.id=8) ");
		sb.append(EFFECTIVE_HQL);
		sb.append(" order by upper(user.costCenter)");
		Object[] params = { supervisorID, new Date(), new Date()} ;
		return (List<String>) this.hibernateTemplate.find(sb.toString(), params);
	}

	@SuppressWarnings("unchecked")
	public List<String> findSupDistinctBusinessSegment(Long supervisorID) {
		StringBuilder sb = new StringBuilder(512);
		sb.append("select distinct user.businessSegment ");
		sb.append("FROM User user ");
		sb.append(" Where user.businessSegment is not null");
		sb.append(" and user.supervisorId = ?1");
//		sb.append(" and (user.userType.id =5 OR user.userType.id=8) ");
		sb.append(EFFECTIVE_HQL);
		sb.append(" order by upper(user.businessSegment)");
		Object[] params = { supervisorID, new Date(), new Date()} ;
		return (List<String>) this.hibernateTemplate.find(sb.toString(), params);
	}

	//for Add/Insert record in 25 table
	public void save(User user) {
		log.debug("saving User instance");
		try {
			System.out.println("Himanshu testing in UserDao***");
			this.hibernateTemplate.saveOrUpdate(user);
			log.debug("save successful");
		} catch (RuntimeException re) {
			log.error("save failed", re);
			throw re;
		}
	}

	//for update a record in 25 table
	//Sahdev@Transactional(readOnly = false)
	public void updateUser(Long userId, String keyId, String firstNm, String mddlNm, String lastNm,
						   Long userStatusId, String userStatus, Long userTypeId, String userType,
						   Long supervisorId, String supervisorName,
						   Long departmentId, String departmentName, String cstCntr, String bsnssSgmnt,
						   Long divisionId, String divisionName,
						   String jobTitle, String location, String phone, String emailAddress,
						   String satStatus, String satJobRole, String satComment,
						   String satMFId, String satGFId, String satCFId, String satLCSId, String satFDMSId,
						   String satSiteminderId, String satAltId1, String satAltId2, String satAltId3,
						   String lastChangedBy ) {
		try {
			StringBuilder sb =  new StringBuilder(256);
			sb.append(" UPDATE SDY025_USER ");
			sb.append(" SET ACTV_FROM_DT = SYSDATE , ");
			sb.append(" LAST_CHNGD_DT = SYSDATE , ");
			sb.append(" LAST_CHNGD_BY = ?,  ");

			//sb.append(" KEY_ID = ? , ");
			sb.append(" FIRST_NM = ? , ");
			sb.append(" MDDL_NM = ? , ");
			sb.append(" LAST_NM = ? , ");

			sb.append(" USR_STATUS_ID = ? , ");
			sb.append(" STATUS = ? , ");
			sb.append(" USR_TYPE_ID = ? , ");
			sb.append(" USER_TYPE = ? , ");

			sb.append(" SUPERVISOR_ID = ? , ");
			sb.append(" SUPERVISOR = ? , ");

			sb.append(" DPRTMNT_ID = ? , ");
			sb.append(" DEPT_NAME = ? , ");
			sb.append(" CST_CNTR = ? , ");
			sb.append(" BSNSS_SGMNT = ? , ");

			sb.append(" DIVISN_ID = ? , ");
			sb.append(" DIVISN_NAME = ? , ");

			sb.append(" JOB_TITLE = ? , ");
			sb.append(" LOCATION = ? , ");
			sb.append(" PHONE = ? , ");
			sb.append(" EMAIL_ADDR = ? , ");

			sb.append(" SAT_STATUS = ? , ");
			sb.append(" SAT_JOB_ROLE = ? , ");
			sb.append(" SAT_COMMENT = ? , ");

			//set Alternate Id
			sb.append(" SAT_MF_ID = ? , ");
			sb.append(" SAT_GF_ID = ? , ");
			sb.append(" SAT_CF_ID = ? , ");
			sb.append(" SAT_LCS_ID = ? , ");
			sb.append(" SAT_FDMS_ID = ? , ");
			sb.append(" SAT_SITEMINDER_ID = ? , ");
			sb.append(" SAT_ALT_ID1 = ? , ");
			sb.append(" SAT_ALT_ID2 = ? , ");
			sb.append(" SAT_ALT_ID3 = ? ");

			sb.append(" WHERE USER_ID = ? ");

			Object []params = new Object[ ]{
					lastChangedBy,
					firstNm, mddlNm, lastNm,
					userStatusId, userStatus, userTypeId, userType,
					supervisorId, supervisorName,
					departmentId, departmentName, cstCntr, bsnssSgmnt,
					divisionId, divisionName,
					jobTitle, location, phone, emailAddress,
					satStatus, satJobRole, satComment,
					satMFId, satGFId, satCFId, satLCSId, satFDMSId,
					satSiteminderId, satAltId1, satAltId2, satAltId3,
					userId
			};

			this.jdbcTemplate.update(sb.toString(), params);

			log.debug("Update successful");
		} catch (RuntimeException re) {
			log.error("Update failed", re);
			throw re;
		}
	}

	//for update a record in 25 table
	//Sahdev@Transactional(readOnly = false)
	public void updateRejectedUser(Long userId, String firstNm, String mddlNm, String lastNm,
								   Long userStatusId, String userStatus, Long userTypeId, String userType,
								   Long supervisorId, String supervisorName,
								   Long departmentId, String departmentName, String cstCntr, String bsnssSgmnt,
								   Long divisionId, String divisionName,
								   String jobTitle, String location, String phone, String emailAddress,
								   String satStatus, String satJobRole, String satComment,
								   String lastChangedBy ) {
		try {

			StringBuilder sb =  new StringBuilder(256);
			sb.append(" UPDATE SDY025_USER ");
			sb.append(" SET ACTV_FROM_DT = SYSDATE , ");
			sb.append(" LAST_CHNGD_DT = SYSDATE , ");
			sb.append(" LAST_CHNGD_BY = ?,  ");

			//sb.append(" KEY_ID = ? , ");
			sb.append(" FIRST_NM = ? , ");
			sb.append(" MDDL_NM = ? , ");
			sb.append(" LAST_NM = ? , ");

			sb.append(" USR_STATUS_ID = ? , ");
			sb.append(" STATUS = ? , ");
			sb.append(" USR_TYPE_ID = ? , ");
			sb.append(" USER_TYPE = ? , ");

			sb.append(" SUPERVISOR_ID = ? , ");
			sb.append(" SUPERVISOR = ? , ");

			sb.append(" DPRTMNT_ID = ? , ");
			sb.append(" DEPT_NAME = ? , ");
			sb.append(" CST_CNTR = ? , ");
			sb.append(" BSNSS_SGMNT = ? , ");

			sb.append(" DIVISN_ID = ? , ");
			sb.append(" DIVISN_NAME = ? , ");

			sb.append(" JOB_TITLE = ? , ");
			sb.append(" LOCATION = ? , ");
			sb.append(" PHONE = ? , ");
			sb.append(" EMAIL_ADDR = ? , ");

			sb.append(" SAT_STATUS = ? , ");
			sb.append(" SAT_JOB_ROLE = ? , ");
			sb.append(" SAT_COMMENT = ? , ");

			sb.append(" WHERE USER_ID = ? ");

			Object []params = new Object[ ]{
					lastChangedBy,
					firstNm, mddlNm, lastNm,
					userStatusId, userStatus, userTypeId, userType,
					supervisorId, supervisorName,
					departmentId, departmentName, cstCntr, bsnssSgmnt,
					divisionId, divisionName,
					jobTitle, location, phone, emailAddress,
					satStatus, satJobRole, satComment,
					userId
			};

			this.jdbcTemplate.update(sb.toString(), params);

			log.debug("Update successful");
		} catch (RuntimeException re) {
			log.error("Update failed", re);
			throw re;
		}
	}

	//update Alternate Ids in 25 table
	//Sahdev@Transactional(readOnly = false)
	public void updateAltIds(Long userId, String lastChangedBy,
							 String satMFId, String satGFId, String satCFId, String satLCSId,
							 String satFDMSId, String satSiteminderId, String satAltId1,
							 String satAltId2, String satAltId3) {
		StringBuilder sb =  new StringBuilder(256);
		sb.append(" UPDATE SDY025_USER ");
		sb.append(" SET ACTV_FROM_DT = SYSDATE , ");
		sb.append(" LAST_CHNGD_DT = SYSDATE , ");
		sb.append(" LAST_CHNGD_BY = ?,  ");
		sb.append(" SAT_MF_ID = ? , ");
		sb.append(" SAT_GF_ID = ? , ");
		sb.append(" SAT_CF_ID = ? , ");
		sb.append(" SAT_LCS_ID = ? , ");
		sb.append(" SAT_FDMS_ID = ? , ");
		sb.append(" SAT_SITEMINDER_ID = ? , ");
		sb.append(" SAT_ALT_ID1 = ? , ");
		sb.append(" SAT_ALT_ID2 = ? , ");
		sb.append(" SAT_ALT_ID3 = ? ");
		sb.append(" WHERE USER_ID = ? ");
		Object []params = new Object[ ]{lastChangedBy,
				satMFId, satGFId, satCFId, satLCSId, satFDMSId,
				satSiteminderId, satAltId1, satAltId2, satAltId3,
				userId };

		this.jdbcTemplate.update(sb.toString(), params);
		}

	// logically delete a user from 25 table
	//Sahdev@Transactional(readOnly = false)
	public void markAsDelete(Long userId, String deletedBy) {
		StringBuilder sb =  new StringBuilder(256);
		sb.append(" UPDATE SDY025_USER ");
		sb.append(" SET ACTV_FROM_DT = SYSDATE , ");
		sb.append(" ACTV_TO_DT = SYSDATE , ");
		sb.append(" LAST_CHNGD_DT = SYSDATE , ");
		sb.append(" LAST_CHNGD_BY = ?,  ");
		sb.append(" DLT_FLG = 'Y' ");
		sb.append(" WHERE USER_ID = ? ");
		Object []params = new Object[ ]{deletedBy, userId };

		this.jdbcTemplate.update(sb.toString(), params);
		}

	//Sahdev@Transactional(readOnly = false)
	public void updateSupervisorOnUserDelete(Long userId, String lastChangedBy) {
		StringBuilder sb =  new StringBuilder(256);
		sb.append(" UPDATE SDY025_USER ");
		sb.append(" SET ACTV_FROM_DT = SYSDATE , ");
		sb.append(" LAST_CHNGD_DT = SYSDATE , ");
		sb.append(" LAST_CHNGD_BY = ?,  ");
		sb.append(" SUPERVISOR_ID = -1 , ");
		sb.append(" SUPERVISOR = '' ");
		sb.append(" WHERE USER_ID = ? ");
		Object []params = new Object[ ]{lastChangedBy, userId };

		this.jdbcTemplate.update(sb.toString(), params);
		}

	public boolean isActiveReviewOwner(Long userId) {
		StringBuilder sb =  new StringBuilder(256);
		sb.append(" SELECT COUNT(*) FROM SDY235_REVIEW_OWNR ");
		sb.append(" WHERE USER_ID = ? ");
		sb.append(" AND ACTV_FROM_DT <= SYSDATE ");
		sb.append(" AND ACTV_TO_DT > SYSDATE ");
		sb.append(" AND (DLT_FLG <> 'Y'  OR DLT_FLG IS  NULL) ");
		Object []params = new Object[ ]{userId };
		int i = this.jdbcTemplate.queryForObject(sb.toString(), params, Integer.class);
		return ((i>0)?true:false);

	}

	@SuppressWarnings("unchecked")
	public List<Review> outstandingReviewsForUserId(Long userId) {
		StringBuilder sb =  new StringBuilder(256);
		sb.append(" SELECT DISTINCT RVW  ");
		sb.append(" FROM ReviewBundle BNDL, Review RVW, Reviewer RVWR ");
		sb.append(" WHERE ");
		sb.append(" RVWR.userId = ? ");
		sb.append(" AND RVW.id = BNDL.reviewId ");
		sb.append(" AND RVWR.reviewBundleId = BNDL.id ");
		sb.append(" AND RVW.deleteFlag <> 'Y' ");
		sb.append(" AND BNDL.deleteFlag <> 'Y' ");
		sb.append(" AND BNDL.bundleStatusCode IN ('APPR', 'PEND') ");

		Object []params = new Object[ ]{userId };
		List<Review> results = (List<Review>) this.hibernateTemplate.find(sb.toString(), params);
		return (results.size() == 0) ? null : results;
	}

	public boolean isActiveUser(Long userId) {
		StringBuilder sb =  new StringBuilder(256);
		sb.append(" SELECT COUNT(*) FROM SDY025_USER ");
		sb.append(" WHERE USER_ID = ? ");
		sb.append(" AND ACTV_FROM_DT <= SYSDATE ");
		sb.append(" AND ACTV_TO_DT > SYSDATE ");
		sb.append(" AND (DLT_FLG <> 'Y'  OR DLT_FLG IS  NULL) ");
		Object []params = new Object[ ]{userId };
		int i = this.jdbcTemplate.queryForObject(sb.toString(), params, Integer.class);
		return ((i>0)?true:false);

	}

	@SuppressWarnings("unchecked")
	public List<User> findPotentialSupervisors(String keyId, String firstName, String lastName) {
		DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
		this.addDeleteActiveRestrictions(criteria);
		criteria.add(Restrictions.in("userTypeDescription", new Object[] {"EMPLOYEE",
				"INTERNATNL", "INTERNATIONAL EMPLOYEE"}));

		if (keyId != null) {
			criteria.add(Restrictions.like("pk.keyId", keyId, MatchMode.ANYWHERE));
		}

		if (firstName != null) {
			criteria.add(Restrictions.like("firstName", firstName, MatchMode.ANYWHERE));
		}

		if (lastName != null) {
			criteria.add(Restrictions.like("lastName", lastName, MatchMode.ANYWHERE));
		}
		return (List<User>) this.hibernateTemplate.findByCriteria(criteria);
	}

}
